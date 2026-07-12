# The Garden Remembers: What Months of Running a Persistent LLM RPG Taught Me About Making Models Behave

The most persistent bug I've ever debugged wasn't in code. It was my two-thousand-year-old matriarch slowly acquiring a habit she'd never had: letting other people summarize her.

I would hand the model her dialogue — exact words, chosen carefully — and, some sessions, get reported speech back. Paraphrase. Softening. A character who has outlived empires does not speak *in essence*; she speaks in full, and the room rearranges itself around what she said. I corrected it. It came back. I corrected it again. The rule that finally killed it is now the first and longest rule in the framework, and the story of why it kept coming back is the most useful thing I learned all year.

That was one failure among many. Over months of running a single persistent RPG campaign with an LLM as Game Master — one player character, a living world, an economy, a population, save states carried across chats, platforms, and model versions — I ended up running an accidental research program in long-horizon LLM reliability. The failures were funny in a game. They would not be funny in a production agent. This post is the autopsy file: what broke, why it broke, and the behavioral rules that stopped it from breaking again.

The full framework is public and free: **(https://github.com/theobliviax/Text-based-rpg-claude)**. What follows is why each piece of it exists.

## The setup, briefly

The campaign is a collaborative sci-fi RPG. I author one character completely; the model authors everything else — the world, every NPC, and the consequences of my actions. It runs in ordinary chat sessions and in Claude Code, with persistent memory, an inventory ledger, and a save-state convention that lets the whole campaign resume from a paste. The world itself stays offstage in this post; the machinery is what's on the table.

What matters is the shape of the problem: **one continuous fiction, sustained across hundreds of turns, dozens of context windows, and multiple model versions, with zero tolerance for the model forgetting who anyone is.** If that sounds like the requirements sheet for a long-running agent, that's the point. Games just make the failures visible faster, because you notice immediately when your matriarch starts acting like a chatbot.

## Problem one: the model keeps eating the protagonist

The most persistent failure was also the most fundamental. Given a player character, an LLM will slowly, politely absorb them. It narrates them in third person. It paraphrases their dialogue. It invents their feelings. It softens their intentions into something more agreeable. Each individual slip is small; the cumulative effect is that the player stops existing.

The cause, as best I can tell, is that assistant instincts outlive persona instructions. Summarization, hedging, and emotional narration are deep defaults, and a paragraph of "stay in second person" at the top of a long context loses to them eventually. Style guidance decays. What doesn't decay — or decays far more slowly — is *law*: a rule stated absolutely, with explicit wrong/right examples, positioned as the single most important instruction in the document.

So the framework's Rule #1 reads like statute. The player's dialogue is rendered verbatim, in quotes, in the character's mouth. Actions appear physically on the page before the world responds. Stated intent gets executed *exactly*, never softened or redirected. Third-person narration of the player character is flagged as WRONG in the text itself, next to the RIGHT form. And the rule ends with the sentence that did the most work of anything I wrote:

**"You decide whether the world yields; you never decide that she failed to try."**

That line survives because it resolves the model's actual ambiguity. Most protagonist-eating happens in the gap between player input and world response, where the model must fill *something* in. Telling it precisely which side of that gap it owns — outcomes yes, attempts never — removed the discretion that the drift was living in.

The general lesson: when an instruction keeps failing, stop repeating it louder and start looking for the ambiguity the model is falling into. Then legislate the ambiguity, not the symptom.

## Problem two: the lore ate the rules

Early on, everything lived in one document — behavioral rules, world history, NPC bios, tone notes. It worked, and then it slowly didn't. Every session added lore. The document grew. And the behavioral rules, which had once been a third of the prompt, became a rounding error inside a world bible. Both got worse: the model missed rules it used to follow, and hallucinated lore it used to look up.

The fix was recognizing that the two kinds of content have different lifecycles. **Lore grows. Behavior must not drift.** A document that grows and a document that must stay stable cannot be the same document.

So the architecture split. The behavioral contract — maybe two thousand tokens, hot in every context — governs *how the game runs*: rendering, turn structure, agency, tone. The canon lives in a separate searchable package the model is instructed to consult before asserting any world fact, with an explicit rule: **if unsure, search; never invent.** The spine stays small enough to hold. The world grows without diluting it.

This is the least glamorous section of the framework and the one I'd defend hardest. Almost every long-context reliability problem I hit traced back to important instructions being outnumbered by reference material. Separate them by lifecycle, and both behave.

## Problem three: an LLM will never tell you it lost count

The campaign tracks a population — a running headcount that changes as the story does. More than once, I checked it against the fiction and found it had drifted. No event explained the change. The model hadn't decided to alter it; it had simply regenerated a plausible-sounding number, confidently, in place of the real one.

An LLM will never tell you it lost count. It will simply, confidently, repopulate your ship.

Numbers are where silent failure lives, because a wrong number is fluent in a way a wrong name isn't. The fix has two parts. First, the framework names its **drift-prone quantities** explicitly — population, treasury, elapsed time, anything numeric that persists — so the model knows these are tracked values, not texture. Second, and more important: when a tracked number slips, the model is required to **reconcile openly**, in an out-of-character aside, instead of smoothing it over. Making the correction visible converts a silent corruption into a maintenance event.

If your agent tracks anything numeric across turns, I'd argue it needs the same two rules. Name the numbers. Forbid the quiet fix.

## The pattern library

The framework's most unusual section is a set of rule *patterns* extracted from months of specific corrections. A few, briefly:

**Hard physical negatives.** Models default-inject genre furniture — clothing, gear, habits — that can contradict a character's nature. Positive descriptions don't prevent this; explicit "NEVER describe X on this character" rules do. State your negatives.

**Never-bench rules, with escape valves.** Some constraints are absolute: a companion who always deploys with the protagonist, say. The insight isn't the rule — it's that a hard rule which forces bad fiction will eventually be broken *by the model, for good reasons*. The sustainable version builds the escape valve into the world: create the NPC whose entire job makes the constraint painless to keep. If a rule keeps straining, don't strengthen the rule. Change the world so the rule is easy.

**Restricted-use tokens.** Names or phrases with strict permissions — who may say them, how rarely, and whether the narrator may ever use them unprompted. Emotional weight comes from scarcity, and scarcity has to be legislated or the model spends it immediately.

**Register-gated vocabulary.** Terms with a casual form and a sacred form, the latter reserved for load-bearing moments. Same principle: the model must be told, explicitly, which words are expensive.

**Deliberate ambiguity flags.** Facts that must stay unmeasured — with an in-fiction justification — because a model asked twice will otherwise generate two different precise answers. If you want mystery, you have to forbid the measurement, not just omit it.

Each of these exists because a specific failure happened more than once. The full spec, with template slots, is in the repo.

## The save-state spine

Everything above keeps a single context coherent. What keeps the *campaign* alive is the save state: a compact block — location, key items, present characters, tracked quantities, active threads, world flags, recent events — appended on request or at natural stopping points. To resume anywhere, on any model, I paste the framework, the latest state block, and one line: continue from the current state.

That block has outlived context windows, platform migrations, and model upgrades. It is the campaign's portable soul, and it taught me the principle I now apply to everything agentic: **state you can paste is state you can trust.** If your system's continuity depends on what a model happens to remember, you don't have continuity — you have luck with good branding.

## What this was actually about

None of these are game problems. Persona stability is instruction adherence over long horizons. The lore split is context architecture. The population bug is silent state corruption. The save state is checkpointing. A persistent RPG is simply a test environment where every one of these failures announces itself in costume, immediately, to a user who is paying very close attention.

The framework is public, free, and designed to be filled in with your own world: **[(https://github.com/SableReiko/Text-based-rpg-claude/blob/main/persistent-world-gm.md)]**. Take the machinery. Break it in new ways. Tell me how.

The garden, of course, stays mine.
