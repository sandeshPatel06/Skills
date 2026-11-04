---
name: using-tod
description: Drive OneDev (issues, pull requests, builds) through the `tod` CLI. 
  Use whenever the user asks to create, edit, query, change  state, comment on, review, 
  checkout, run CI/CD job for, get commit message requirement, or otherwise interact 
  with anything in OneDev.
---

# Using the tod CLI

This skill is the umbrella guide for driving OneDev through the `tod` CLI.
More specialized skills (`edit-build-spec`, `investigate-build-problems`,
`review-pull-request`, `generate-commit-message`, `work-on-issue`,
`submit-work`) cover specific workflows in more detail — read them when
their description matches the user's intent.

## Prerequisites

- `tod` is installed and on `PATH`.
- A tod config file (`$XDG_CONFIG_HOME/tod/config` or
  `~/.config/tod/config`) has `server-url` and `access-token` set. Run
  `tod config set` to create one.
- For commands that operate on the current project, the working directory is
  inside a git repository whose remote points at that OneDev project.

## Command shape

Commands are grouped by resource:

- `tod issue` — `list`, `get`, `get-comments`, `create`, `edit`, `change-state`,
  `link`, `add-comment`, `log-work`, `current-reference`,
  `get-query-description`, `get-valid-fields`, `get-valid-links`
- `tod pr` — `list`, `get`, `get-comments`, `get-code-comments`, `get-patch`,
  `get-file-content`, `create`, `edit`, `approve`, `request-changes`, `merge`,
  `discard`, `add-comment`, `add-code-comment`, `checkout`, `get-query-description`
- `tod code-comment` — `add-reply`, `resolve`, `unresolve` (operates on the
  `id` returned by `tod pr get-code-comments`)
- `tod build` — `list`, `get`, `get-log`, `get-file-content`,
  `get-changes-since-success`, `run` (with `--branch`, `--tag`, or `--local`),
  `get-spec-schema`, `check-spec`, `get-query-description`
- `tod config` — `init`, `show`, `path`
- `tod get-login-name`, `tod get-unix-timestamp`, `tod project`,
  `tod remote`, `tod get-valid-labels`,
  `tod get-commit-message-requirement`

Every command writes the raw OneDev server response to stdout. Run
`tod <command> --help` at any time to see exact flag names.

## Reference formats

Commands that take a `<ref>/<source ref>/<target ref>` argument accept:

- Issues / pull requests / builds: `#<n>`, `<project>#<n>`, or
  `<project-key>-<n>` (e.g. `#123`, `myproject#123`, `PROJ-123`).

When the user wants to act on "the current issue" without naming a
reference, run `tod issue current-reference` first. It prints `#<n>`
when the current branch matches `[<prefix>/]issue-<n>[-<suffix>]` and the
current project can be inferred, otherwise it prints nothing. Treat empty
output as "no current issue" and ask the user for an explicit reference.

## Requesting user consent for state-changing calls

Ask the user to confirm before running any command that changes OneDev
state: `create`, `edit`, `change-state`, `link`, `add-comment`,
`add-code-comment`, `add-reply`, `resolve`, `unresolve`, `log-work`,
`approve`, `request-changes`, `merge`, `discard`, `run` etc. Various
list/get/show commands do not need consent.

## Querying & Filtering via --query

Commands like `tod pr list --query "..."` and `tod issue list --query "..."` use OneDev's ANTLR structured query grammar.

- **Field names are strictly Title-Cased / Case-Sensitive**:
  - Pull requests: `"Source Branch" is "..."`, `"Target Branch" is "..."`, `"Source Project" is "..."`, `"Submitter" is "..."`, `"Title" contains "..."`, `"Status" is "OPEN"`.
    *(Note: Using lowercase like `"source branch"` returns an HTTP 500 error: `Field not found: source branch`)*.
  - Issues: `"State" is "..."`, `"Type" is "..."`, `"Priority" is "..."`, `"Assignees" is "..."`.
- **Query Grammar Inspection**:
  - Run `tod pr get-query-description` or `tod issue get-query-description` to inspect the full ANTLR grammar, supported operators (`is`, `contains`, `and`, `or`, `order by`), and valid criteria keywords.

## Passing Multi-line Markdown Content

When creating or editing issues and pull requests, or adding comments:
- Avoid passing raw multi-line Markdown directly into `--description` or `--comment` flags, as shell quoting and newlines easily break or corrupt formatting.
- Always prefer `--description-file -` or `--comment-file -` using `stdin` with a heredoc:
  ```bash
  cat << 'EOF' | tod pr edit <ref> --description-file -
  # Pull Request Description
  - Point 1
  - Point 2
  EOF
  ```
- Similarly for state transitions:
  ```bash
  tod issue change-state <ref> "<state>" --comment "Rationale here"
  # Or with multi-line comment:
  cat << 'EOF' | tod issue change-state <ref> "<state>" --comment-file -
  Multi-line rationale...
  EOF
  ```

## Issue State Transitions & Custom Fields

- **Valid States**:
  - Typical OneDev states include: `Draft`, `Open`, `In Progress`, `Testing`, `In Review`, `Closed`. Check `tod issue get-query-description` for the exact `StateName` tokens in the project grammar.
- **Custom Fields**:
  - Run `tod issue get-valid-fields` (without arguments) to inspect custom fields (such as `Type`, `Priority`, `Effort`, `Deadline`, `Assignees`).
  - *Do not pass an issue reference* to `tod issue get-valid-fields` (e.g. `tod issue get-valid-fields 123` will error).

## Issue Creation Template

When the user asks to create an issue, use this description format (without a leading template title line):

## 📝 User Story

**As a** [user type / role]
**I want** [feature, fix, or change]
**So that** [business value or user benefit]

---

## 📖 Problem Description

**Current Behavior**
[What is happening right now?]

**Why This Is a Problem**
[Why this behavior is incorrect, confusing, risky, or inefficient.]

**Context**

* Platform: [Web / Mobile / Backend / Desktop / API]
* Environment: [Prod / Staging / Dev / Local]
* Affected area(s): [module, screen, service, API, workflow]

---

## 🔄 Steps to Reproduce

1. Open [application/system] as [user type].
2. Perform [action].
3. Navigate to [screen/page/tab].
4. Trigger [event: submit, cancel, delete, refresh, etc.].
5. Observe: [incorrect or unexpected behavior].

---

## 🎯 Expected Behavior

[Clearly describe what *should* happen instead.]

---

## ✅ Requirements

* [ ] Each entity/item/session has a **unique identifier**
* [ ] Removing/cancelling invalidates related identifiers or state
* [ ] No reuse of identifiers, references, or stale data
* [ ] Remaining entities continue functioning normally
* [ ] UI/state reflects the latest correct data
* [ ] System remains stable across retries and refreshes

---

## 🗒️ Implementation Checklist

* [ ] **Update core logic** → [business rules / workflow changes]
* [ ] **Validate data handling** → [IDs, tokens, cache, storage, cleanup]
* [ ] **Ensure UI/UX refresh** → [screen, component, state update]
* [ ] **Confirm UX flow** → [expected user journey and feedback]
* [ ] **Update documentation** → [README, API docs, comments]

---

## 🎯 Acceptance Criteria

* [ ] When [action] occurs, [expected result] happens
* [ ] Entity/state is correctly created, updated, or removed
* [ ] No incorrect redirection, reuse, or stale state appears
* [ ] System behaves consistently across refreshes and retries
* [ ] User experience is intuitive and predictable

---

## 📌 Additional Notes & Quality Checklist

### 🔧 Technical

* Backend/logic must update status correctly (`active`, `cancelled`, `deleted`, etc.)
* APIs/services must return **only valid and current entities**
* Handle race conditions, retries, and partial failures safely
* Ensure idempotency — repeated actions don’t corrupt state
* Clean up unused resources (files, sessions, cache, references)

### 🎨 UX / UI

* UI must never show invalid or outdated state
* Provide clear user feedback (confirmation, error, loading)
* No silent failures — user should understand what happened
* Ensure behavior matches platform expectations (mobile vs web)

### 🧪 QA / Testing

* Test single and multiple entity scenarios
* Test edge cases:

  * Cancel → retry
  * Refresh during action
  * Offline / slow network
  * App background → resume
* Verify backward compatibility where applicable

### 📊 Monitoring & Logging

* Log key events and state transitions
* Track unexpected behavior or anomalies
* Ensure errors are observable and debuggable
