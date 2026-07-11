# Text-based-rpg-claude
A battle-tested behavioral framework for running long-horizon RPG campaigns with LLMs. Stops voice drift, canon contamination, and silent state corruption across hundreds of turns and context resets. Includes template slots for your own world, a playable gothic-cyberpunk demo, and a write-up of every failure mode that shaped the rules.


Persistent-World GM

A behavioral framework for running long-horizon RPG campaigns with LLMs — battle-tested over months of continuous play.

Most LLM roleplay advice is about writing better characters. This framework is about the harder problem: keeping a model behaved across hundreds of turns, dozens of context resets, and multiple model versions — without voice drift, canon contamination, silent state corruption, or the slow erosion of player agency.

It was extracted from a single persistent campaign run long enough for every failure mode to show up more than once. Each rule exists because something specific broke without it.

What's in the box

FileWhat it ispersistent-world-gm.mdThe framework — a behavioral contract you paste as your GM's system prompt, with template slots for your own worldvespergate-demo.mdA worked example — a small gothic-cyberpunk setting with every template slot filled, playable immediatelythe-garden-remembers.mdThe write-up — what broke, why it broke, and the design reasoning behind each rule

Quick start


Paste persistent-world-gm.md and vespergate-demo.md into a fresh chat (or a Claude Project / character frontend of your choice).
Say: "Start Vespergate."
Play. When you want to stop, say "save" — the GM appends a [CURRENT STATE] block.
To resume anywhere, on any model: paste the framework + the demo + your latest state block + "Continue from the current state."


To run your own world: copy the demo's structure, fill the template slots in the framework with your canon, and keep lore and behavior in separate documents. The write-up explains why that split matters.

Design principles, in one breath

The player is never paraphrased. Behavior and lore live in separate documents because they age differently. Numbers the model tracks are named, and corrections happen in the open. Hard rules get escape valves built into the world. State you can paste is state you can trust.

Compatibility

Written for and tested with Claude, but the rules are model-agnostic — the failure modes they prevent are universal LLM behaviors. Works in plain chat, Claude Projects, Claude Code, and character frontends that accept a system prompt (SillyTavern users: the framework maps cleanly onto a system prompt + lorebook split).

License

Persistent-World GM
Copyright (c) 2026 Sable Reiko-1

This work is licensed under the Creative Commons Attribution 4.0
International License (CC BY 4.0).

You are free to share and adapt this material for any purpose,
including commercially, provided you give appropriate credit to
Sable Reiko-1, link to the license, and indicate if changes were made.

Full license text: https://creativecommons.org/licenses/by/4.0/legalcode

Author

Built by Sable Reiko-1. If you use this, break it, or improve it — I want to hear about it: [CONTACT/LINK].
