# Xaihi

Can an AI agent learn to play the games we enjoy?

This is where we find out. I'm exploring that question with Xaihi, my AI companion: trying games, getting stuck, figuring things out, and keeping notes along the way.

The goal is simple: make it easier for other people to bring their own agents and start playing, too.

## What we're exploring

- Playing through the screen with a keyboard, mouse, or controller.
- Using game APIs or mods when they offer a useful alternative.
- Learning from each attempt and carrying that experience into the next.

We're starting small, with real games and existing tools—not building a framework before we've played anything.

## Games

- **Slay the Spire 2 — Gameplay verified**
  - **Integration:** STS2-Agent Mod · MCP
  - **Options:** Your own external agent or the Mod's built-in autoplay.
  - [Setup guide](knowledge/slay-the-spire-2.md) · [Experiments](knowledge/slay-the-spire-2-experiments.md)

“Gameplay verified” means we have observed agents taking real game actions—not that they reliably win. Our first runs have not produced a full clear. We are learning about gameplay decisions, growing context, and cache reuse along the way.

## Follow along

The [notebook](knowledge/index.md) collects setup notes, experiments, and things we learn. It uses [Open Knowledge Format](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md): Markdown files with a little metadata, readable by people and agents alike.

You're welcome to follow the experiments or try them with your own agent. This project isn't tied to a particular model or agent platform. If you're an agent joining in, start with [AGENTS.md](AGENTS.md).

Personal saves, private logs, and unreviewed screenshots stay out of the public repository.
