# Fleet Guide

Fleet gives you one place to run parallel coding tasks without mixing their branches,
working directories, or delivery steps. You keep using the agent CLI you trust. Fleet
creates the isolation around it, shows where every task stands, and gives you a deliberate
path from generated code to reviewed Git work.

If you are new to Astrolabe itself, start with the [User Guide](USER_GUIDE.md).

## What Fleet changes about agent work

Without isolation, two agents can edit the same checkout, switch branches underneath one
another, or leave you to reconstruct which changes belong to which task. Fleet gives every
task three things:

1. A fresh branch named `fleet/<task-slug>`.
2. A separate Git worktree called a **berth**.
3. An agent command launched inside that berth.

The default berth lives next to your repository at:

```text
<repository>.fleet/<task-slug>
```

Each agent works in its own directory while Git keeps the branches connected to the same
repository. The Fleet board then follows the work from setup to review and delivery.

> Fleet is orchestration, not chat. It launches and hosts your agent CLI, but the durable
> result is the branch and its Git diff. Review the work, not a remembered conversation.

## Before your first task

Make sure these commands work in a normal terminal:

```bash
git --version
your-agent-command
```

Fleet requires system Git 2.40 or newer. Your agent CLI must already be installed and
authenticated. Astrolabe does not install an agent, create an agent account, or store your
provider credentials.

Your repository should also have a clear base branch, usually `main`, and a working remote
if you plan to deliver through a pull request.

## Set up Fleet for a repository

Open the repository, then go to **Settings > Fleet**. These settings apply to the current
repository.

### Choose the default agent

Select a built-in agent preset or enter a custom launch command. This becomes the default
for new tasks, and you can still override it task by task.

Use the same command that you would run from the repository in a terminal. If the command
needs shell configuration, verify that it works outside Astrolabe first.

### Prepare each berth

A new worktree starts from Git-tracked files. Development environments often need more.
Configure any of these when your project requires them:

- **Files to copy:** local files such as `.env` or `.env.local` that are intentionally not
  committed. Never copy secrets the task does not need.
- **Bootstrap command:** a setup step such as `pnpm install`.
- **Port base and offset:** predictable ports for tasks that run local servers in parallel.

Fleet runs berth setup after creating the worktree and before launching the agent. A setup
failure is reported as part of the task, so you can fix the command and retry.

### Choose how local landing works

Select **Rebase** or **Merge** as the repository's local landing strategy.

- Rebase replays the task commits onto the current local base branch.
- Merge preserves the branch history and joins it to the local base branch.

This setting affects local landing only. A pull request still follows the merge policy of
your hosting service and repository.

### Choose a terminal mode

- **Embedded terminal:** work with the agent inside Astrolabe. Hiding the terminal does not
  stop the agent.
- **External terminal:** open the berth in your configured terminal application.

## Create and start a task

Open **Fleet**, then select **New task**.

### 1. Name the outcome

Use a short task name that describes a result, for example:

```text
Add VAT validation
```

Fleet previews the slug and branch name before creating anything:

```text
fleet/add-vat-validation
```

### 2. Give the agent useful context

Expand **Add context** and write a goal that explains the desired user or system outcome.
Then add one checkable acceptance result per line.

Example:

```text
Goal
Reject invoice VAT rates that are outside the supported range.

Acceptance criteria
Invalid VAT rates show a field-level error.
Valid invoices continue to save.
Tests cover the lower and upper boundaries.
```

Good acceptance criteria help twice: they guide the agent while it works, and they give
you a concrete review checklist when it finishes.

You can also record where the task came from, such as a manual request, Notion page, or
GitHub issue, and attach the source URL.

### 3. Check advanced options

The default base branch and agent come from Fleet settings. Open the advanced options when
this task needs a different base, agent preset, or custom command.

### 4. Start the task

Select **Create & start task**. Fleet will:

1. Create the `fleet/<task-slug>` branch.
2. Create the berth worktree.
3. Copy configured files and run the bootstrap command.
4. Launch the selected agent inside the berth.

If setup or launch fails, the branch and berth remain available for inspection. Fix the
cause and use **Retry start**.

## Read the Fleet board at a glance

Each card shows the task name, branch, goal, agent, elapsed time, diff summary, and current
state. The available action changes with the state, so the board always points to the next
useful step.

| State | What it means | What to do next |
| --- | --- | --- |
| **Preparing** | Fleet is creating or setting up the berth | Wait for setup to finish |
| **Running** | The agent command is active | Open or hide the terminal as needed |
| **Needs input** | The agent needs a decision or more information | Select **Respond** and unblock it |
| **Failed** | Setup or launch did not complete | Read the error, fix the cause, then retry |
| **Blocked by conflict** | A landing operation needs conflict resolution | Open the resolver and finish the landing |
| **External process untracked** | The berth was adopted, but Fleet did not launch its process | Stop the external process or start it through Fleet when you need tracked status |
| **Changes requested** | Review found work that needs another pass | Return the task to the agent, then review the updated files |
| **Ready for review** | The agent has stopped and changes are available | Open **View changes** or **Review** |
| **Awaiting PR merge** | A pull request is registered | Wait for the PR to merge, then confirm and retire the berth |
| **Completed** | The task has been delivered and retired | Find its receipt in completed task history |

State is always shown as text as well as color. You do not need to remember the meaning of
a colored dot.

## Work with a running agent

Use **Open terminal** to see or interact with the agent. You can hide an embedded terminal
and return later without stopping the command.

Use **End agent** when you want to stop the command but keep the branch and files. Ending an
agent does not delete the berth. Fleet also prevents two tracked agents from running in the
same berth at once.

If the agent needs a decision, answer the smallest question that unblocks the task. Update
the goal or acceptance criteria when the scope changes materially, so the eventual review
still reflects the work you intended.

## Adopt an existing worktree

Use **Adopt existing worktree** when work already exists in a Git worktree that Fleet does
not manage yet.

1. Choose one of the unmanaged worktrees Fleet found.
2. Name the task.
3. Enter its goal and acceptance criteria.
4. Confirm the base branch.
5. Select an agent preset or enter the command that should run there.
6. Adopt the worktree.

Astrolabe does not guess which process is already running in an adopted worktree. Until
Fleet launches the configured command, the task may show **External process untracked**.
This avoids presenting guessed process state as fact.

## Review the work, file by file

Select **Review** when a task is ready. Fleet compares the task branch with its base branch
and keeps the original goal and acceptance criteria beside the diff.

For every changed file:

1. Read the diff and compare it with the task intent.
2. Check behavior, tests, generated files, and unexpected scope changes.
3. Select **Mark reviewed** only when that file is acceptable.
4. Move to the next file.

You can remove a review mark if you want to revisit a file. If the agent changes a file
after you reviewed it, read the updated diff and review it again.

Fleet keeps delivery actions unavailable until every changed file is reviewed and the task
is ready. This makes review a visible gate rather than a promise you need to remember.

## Deliver through a pull request

Choose this path when the branch should go through remote checks, team review, or a hosted
merge policy.

### Create a pull request

1. Finish the Fleet review.
2. Make sure the task branch is available on `origin`. Fleet opens the comparison but does
   not push the branch for you. From the berth terminal, you can publish the current branch
   with `git push -u origin HEAD`.
3. Select **Create PR**.
4. Astrolabe opens the GitHub compare page for the task branch and base branch.
5. Review the title, description, commits, and target branch on GitHub.
6. Submit the pull request there.

Astrolabe opens the prepared comparison. It does not silently submit a pull request on
your behalf.

### Register an existing pull request

If the PR already exists, select **Register PR** and paste its GitHub URL. The task moves to
**Awaiting PR merge**.

After the PR is merged, select **Confirm merged & retire**. Fleet verifies that the task was
integrated when Git history makes that possible. A squash or rebase merge can make direct
ancestry impossible to prove, so Astrolabe asks for explicit confirmation instead of
pretending it knows.

Retiring the task removes its berth and records the delivery in completed task history.
The berth must be clean enough for Git to remove it safely. Commit or move any remaining
work before retirement.

## Land the work locally

Choose **Land locally** when you intentionally want to integrate the task into the local
base branch without a pull request.

Before anything moves, Astrolabe shows:

- The exact commits that will move to the base branch.
- Whether the local base branch is behind its remote. Local landing uses the local base.
- The configured rebase or merge strategy.
- That the berth will be removed and the task branch archived after success.
- Any untracked files that will be deleted with the berth and cannot be restored by Undo.

Read this confirmation carefully. Commit or move any untracked file you need to keep. The
agent must be stopped before local landing can begin.

Fleet serializes local landings through a queue. If several tasks are ready at once, they
land one at a time so they cannot move the same base branch concurrently.

If conflicts appear, the task moves to **Blocked by conflict** and opens the resolver.
Resolve every conflict, review the result, then select **Finish landing**. A successful
landing retires the berth and creates a completed task receipt.

## Guardrails worth understanding

- **One task, one berth:** parallel agents do not share a working directory.
- **Git is the source of truth:** Fleet follows real worktrees and branches, not only its
  own saved metadata.
- **One tracked agent per berth:** a second agent cannot be launched into the same task by
  accident.
- **Review is explicit:** every changed file must be marked reviewed before delivery.
- **Local landing is serialized:** only one task moves a base branch at a time.
- **Untracked files get a direct warning:** they are not recoverable through Astrolabe Undo
  after the berth is removed.
- **Remote delivery stays visible:** creating a PR opens GitHub for your final submission,
  and retirement waits for merge confirmation.
- **Credentials stay in your existing tools:** Fleet relies on system Git and your agent
  CLI instead of storing provider tokens.

## A practical operating rhythm

For work that is easy to review and land:

1. Keep each task focused on one outcome.
2. Write acceptance criteria before launching the agent.
3. Base new tasks on a branch that is current and intentional.
4. Give each task only the environment files and secrets it needs.
5. Check **Needs input** and **Failed** tasks before starting more work.
6. Review smaller completed tasks early instead of building a large landing backlog.
7. Prefer a pull request when team review or remote CI matters.
8. Use local landing only after checking the local base and untracked-file warning.

## Troubleshooting Fleet

### The agent command fails immediately

Run the same command from a normal terminal. Confirm that the executable is on `PATH`, its
login is valid, and it works from the repository. Then check the task's selected agent and
the repository's Fleet launch command.

### Berth setup fails

Check the copied file paths and bootstrap command in **Settings > Fleet**. Paths should be
valid for the repository, and the command should be safe to run in a fresh worktree.

### A task does not know an external agent is running

This is expected for an adopted worktree. Astrolabe does not infer process ownership. End
the external process and launch the configured command through Fleet if you need tracked
status and lifecycle controls.

### Review changed after it was complete

The branch changed after your earlier review. Re-open the affected file, inspect the new
diff, and mark it reviewed again.

### Local landing is blocked

Confirm that the agent has stopped. Then read the landing warning for an out-of-date local
base, untracked files, or conflicts. Resolve the stated condition rather than bypassing it.

### A merged PR cannot be verified automatically

The repository may use squash or rebase merging, which changes commit ancestry. Compare
the merged PR and task changes on GitHub. Confirm retirement only when you have verified
that the intended work is present on the base branch.

### A merged task cannot be retired

Check the berth for modified or untracked files. Git refuses to remove a worktree when that
could discard work. Commit anything that belongs on the task branch, or move files you need
to keep, then confirm retirement again.

## Before you retire a task

- Every changed file has been reviewed.
- The acceptance criteria have been checked.
- Tests or checks relevant to the change have run.
- Unexpected files or scope changes have been resolved.
- Important untracked files have been committed or moved.
- The pull request is merged, or the local landing confirmation is correct.

Need help with the rest of Astrolabe? Return to the [User Guide](USER_GUIDE.md) or
[report an issue](https://github.com/by-astrohub/astrolabe-changelog/issues).
