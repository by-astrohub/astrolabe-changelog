# Astrolabe User Guide

Astrolabe helps you move from working changes to reviewed, deliverable commits without
losing sight of what Git is doing. Use Graph for everyday Git work, Conflicts when changes
collide, and Fleet when several coding agents are working in parallel.

This guide covers the essentials. If you are here for agent workflows, continue with the
[Fleet Guide](FLEET_GUIDE.md).

## Download the beta

- [macOS](https://github.com/by-astrohub/astrolabe-changelog/releases/download/v0.1.0-beta.2/Astrolabe-beta-2-mac.dmg)
- [Windows](https://github.com/by-astrohub/astrolabe-changelog/releases/download/v0.1.0-beta.2/Astrolabe-beta-2-win.exe)
- [Beta 2 release notes](https://github.com/by-astrohub/astrolabe-changelog/releases/tag/v0.1.0-beta.2)

Astrolabe is currently beta software. Keep important work pushed to a remote and review
the confirmation shown before any operation that rewrites or removes work.

## Before you start

You need:

- Git 2.40 or newer available in your system terminal.
- A local Git repository, or the URL of one you can clone.
- Working Git credentials for any remote you plan to pull from or push to.
- For Fleet, an agent CLI such as Claude Code or Codex that is already installed,
  authenticated, and working in a normal terminal.

Astrolabe uses your system Git configuration, credential helper, and SSH keys. It does not
ask you to store a GitHub token in the app.

## Install Astrolabe

### macOS

1. Download the macOS disk image.
2. Open the `.dmg` file.
3. Move Astrolabe to Applications, then open it.

### Windows

1. Download the Windows installer.
2. Open the `.exe` file.
3. Follow the installer, then launch Astrolabe.

## Your first five minutes

### 1. Open a repository

From the welcome screen, choose one of these paths:

- Select **Open repository** and choose a folder that already contains a Git repository.
- Select **Clone**, paste a repository URL, and choose where it should be saved.
- Open a repository from **Recent repositories**.

After a repository opens, Astrolabe takes you to Graph.

### 2. Read the Graph workspace

Graph brings the current state of the repository into one place:

- The commit graph shows branches, commits, and their relationship over time.
- **Local changes** shows work that has not been committed yet.
- The details panel shows the selected commit, file, or diff.
- The top toolbar holds repository actions such as undo, redo, pull, push, and stash.

Start with the working changes, decide what belongs in the next commit, and inspect the
diff before staging it.

### 3. Stage and commit a focused change

1. Select a changed file to review its diff.
2. Stage the whole file, or stage only the hunks or lines that belong together.
3. Enter a clear commit message.
4. Select **Commit**.

If the commit cannot proceed, Astrolabe explains what is missing or what Git needs. For
example, it will ask you to stage a file before committing or help you configure a missing
Git identity.

### 4. Synchronize with the remote

Use **Pull** when the remote branch has commits you do not have locally. Use **Push** when
your local branch has commits ready to share.

The toolbar shows ahead and behind counts when an upstream branch is configured. If a push
is rejected because the remote moved, pull and review the incoming work before trying
again.

## Choose the workspace that matches the job

| Workspace | Use it when | What you do there |
| --- | --- | --- |
| **Graph** | You are inspecting history or preparing commits | Review changes, stage, commit, switch branches, stash, pull, and push |
| **Conflicts** | A merge, rebase, or landing has conflicts | Compare both sides, see who wrote them, choose a result, and finish the operation |
| **Fleet** | Coding agents are working on parallel tasks | Create isolated tasks, watch their state, review every changed file, and deliver the work |

Use the top navigation to move between these workspaces. Open **Settings > Shortcuts** for
the complete shortcut list and to find the fastest path for your platform.

## Everyday Git workflows

### Switch branches without losing local work

Choose the branch you want from the repository sidebar. If the checkout would overwrite
uncommitted changes, Astrolabe offers to stash the changes and switch safely. Read the
prompt before continuing so you know where the work will be stored.

### Set work aside with a stash

Use **Stash** when the current changes are not ready for a commit but you need a clean
working tree. Give the stash a useful message. You can later inspect it and apply it back
to the repository.

### Inspect a commit or file

Select a commit in Graph to see its details and changed files. Select a file to read the
diff. This is also the best place to verify what will be shared before a push.

### Resolve conflicts with context

Open **Conflicts** when Git reports a conflicted operation. Author Lens shows both sides of
each conflict with commit and author context, so you can decide based on intent instead of
guessing from raw markers.

For each conflict:

1. Read both sides and their author context.
2. Choose yours, theirs, both, or edit the result manually.
3. Review the resolved result.
4. Continue until every conflict is resolved.
5. Finish the merge, rebase, or Fleet landing when Astrolabe says it is ready.

During a rebase, Git's internal meaning of "ours" and "theirs" can be surprising.
Astrolabe keeps the user-facing labels focused on whose work you are looking at.

## Work confidently

- **Undo and redo:** Astrolabe records supported Git operations in the toolbar. Check the
  operation name before undoing it.
- **Force push:** A force push rewrites remote history. Astrolabe uses force-with-lease and
  asks for confirmation. Continue only when you understand who else uses the branch.
- **Long operations:** Clone, pull, push, and large history reads show progress. You can
  inspect what is running instead of wondering whether the app is stuck.
- **External Git changes:** Astrolabe watches the open repository. If you edit it in an IDE
  or terminal, give the app a moment to refresh before acting on the new state.

## Troubleshooting

### A repository will not open

Confirm that the folder exists and contains a valid Git repository. In a terminal, run
`git status` inside the folder. If Git itself cannot read the repository, fix that error
first and try again.

### Pull or push cannot authenticate

Test the same remote with system Git in a terminal. Astrolabe uses the same credential
helper or SSH keys, so a working terminal setup is the quickest way to confirm access.

### The changes you expect are not visible

Confirm that the correct repository and branch are open. Check whether the files are
ignored by Git, then wait briefly for the repository watcher to refresh.

### A Fleet agent does not start

Open a terminal and run the configured agent command yourself. If it does not start there,
fix its installation or authentication first. Then check the repository's Fleet settings,
including the launch command and berth setup command.

## Get help

- [Read the Fleet Guide](FLEET_GUIDE.md)
- [Review the changelog](../CHANGELOG.md)
- [Report a bug or request a feature](https://github.com/by-astrohub/astrolabe-changelog/issues)

When reporting a problem, include your operating system, Astrolabe version, Git version,
what you expected, what happened, and the exact step where it failed. Remove secrets and
private repository details before attaching screenshots or logs.
