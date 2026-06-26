---
name: gitlab-mr-review
description: |
  GitLab Merge Request code review skill. Fetches MR details and diffs via GitLab API,
  performs comprehensive code review, and analyzes potential issues before/after changes.

  Use this skill when the user wants to:
  - Review a GitLab merge request
  - Check code changes in an MR
  - Analyze potential issues in MR diffs
  - Do code review on a GitLab MR link
  - Get a second opinion on merge request changes

  Trigger keywords: review mr, code review, merge request, gitlab mr, mr review, 审查, 代码审查, mr链接, merge链接
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
  - Agent
---

# GitLab Merge Request Code Review

Perform comprehensive code review on GitLab Merge Requests by fetching MR data via the GitLab API.

## CRITICAL RULES

1. **Always use curl with `-sk` flag** for self-hosted GitLab instances (they often use self-signed certs).
2. **Use Node.js for JSON parsing** (not Python — many Windows machines don't have Python installed).
3. **Never store tokens in files.** Only use tokens provided in the current session.
4. **Always read the full diff output** — never summarize from partial data.
5. **Always report results after completion** — present the full review to the user.

## Workflow

### Step 1: Parse the MR URL

Extract project path and MR ID from the GitLab MR URL.

URL patterns:
```
https://{host}/{group}/{project}/-/merge_requests/{id}
https://{host}/{group}/{subgroup}/{project}/-/merge_requests/{id}/diffs
```

Extract:
- `host`: the GitLab domain (e.g., `gitlab.ainfinit.com`)
- `project_path`: URL-encoded project path (e.g., `front%2Fphantom-ui-ops`)
- `mr_id`: the MR number (e.g., `40`)

### Step 2: Get the GitLab Token

If no token is available in the environment (`GITLAB_TOKEN`, `GITLAB_PRIVATE_TOKEN`, `GL_TOKEN`), ask the user for their GitLab Personal Access Token (needs `read_api` scope).

### Step 3: Pull Latest Code

Locate the local project directory (default base: `D:/code/gitlab/{project-name}`, where `project-name` is the last segment of the GitLab project path). Then pull the latest code for both the target and source branches so that local codebase analysis is based on up-to-date code.

```bash
# 1. Enter the project directory
cd {local_project_path}

# 2. Fetch all remote updates
git fetch --all

# 3. Checkout the target branch and pull latest
git checkout {target_branch}
git pull origin {target_branch}

# 4. Checkout the source branch and pull latest
git checkout {source_branch}
git pull origin {source_branch}
```

**Important notes:**
- If the local directory doesn't exist or is not a git repo, skip this step and rely solely on the API diff data.
- If `git pull` fails due to uncommitted changes, warn the user and ask them to stash or commit first. Do NOT run `git stash` or `git reset` without explicit user approval.
- After pulling, stay on the **source branch** so that subsequent file reads reflect the MR's changed code.
- Run `git fetch --all` first to ensure remote branch info is up to date, even if the branch hasn't been checked out locally before.

### Step 4: Fetch MR Info

```bash
curl -sk --header "PRIVATE-TOKEN: {token}" \
  "https://{host}/api/v4/projects/{project_path}/merge_requests/{mr_id}" \
  2>/dev/null | node -e "
    let d='';
    process.stdin.on('data',c=>d+=c);
    process.stdin.on('end',()=>{
      try {
        let j=JSON.parse(d);
        if(j.message) { console.log('ERROR:', j.message); return; }
        console.log('Title:', j.title);
        console.log('URL:', j.web_url);
        console.log('Source:', j.source_branch, '-> Target:', j.target_branch);
        console.log('State:', j.state);
        console.log('Author:', j.author?.name);
        console.log('Description:', (j.description||'').slice(0,1000));
      } catch(e) { console.log('Parse error:', d.slice(0,500)); }
    });
  "
```

### Step 5: Fetch MR Diffs

Save the full diff output to a temporary file for reading:

```bash
curl -sk --header "PRIVATE-TOKEN: {token}" \
  "https://{host}/api/v4/projects/{project_path}/merge_requests/{mr_id}/changes" \
  2>/dev/null > /tmp/mr_diff.json

node -e "
  const fs = require('fs');
  const d = JSON.parse(fs.readFileSync('/tmp/mr_diff.json','utf8'));
  const changes = d.changes || [];
  console.log('Files changed:', changes.length);
  for (const c of changes) {
    console.log('==========');
    console.log('File:', c.new_path);
    console.log('New:', c.new_file, '| Deleted:', c.deleted_file, '| Renamed:', c.renamed_file);
    console.log('--- DIFF ---');
    console.log(c.diff || '(no diff)');
  }
" > /tmp/mr_diff_output.txt
```

### Step 6: Read and Analyze the Diff

Use the Read tool to read the full diff output file. If it's large (over 2000 lines), read in chunks.

### Step 7: (Optional) Explore the Local Codebase

If the project exists locally (check `D:/code/gitlab/{project-name}` or similar), read the **full original files** that were modified to understand surrounding context. This helps identify:
- Whether the changed function is called elsewhere
- Whether imports were updated in all consumers
- Whether related components are consistent

### Step 8: Produce the Review

Output a structured review in the following format:

```markdown
## MR 概要

| 项目 | 内容 |
|------|------|
| **标题** | {title} |
| **链接** | [查看 MR]({mr_web_url}) |
| **分支** | `{source}` -> `{target}` |
| **作者** | {author} |
| **文件** | {count} 个（{new} 个新增，{modified} 个修改，{deleted} 个删除） |

**核心改动：** {one paragraph summary}

---

## 一、代码问题

For each issue found, use this format:

### {severity_icon} [{severity}] {title}

**文件：** `{file_path}` line {line_number}

\`\`\`{language}
{problematic code}
\`\`\`

{explanation of the problem and suggested fix}

---

## 二、改动前后可能引发的问题

For each potential issue:

### {severity_icon} [{severity}] {title}

{detailed analysis of the risk, what scenarios could trigger it, and recommended mitigations}

---

## 总结

| 级别 | 数量 | 关键项 |
|------|------|--------|
| 严重 | {n} | {brief} |
| 高 | {n} | {brief} |
| 中 | {n} | {brief} |
| 低 | {n} | {brief} |

**建议合并前确认：**
1. {actionable item}
2. {actionable item}
3. {actionable item}
```

## Severity Levels

| Level | Icon | When to use |
|-------|------|-------------|
| 严重 | [严重] | Will cause runtime errors, data loss, or security vulnerabilities |
| 高 | [高] | Likely to cause bugs in specific scenarios, missing critical config |
| 中 | [中] | Code quality issues, potential edge cases, maintainability concerns |
| 低 | [低] | Style inconsistencies, minor optimizations, nice-to-haves |

## Review Checklist

Always check for these categories:

### Correctness
- Logic errors, off-by-one, null/undefined handling
- API contract changes (method, params, response format)
- Missing error handling on async operations
- Race conditions in async/event-driven code

### Compatibility
- Breaking changes to shared APIs or interfaces
- Missing updates to all consumers when an API changes
- Version/dependency compatibility
- Route/navigation configuration completeness

### Security
- User input sanitization (XSS, injection)
- Sensitive data exposure in logs or responses
- Authentication/authorization gaps

### Performance
- Unnecessary re-renders (Vue/React)
- Redundant API calls
- Missing pagination or limits on large data sets
- Memory leaks (event listeners, timers not cleaned up)

### Code Quality
- Dead code or unused variables/imports
- DRY violations (duplicated logic across files)
- Inconsistent naming or patterns
- Typos in identifiers or strings

### Vue/React Specific
- Prop type mismatches or missing defaults
- `.sync` modifier usage correctness
- Component lifecycle issues (data fetching timing)
- Computed properties with side effects
- Missing key in v-for / map

## Tips

- When reviewing Vue projects, pay special attention to parent-child component communication patterns (.sync, $emit, refs).
- When an API method changes (GET to POST, param structure), always trace all callers to check they've been updated.
- When code is refactored into a new component, verify the original page's remaining code has no dead references.
- For batch/bulk operations, check input validation (max count, empty input, special characters).
- Always check if router configuration files were updated when new pages are added.
