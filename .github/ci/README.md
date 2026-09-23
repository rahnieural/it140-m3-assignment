<!-- To see this file in a clean, formatted view, select ▼ in the upper-right corner of the editor pane, then select "Markdown Preview". -->

# IT 140 Module Three Assignment | GitHub Continuous Integration Guide

This guide explains the GitHub Actions checks used in the Module Three assignment repository.

> [!IMPORTANT]
> **GitHub continuous integration (CI) provides feedback. It does not grade or submit the assignment.** Students submit the required files in **D2L Brightspace**, and the D2L assignment rubric determines the grade.

## About CI

### What CI Means Here

**Continuous integration (CI)** is automated checking that runs after repository changes are saved to GitHub. GitHub Actions provides the CI for this repository.

The Module Three repository separates two purposes:

* **Student CI** gives formative feedback about the current state of the two graded design artifacts and protected repository files.
* **Course-repository CI** protects the public assignment template, documentation, starter artifacts, command instructions, and supporting files.

### Important Terms

| Term | Meaning |
| --- | --- |
| **course repository** | `GC-STEM/it140-m3-assignment`, the public starter repository maintained for the course |
| **personal repository** | The private repository a student creates from the course template |
| **workflow** | Instructions in `.github/workflows/` that tell GitHub Actions what to run |
| **workflow run** | One execution of a workflow |
| **Assignment artifact check** | The student-facing job that checks repository integrity and the graded design-file state |
| **Course repository check** | The maintainer-facing job that validates the public assignment package |
| **README command checks** | Cross-platform checks for Bash command blocks in README files |

## Student CI

### What the Assignment Artifact Check Covers

The student-facing **Assignment artifact check** focuses on the Module Three repository and the two graded design files:

* `design/paycheck_calculator.drawio`
* `design/paycheck_calculator.pseudo`

It can check that:

* required course files remain present;
* committed changes stay within the student-editable file set;
* the Draw.io file remains parseable XML;
* pseudocode retains its required `BEGIN`/`END` structure;
* changed pseudocode no longer contains starter `TODO:` prompts; and
* once graded work begins, both graded design files have changed from the starter state.

The optional `src/paycheck_calculator.py` program and the optional acceptance tests are **not required by student CI** and do not determine the Module Three assignment-check result.

### Fresh Personal Repositories Are Neutral

The two graded design files intentionally begin in a starter state. Creating a personal repository is not a student error.

Therefore, a brand-new personal repository with no committed graded-design changes should not fail simply because the assignment has not been started yet.

After a student begins changing graded work, CI can report that the other graded file is still untouched or that changed pseudocode still contains starter TODO prompts. That is development feedback, not a grade.

### How Students Should Use the Feedback

A useful workflow is:

> **Work → Save locally → Commit → Push → Review CI → Improve**

To review CI feedback:

1. Open the personal `it140-m3-assignment` repository on GitHub.
2. Select **Actions**.
3. Open the most recent **IT 140 Checks** workflow run.
4. Open **Assignment artifact check**.
5. Read the summary and the first failing step, if any.

A green result means the automated checks passed for the repository state they inspect. It does **not** mean the flowchart or pseudocode meets every rubric criterion, and it does not submit the assignment.

## When Something Fails

### Normal Development Feedback

After graded work begins, a failed student check may mean:

* only one of the two graded design files has changed;
* changed pseudocode still contains a starter `TODO:` prompt;
* the Draw.io XML is damaged; or
* a protected course file was modified, deleted, renamed, or added unexpectedly.

Read the first error, correct that problem, commit and push the correction, and review the new run.

### Possible Repository or CI Problems

A repository or CI problem is more likely when:

* a newly created personal repository fails before the student changes graded work;
* GitHub Actions fails during checkout or Python setup;
* the feedback clearly does not match the files stored in the personal repository; or
* several students report the same infrastructure failure.

Use the support routing in the root [README](../../README.md). Do not post completed graded work, credentials, tokens, or private identifying information in public GitHub Issues or Discussions.

## Faculty Guidance

CI is **formative feedback**, not grading automation.

When helping a student:

* use the workflow summary to identify whether the issue is repository integrity, Draw.io structure, pseudocode structure/TODO state, or completion state;
* remember that the optional Python practice is outside the graded Module Three CI scope;
* use the official D2L Guidelines and Rubric for grading; and
* treat a green workflow as only one development signal, not proof of assignment completeness or quality.

If many students encounter the same GitHub Actions setup failure, investigate a repository or platform problem before assuming identical student errors.

## Course Repository CI

### Course Repository Check

[`tests.yml`](../workflows/tests.yml) runs a separate **Course repository check** in `GC-STEM/it140-m3-assignment`.

The course job validates areas such as:

* required files and stable Markdown sections;
* local Markdown links;
* JSON and TOML configuration;
* the graded Draw.io and pseudocode starter artifacts;
* the repository social-preview image;
* course-managed Python syntax and Ruff checks; and
* the intentionally incomplete starter state through [`check_starter.py`](./check_starter.py).

[`check_repository.py`](./check_repository.py) contains the repository and artifact checks used by both course and personal repositories. Student mode changes only the student-facing completion behavior; it does not weaken protection of course-managed files.

### README Command Checks

[`readme-commands.yml`](../workflows/readme-commands.yml) runs only in the public course repository and validates README command blocks on:

| Environment | Shell |
| --- | --- |
| Linux | Bash |
| macOS | zsh |
| Windows | Git Bash from Git for Windows |

[`check_readme_commands.py`](./check_readme_commands.py) checks every fenced `bash`, `sh`, or `shell` block in README-style documentation for syntax and cross-platform policy violations. It also smoke-tests selected procedural blocks marked with comments such as:

```text
<!-- ci:command-test id=open-existing-repo fixture=existing-repo expect=repo -->
```

The smoke tests use disposable directories and harmless command shims. They verify path handling and documented command sequences without creating real GitHub repositories, commits, or VS Code sessions.

The checker rejects patterns that conflict with the course command convention, including:

* Command Prompt `%USERPROFILE%` syntax in Bash blocks;
* PowerShell `$env:` syntax in Bash blocks;
* Windows drive paths in cross-platform Bash blocks;
* backslashes with `~` or `$HOME`; and
* `code ~/Repos/...` instead of `cd ...` followed by `code .`.

### External Link Checks

[`external-links.yml`](../workflows/external-links.yml) protects external links in course-managed Markdown. External sites can occasionally fail temporarily; confirm a link is actually stale before replacing it.

## Maintainer Guidance

### When Course CI Fails

Fix the cause rather than weakening the check merely to make the workflow green. Confirm whether the change intentionally altered a required file, section marker, student-editable path, starter artifact, or command sequence.

### When README Command Checks Fail

Run the checker locally from the repository root when the applicable shell is available:

```bash
python3 .github/ci/check_readme_commands.py --shell bash --platform linux
```

On GitHub, review all three matrix jobs because a command can be valid in one shell environment and fail in another.

### Student CI Lifecycle

Preserve this behavior when modifying the workflow:

1. **Untouched personal starter:** neutral; no failure solely because work has not started.
2. **Graded work begins:** incomplete or damaged graded artifacts may produce formative failures.
3. **Both graded files changed and structural checks pass:** student CI can be green.
4. **At every state:** green is not a grade or submission.

Do not add the optional Python program or optional acceptance tests as student requirements unless the assignment requirements themselves change.

## Summary

Key points:

* Student CI gives limited formative feedback about repository integrity and the two graded design artifacts.
* A new personal repository is not treated as a failure simply because the graded starter files are untouched.
* Optional Python work remains optional and outside the student CI requirement.
* Course CI protects the template, starter artifacts, documentation, commands, and configuration.
* README commands are checked on Linux/Bash, macOS/zsh, and Windows/Git Bash.
* D2L Brightspace remains the assignment submission and grading system.
