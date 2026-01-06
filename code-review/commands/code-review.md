---
allowed-tools: Bash(git diff:*), Bash(git log:*), Bash(git show:*), Bash(git branch:*), Bash(git rev-parse:*), Bash(cat:*), Bash(find:*)
description: Code review local branch changes
---

Provide a code review for the current local branch compared to a base branch.

To do this, follow these steps precisely:

1. Determine the base branch and current branch:
   - Get the current branch name using `git branch --show-current`
   - Use the provided base branch argument, or default to `main` (fallback to `master` if `main` doesn't exist)
   - Get the diff using `git diff <base>...<current>` 
   - Get the list of changed files using `git diff --name-only <base>...<current>`

   If there are no changes, stop and report "No changes detected between current branch and base branch."

2. Launch a haiku agent to return a list of file paths (not their contents) for all relevant CLAUDE.md files including:
   - The root CLAUDE.md file, if it exists
   - Any CLAUDE.md files in directories containing files modified in the diff

3. Launch a sonnet agent to view the diff and return a summary of the changes. Include:
   - The current branch name
   - The base branch being compared against
   - A summary of what the changes do

4. Launch 4 agents in parallel to independently review the changes. Each agent should return the list of issues, where each issue includes:
   - File path
   - Line number(s) in the new version
   - Description of the issue
   - Reason it was flagged (e.g. "CLAUDE.md adherence", "bug")
   
   The agents should do the following:

   Agents 1 + 2: CLAUDE.md compliance sonnet agents
   Audit changes for CLAUDE.md compliance in parallel. Note: When evaluating CLAUDE.md compliance for a file, you should only consider CLAUDE.md files that share a file path with the file or parents.

   Agent 3: Opus bug agent (parallel subagent with agent 4)
   Scan for obvious bugs. Focus only on the diff itself without reading extra context. Flag only significant bugs; ignore nitpicks and likely false positives. Do not flag issues that you cannot validate without looking at context outside of the git diff.

   Agent 4: Opus bug agent (parallel subagent with agent 3)
   Look for problems that exist in the introduced code. This could be security issues, incorrect logic, etc. Only look for issues that fall within the changed code.

   **CRITICAL: We only want HIGH SIGNAL issues.** This means:
   - Objective bugs that will cause incorrect behavior at runtime
   - Clear, unambiguous CLAUDE.md violations where you can quote the exact rule being broken

   We do NOT want:
   - Subjective concerns or "suggestions"
   - Style preferences not explicitly required by CLAUDE.md
   - Potential issues that "might" be problems
   - Anything requiring interpretation or judgment calls

   If you are not certain an issue is real, do not flag it. False positives erode trust and waste reviewer time.

   In addition to the above, each subagent should be told the branch name and commit messages from the branch. This will help provide context regarding the author's intent. Get commit messages using `git log <base>..<current> --oneline`.

5. For each issue found in the previous step by agents 3 and 4, launch parallel subagents to validate the issue. These subagents should get the branch context (name and commit messages) along with a description of the issue. The agent's job is to review the issue to validate that the stated issue is truly an issue with high confidence. For example, if an issue such as "variable is not defined" was flagged, the subagent's job would be to validate that is actually true in the code. Another example would be CLAUDE.md issues. The agent should validate that the CLAUDE.md rule that was violated is scoped for this file and is actually violated. Use Opus subagents for bugs and logic issues, and sonnet agents for CLAUDE.md violations.

6. Filter out any issues that were not validated in step 5. This step will give us our list of high signal issues for our review.

7. Output the review results:

   If NO issues were found, output:
```
   ## Local Code Review

   ✅ No issues found. Checked for bugs and CLAUDE.md compliance.

   Branch: <current-branch>
   Compared against: <base-branch>
   Files reviewed: <count>
```

8. If issues WERE found, output a formatted review report:
