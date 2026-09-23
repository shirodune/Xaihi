---
type: Playbook
title: Windows Cua connection
description: A sanitized playbook for remote observation using SSH and an interactive Windows Cua daemon.
status: draft
tags: [windows, computer-use, ssh]
sources:
  - id: cua-ssh
    resource: https://cua.ai/docs/how-to-guides/driver/windows-ssh
    title: Drive a Windows app over SSH
  - id: hermes-cua
    resource: https://hermes-agent.nousresearch.com/docs/user-guide/features/computer-use
    title: Hermes Computer Use
  - id: local-observation
    resource: initial-observation.md
    title: Initial observation experiment
---

# Scope

The tested architecture is an agent host invoking Cua Driver CLI over SSH, with a daemon running in the Windows interactive desktop session. This is not verified remote configuration for a platform's built-in computer-use tool.[^local-observation]

# Prerequisites

- Owner-approved Windows access and a logged-in interactive desktop.
- Cua Driver installed from its official source.
- An authorized SSH connection, ideally over a private network with source-restricted firewall rules.
- A dedicated key and a host fingerprint independently confirmed by the owner. Keep all actual connection details outside the repository.

# Interactive desktop daemon

From the Windows desktop, follow the upstream autostart instructions:[^cua-ssh]

```powershell
cua-driver --version
cua-driver autostart enable
cua-driver autostart kick
cua-driver status
```

Registering the task can request administrator approval. A daemon in an interactive session is needed because a Windows SSH session does not itself have access to the user's desktop. Do not weaken authorization modes to work around a failure.

# Verify the remote path

1. Verify remote identity with `whoami`.
2. Query the driver version and daemon status.
3. Discover tool schemas with `cua-driver describe TOOL` before invocation.
4. Enumerate windows with `cua-driver call list_windows`.
5. With consent, capture a target using `get_window_state` and freshly observed `pid` and `window_id`.
6. Decode the returned screenshot locally and inspect it. A success code is not proof of a usable image.
7. Test `get_desktop_state` separately; a window capture does not establish full-display capture.

Cua also documents an MCP proxy with an explicitly selected Windows pipe. That route needs its own integration test; CLI success does not prove MCP or an agent wrapper works.[^cua-ssh][^hermes-cua]

# Observed pitfalls

In the initial experiment, the shell command and install entry path failed to resolve while the actual release executable worked. Discover installation paths from the machine rather than hardcoding a release version. A full-display request timed out once and a later retry succeeded; the cause was not established.[^local-observation]

Window captures may return an empty accessibility tree while the image is usable. Treat screenshots and metadata as separate evidence. Keep large base64 image payloads out of model context.

# Next verification boundary

No game input has been verified by this playbook. Before gameplay, obtain consent for a small disposable input test and independently inspect the result. Game compatibility and foreground requirements remain open questions.

[^cua-ssh]: Official Cua Windows SSH instructions.
[^hermes-cua]: Official Hermes Computer Use documentation.
[^local-observation]: Sanitized historical findings; private raw evidence is not published.
