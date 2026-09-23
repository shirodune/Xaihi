# Xaihi

A practical exploration of AI agents playing games.

We connect agents to real games, observe what works and what fails, and turn verified experience into reusable knowledge. Xaihi names the project and its current agent collaborator; it does not require a particular model, agent platform, or control tool.

## Current status

This is an early experiment repository, not a universal gaming framework.

- Remote Windows access, window enumeration, window capture, and primary-display capture have been exercised with SSH and Cua Driver.
- Steam library inspection has been exercised through a window screenshot.
- Game input, autonomous gameplay, and completed game objectives have **not** been verified.
- Remote routing through Hermes's built-in `computer_use` wrapper has **not** been verified; the tested path uses the Cua CLI over SSH.

These are historical observations, not a claim that a reader's setup works or that the original connection is currently live.

## Start here

1. Read [AGENTS.md](AGENTS.md) for execution and privacy boundaries.
2. Read the [knowledge index](knowledge/index.md).
3. Follow the [Windows connection playbook](knowledge/windows-cua.md) only on a machine you own or are authorized to control.
4. Re-verify observation, then test one authorized input in a disposable context before attempting gameplay.

## Approach

Explore both screen-and-input interaction and game-provided interfaces. Label the observation and action channels for every experiment. Start with one real task; add code only when an observed limitation requires it. Do not mistake a successful API call for a verified game outcome.

## Knowledge and portability

`knowledge/` is an Open Knowledge Format (OKF) v0.2 bundle, following the [official specification](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). The surrounding repository is not itself an OKF bundle.

Keep game knowledge independent of platform-specific tool names. A different agent can read these files, but must configure its own tools, permissions, and credentials. No chat history or secret is required to read the project.

## Public and private data

Publish curated findings and intentionally reviewed evidence only. Keep save files in the game's normal location. Keep personal progress, raw screenshots, full logs, credentials, and machine-specific connection details outside this repository. A private directory beside, rather than inside, the checkout is recommended. Git ignores are a second line of defense, not a publication review.

No game binaries, ROMs, proprietary assets, or private screenshots are included. No gameplay success is claimed yet.
