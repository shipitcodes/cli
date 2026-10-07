# Shipcode CLI user guide

The `shipcode` CLI connects your local project to Shipcode. Use it to create a project, read stage instructions, get help from the mentor, run tests, submit solutions, and track your progress from the terminal.

For supported platforms and installation instructions, see the [repository README](../README.md#installation). This guide covers the public commands and everyday workflows. Available commands can vary by installed version; use `shipcode --help` to check your installation.

## Contents

- [Command overview](#command-overview)
- [A typical project workflow](#a-typical-project-workflow)
- [Authentication](#authentication)
- [Initialize a project](#initialize-a-project)
- [Read stage instructions](#read-stage-instructions)
- [Check progress](#check-progress)
- [Run tests](#run-tests)
- [Submit a solution](#submit-a-solution)
- [Ask the project mentor](#ask-the-project-mentor)
- [Inspect and follow a run](#inspect-and-follow-a-run)
- [Choose a project directory or stage](#choose-a-project-directory-or-stage)
- [Manage local preferences](#manage-local-preferences)
- [Update the CLI](#update-the-cli)
- [Environment variables](#environment-variables)
- [Troubleshooting](#troubleshooting)

## Command overview

| Command | Purpose |
| --- | --- |
| `shipcode auth login` | Sign in and authorize your terminal. |
| `shipcode auth status` | Check whether your CLI session is valid. |
| `shipcode auth logout` | Revoke the current session and remove its saved credential. |
| `shipcode init <code>` | Create the project you configured on the website. |
| `shipcode task` | Read the current stage instructions. |
| `shipcode status` | View progress and stage availability. |
| `shipcode test` | Run stage tests without submitting your solution. |
| `shipcode submit` | Run tests and record the result to advance your progress. |
| `shipcode ask [question]` | Ask the project mentor a question or start an interactive conversation. |
| `shipcode runs show <run-id>` | Inspect an existing cloud run. |
| `shipcode config` | View local CLI preferences. |
| `shipcode update` | Update the installed CLI. |
| `shipcode --version` | Print the installed version. |
| `shipcode --help` | Show available commands. |

For help with a command or subcommand:

```sh
shipcode test --help
shipcode ask --help
shipcode auth login --help
shipcode runs show --help
shipcode config set --help
```

## A typical project workflow

Install Git and the CLI, then select your project on [Shipcode](https://app.shipit.codes). The website provides an initialization command for the project you configured.

```sh
shipcode auth login
shipcode init sc_init_<your-code>
cd <generated-project-directory>
shipcode task
```

Replace the angle-bracket placeholders with your actual code and directory. Edit the project files in your preferred editor, then check your solution:

```sh
shipcode test
```

If you need guidance, ask the mentor:

```sh
shipcode ask "Why is my solution failing this stage?"
```

When you are ready to record your solution:

```sh
shipcode submit
shipcode status
shipcode task
```

A passing submission completes the stage and unlocks the next step when its prerequisites are satisfied. Repeat the workflow until you complete the project. Tests, submissions, and mentor questions require Pro; mentor questions also use your available mentor credits.

## Authentication

### Sign in

```sh
shipcode auth login
```

The command prints a one-time link. Open it in your browser, sign in to Shipcode if necessary, authorize the terminal, and paste the short code displayed by the website into the waiting terminal prompt.

The code expires after ten minutes and can only be used once. If it expires, run the command again to start a new login. The CLI saves the session credential locally, so you do not need to sign in before every command. Signing in again replaces the saved credential.

### Check your session

```sh
shipcode auth status
```

Use this when you are unsure which session is active or a command reports an authentication problem. It checks whether the effective credential still represents an active session.

### Sign out

```sh
shipcode auth logout
```

This revokes the current session on Shipcode and removes its saved local credential. You can also revoke authorized terminals from **CLI sessions** in your Shipcode settings. A revoked terminal must sign in again before using authenticated commands.

If you supplied the credential through `SHIPCODE_TOKEN`, logout cannot remove the variable from your current shell. Remove it yourself after signing out:

```sh
unset SHIPCODE_TOKEN
```

## Initialize a project

```sh
shipcode init sc_init_<your-code>
```

Run this from the parent directory where you want the new project to be created. Use the initialization code provided by the website for your selected project, language, and system.

The command downloads the project starter, verifies its checksum, creates the project directory, and initializes a Git repository with an initial commit. Git must be installed and available in your `PATH`.

The initial commit gives you a baseline for reviewing your changes with `git diff`. It does not require a configured Git name, email, or signing key and does not change your Git configuration.

Initialization does not overwrite a non-empty destination. If initialization is interrupted, run the same command again from the same computer to resume. Retries preserve existing commits and uncommitted changes.

## Read stage instructions

```sh
shipcode task
```

This displays the current stage instructions in your terminal. Use it before editing your solution or when you want to revisit the requirements.

To request a particular stage:

```sh
shipcode task --stage <stage-slug>
```

Use the actual stage slug shown for your project. Selecting a stage does not bypass its availability rules.

## Check progress

```sh
shipcode status
```

This shows completed, available, and locked stages. Use it to see what you have finished, what you can work on next, and which stages still have prerequisites.

## Run tests

```sh
shipcode test
```

The command selects the current required stage by default, packages your project, and runs its tests in an isolated cloud environment. It requires an active CLI session, an internet connection, and Pro.

To rerun a particular unlocked stage:

```sh
shipcode test --stage <stage-slug>
```

Runs also include regression tests for completed earlier stages, where applicable. This helps detect changes that break previously completed work.

During a run, the CLI reports progress and prints test results as they become available. The final report lists passes and failures, with diagnostics for failed tests or builds. Keep the printed run ID if you want to inspect the result later.

A passing `test` tells you the solution is ready to submit. Run `shipcode submit` to record it and complete the stage.

### Prepare your project for cloud tests

Project archives follow the project's ignore rules. Keep dependencies and generated output out of the submitted project when they are not needed.

The cloud environment does not install arbitrary packages. For projects with dependencies, follow the stage instructions and the supported-package guidance shown by the CLI. A dependency being installed on your computer does not make it available during a cloud run.

For exercises that compare program output, send debugging messages to stderr. Extra stdout, unexpected whitespace, or a missing final newline can cause an output comparison to fail; use the reported diagnostic to find the difference.

## Submit a solution

```sh
shipcode submit
```

This runs the tests and records the result on Shipcode. A passing result advances your progress; a failed result is saved without completing the stage.

To submit a particular available stage:

```sh
shipcode submit --stage <stage-slug>
```

Before running tests, a submission stages the project according to its Git ignore rules and creates a local commit for that stage, even when there are no changes. Review your files before submitting. The commit is retained if tests fail or the remote request is interrupted. The CLI does not push the project to a Git remote.

After completing the last mandatory stage, the CLI displays a project summary and certificate link. In an interactive terminal, it may also show a celebration and open the certificate in your browser.

For a finished project, testing or resubmitting a completed stage displays the completion message instead of starting another cloud run. A pending optional stage can still be submitted explicitly with `--stage`.

## Ask the project mentor

### Ask a single question

```sh
shipcode ask "How should I approach this stage?"
```

The mentor uses the project and stage context to help you reason about your solution. Questions require Pro and use the same mentor allowance as the website. The CLI displays the remaining period credits after each answer.

### Keep a conversation open

Run the command without a question in an interactive terminal:

```sh
shipcode ask
```

Enter your questions at the prompt. Type `exit`, `quit`, or `salir` to end the session. Outside an interactive terminal, supply a question as an argument.

### Choose a stage or start a new chat

```sh
shipcode ask --stage <stage-slug> "What is expected in this stage?"
shipcode ask --new "Help me review my approach from the beginning."
shipcode ask --dir ./my-project "Why does this test fail?"
```

Put options before the question text. The command treats trailing arguments as the question, so options placed after the question may become part of it.

By default, questions continue the current conversation for your attempt. After a failed test, the first question continues that run's review. Use `--new` to start a separate chat. If you omit `--stage`, the CLI selects the current stage or the failed run being continued.

### Review after a failed run

After a failed test or submission, the CLI can wait for the mentor's automatic review and display it in the terminal. Availability depends on your plan, remaining credits, and whether a review can be produced. If the terminal directs you to the stage page, open that page to continue.

You can change how long the CLI waits for answers and automatic reviews through [local preferences](#manage-local-preferences).

## Inspect and follow a run

Use the run ID printed by `test` or `submit`:

```sh
shipcode runs show <run-id>
```

To keep waiting and stream new output:

```sh
shipcode runs show <run-id> --follow
```

These commands inspect the existing run without uploading your project again. They are useful if your terminal disconnects, a request times out, or you want to revisit a result.

If you are outside the project directory:

```sh
shipcode runs show <run-id> --follow --dir ./my-project
```

A network interruption does not establish that a test failed. Inspect or follow the existing run before starting a replacement.

## Choose a project directory or stage

Project commands use the current directory by default. The following commands support `--dir`:

```sh
shipcode task --dir ./my-project
shipcode status --dir ./my-project
shipcode test --dir ./my-project
shipcode submit --dir ./my-project
shipcode ask --dir ./my-project "What should I change?"
shipcode runs show <run-id> --dir ./my-project
```

Point `--dir` at the initialized project directory. `init` creates a project from its initialization code and does not accept this option.

`task`, `test`, `submit`, and `ask` also support `--stage`. Combine the options when needed:

```sh
shipcode test --dir ./my-project --stage <stage-slug>
shipcode ask --dir ./my-project --stage <stage-slug> "Explain this requirement."
```

`status` shows the overall project progress; it does not accept `--stage`.

## Manage local preferences

View all settings and their effective values:

```sh
shipcode config
shipcode config list
```

Read, change, or reset a setting:

```sh
shipcode config get mentor.ask_timeout
shipcode config set mentor.ask_timeout 120
shipcode config unset mentor.ask_timeout
```

Find the local configuration file:

```sh
shipcode config path
```

| Setting | Default | Allowed values | Purpose |
| --- | --- | --- | --- |
| `mentor.ask_timeout` | 70 seconds | 10–600 seconds | Maximum wait for an answer to `shipcode ask`. |
| `mentor.review_timeout` | 60 seconds | 0–600 seconds | Maximum wait for an automatic review after a failed run. Set to `0` to skip the wait. |

For example, wait longer for mentor answers but skip waiting for automatic reviews:

```sh
shipcode config set mentor.ask_timeout 120
shipcode config set mentor.review_timeout 0
```

Settings are per computer and optional; defaults apply when no configuration file exists. Values must be whole numbers of seconds within the allowed ranges. Unknown keys or invalid values produce an error.

These preferences control waiting time. They do not change stage access, your plan, mentor credits, or test limits.

## Update the CLI

```sh
shipcode update
```

This downloads the latest stable release for your platform, verifies its SHA-256 checksum, and replaces the installed executable. It does not require a Shipcode session.

To install a particular published version:

```sh
SHIPCODE_VERSION="0.1.0" shipcode update
```

Replace `0.1.0` with the desired version. Accepted formats include `0.1.0`, `v0.1.0`, and `cli-v0.1.0`; a published prerelease can be selected with its full version, such as `0.2.0-beta.1`.

You can also rerun the [installer](../README.md#installation). Check the result with `shipcode --version`.

## Environment variables

These variables customize user-facing behavior:

| Variable | Purpose |
| --- | --- |
| `SHIPCODE_VERSION` | Select a published version for the installer or `shipcode update`. |
| `SHIPCODE_INSTALL_DIR` | Change the installer's destination; the default is `~/.local/bin`. This does not change the target of `shipcode update`, which replaces the installed executable. |
| `SHIPCODE_TOKEN` | Supply a CLI session credential for automation, overriding the saved credential. |
| `SHIPCODE_CONFIG_DIR` | Choose a different directory for local CLI configuration. |
| `SHIPCODE_NO_BROWSER` | Set to `1` to prevent automatic browser opening for the completion certificate. |
| `NO_COLOR` | Disable colored terminal output. |
| `CI` | Use output appropriate for an automated environment, without the interactive completion celebration. |

For example, disable colors for a command:

```sh
NO_COLOR=1 shipcode status
```

For automation, supply `SHIPCODE_TOKEN` through your environment's secret storage. Treat it as a password and keep it out of source files, commits, and shared logs. Interactive `shipcode auth login` remains the usual way to authorize a personal terminal.

## Troubleshooting

| Problem | What to do |
| --- | --- |
| `shipcode` is not found | Add the installation directory to `PATH`, open a new terminal, and run `shipcode --version`. |
| Git is missing | Install Git and check `git --version` before initializing or submitting a project. |
| The session is invalid or revoked | Run `shipcode auth status`, then `shipcode auth login` if needed. |
| Initialization was interrupted | Rerun the same `shipcode init` command on the same computer. |
| A command cannot find the project | Run it inside the initialized directory or use `--dir` on a supported command. |
| A stage is locked | Check `shipcode status` and complete its prerequisites. |
| A test passed but progress did not advance | Run `shipcode submit` to record the solution. |
| A build or test fails | Read the diagnostic, check `shipcode task`, edit your solution, and rerun the tests. |
| A cloud run was interrupted locally | Use `shipcode runs show <run-id> --follow` to follow the existing run. |
| A dependency is unavailable | Follow the project's supported-package guidance; local dependencies are not automatically installed in the cloud. |
| A mentor answer times out | Increase `mentor.ask_timeout` within its allowed range. |
| No mentor review appears | Check your plan and credits, and make sure `mentor.review_timeout` is not set to `0`. |
| A command or option is unavailable | Check its `--help` output and update the CLI if necessary. |

When asking for help with a run, include your CLI version, the command you ran, the run ID, and the relevant error message. Keep session credentials and initialization codes out of shared reports.
