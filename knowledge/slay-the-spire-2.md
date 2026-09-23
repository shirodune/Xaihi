---
type: Playbook
title: Slay the Spire 2 integration
description: Connect an external agent through MCP or use the Mod's built-in player.
tags: [games, slay-the-spire-2, mcp]
---

# Slay the Spire 2

**Status: Gameplay verified, not a verified full clear.** We have used external agents and the Mod's built-in player in real runs. See the [experiment notes](slay-the-spire-2-experiments.md).

## Requirements

Our tested setup used Windows, game **v0.111.0**, and [CharTyr/STS2-Agent](https://github.com/CharTyr/STS2-Agent) **v0.15.0**. This is a tested combination, not a promise that every newer release is compatible. Follow the upstream installation instructions and check release compatibility before installing.

Modded and unmodded saves can be separate. Back up saves before any migration; do not assume an empty modded profile means the original save is gone. No save migration is required just to test connectivity.

## Choose a player

- **External agent:** your own MCP-capable agent reads state and chooses actions. Its host manages model requests, context and accounting.
- **Built-in autoplay:** configure an OpenAI-compatible endpoint and play model in the Mod panel. The Mod manages decisions itself. An external supervisor can observe it, but must not issue competing game actions.

Use one controller at a time. Neither route inherits this repository's notes automatically: load the relevant guidance explicitly.

## Connect an external agent

Enable MCP in the Mod's connection panel. On the game computer, the tested endpoint is:

```text
http://127.0.0.1:8080/mcp
```

A generic client configuration is:

```json
{
  "mcpServers": {
    "sts2-ai-agent": {
      "type": "http",
      "url": "http://127.0.0.1:8080/mcp"
    }
  }
}
```

Client configuration schemas vary. Use the client's documented HTTP/Streamable HTTP MCP support; the important part is the endpoint, not this exact configuration wrapper.

### Remote agents

Loopback on the agent machine is not loopback on the game machine. Our remote setup used an access-restricted Tailscale TCP Serve forward to the game's loopback service. An SSH tunnel is another option. Restrict the path to authorized agents; do not publish the unauthenticated game-control endpoint on the Internet. Verify your access rules rather than assuming a private network permits only the intended peer.

SSH/Cua desktop control is optional. It helps with settings that have no game API, but is not required to play cards through MCP. See the [Windows Cua notes](windows-cua.md) for the separate desktop channel.

### Hermes example

For a local game endpoint, register it in the intended profile:

```bash
hermes --profile YOUR_PROFILE mcp add sts2-ai-agent --url http://127.0.0.1:8080/mcp --connect-timeout 20
hermes --profile YOUR_PROFILE mcp test sts2-ai-agent
```

Use your restricted forwarded endpoint instead when remote. Install/load the upstream [sts2-mcp-player skill](https://github.com/CharTyr/STS2-Agent/tree/main/skills/sts2-mcp-player), matched to the installed Mod where possible.

In our Hermes installation, the explicit CLI toolset selector was `-t sts2-ai-agent,file`. Using `mcp-sts2-ai-agent` failed discovery in that launch path. Check your installed CLI and validate the actual worker's tools, not only the supervisor's connection. Do not change shared model configuration merely to launch a player; use invocation-scoped model selection.

## First verified action

1. Call `health_check`; confirm the service is ready and built-in autoplay is paused.
2. Read `get_game_state` or `decide`; check the profile, character, difficulty and run identity.
3. With the player's authorization, call one action from the latest `available_actions`, including a short reason. Do not start or abandon a run just to test the connection.
4. Inspect the returned state. Re-read after a pending/uncertain transition; never blindly replay an action.
5. Stop and confirm the observed change before allowing longer play.

Prefer compact state and reuse the state returned by `act`. Use targeted metadata lookups for unfamiliar mechanics and `wait_until_actionable` for animations. A missing action during a transition is not permission to invent another one.

## Built-in autoplay

Configure and save the endpoint, model and play role in the game panel. Test connection, then perform a small bounded gameplay test. Connection verification alone does not prove correct tool use.

On our installation, loopback `POST /session/control` with `{"running":true}` started autoplay and `{"running":false}` paused it. Verify through `/health`; acceptance of a request is not proof of the resulting state. Do not assume this loopback-only control route is remotely accessible just because MCP is forwarded.

We verified a ten-request budget stopped new autoplay requests. Requests are not cards or turns, and token caps are not billing guarantees. In-flight work can still complete. Never remove caps without explaining the shared-quota exposure.

## Limitations and cost

- We have not established a reliable full-clear rate or a model ranking.
- External long conversations can accumulate large histories and trigger costly compression.
- Built-in decisions can be smaller but numerous; cache reuse depends on the full provider/proxy path.
- Record input, output, cache reads/writes, requests and compression separately. Total token volume does not equal subscription quota or money.
- Stop on quota failure; preserve the run instead of silently changing models or restarting.
- Desktop input in this game's custom UI was less reliable than game MCP actions. Validate edits and stop repeated GUI retries early.
