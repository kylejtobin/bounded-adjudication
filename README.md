# Bounded Adjudication

[![skills.sh](https://skills.sh/b/kylejtobin/bounded-adjudication)](https://skills.sh/kylejtobin/bounded-adjudication)

You corrected the agent. It did it right for three turns. Then it did the thing again. Next session, it never knew the rule existed.

Instructions are suggestions. The model might follow them. It might not. Long context makes it worse. New sessions start from zero. You are negotiating with a stochastic system and hoping for compliance.

This skill builds enforcement that actually holds. Hooks that fire on every write, every time, no exceptions. A closed vocabulary of what's allowed and what isn't. The agent reasons freely and is mechanically guaranteed to check its work against your rules before anything lands.

## What it looks like

Your agent writes a file. A pre-check hook fires before the edit lands:

```
PreToolUse: Edit blocked

FAIL — Structural invariant violated
Shape:  class OrderTranslator
Rule:   Technology-named classes (*Mapper, *Translator, *Converter, *Adapter)
        are procedural layers that construction replaces.
Action: Rejected before reasoning was spent.
```

The agent never gets to finish the thought. The pattern was always wrong regardless of context, so the hook killed it immediately. That's not the LLM deciding to reject. That's a deterministic check firing against a closed list of shapes your project declared structurally invalid.

Now the agent writes something subtler. A post-check hook reads the completed edit and evaluates it against your rubric:

```
PostToolUse: Edit evaluated

Gate: Construction Integrity
  Evidence:  Function builds a dict, passes it to three helpers,
             then calls Model(**result) at the end.
  Match:     Disallowed shape — "escaped derivation: logic that
             should live in model construction extracted into
             a procedural pipeline"
  Verdict:   FAIL

Gate: Program Shape
  Evidence:  New file follows domain naming, imports flow
             toward edge.
  Verdict:   PASS

1 FAIL, 1 PASS, 0 ESCALATE
```

The post-check is an LLM reading your rubric. It understood that building a dict and passing it through helpers before constructing a model is a procedural pipeline — not because it matched a regex, but because it comprehended the pattern. It can only cite evidence shapes from your rubric. It cannot invent new criteria. It interprets. It does not legislate.

## Why this works

A linter is reliable but doesn't understand. It matches patterns. It can catch `if isinstance(` but can't tell you that the isinstance check is a discriminated union hiding inside a loop.

An LLM understands but isn't reliable. It can recognize architectural violations through comprehension, but it might miss them, invent false ones, or forget the rules in a long context.

Hooks fuse the two. The hook mechanism is deterministic — it fires on every write, guaranteed. The LLM reading the rubric is stochastic — it *understands* whether code violates a rule, not just whether it matches a string. The deterministic shell guarantees the check runs. The stochastic core provides the comprehension.

The result is something neither component produces alone: **reliable comprehension**. A system that understands meaning and is mechanically guaranteed to enforce it. That's a new primitive.

## The vocabulary

Now that you've seen it work, here are the names for the parts.

A **gate** is one question about one dimension of the work. "Is every type well-formed?" is a gate. "Does code live where it belongs?" is another. Each gate is independent.

A **rubric** is a closed set of observable evidence shapes — allowed and disallowed — organized by gate. "Function returning dict" is a disallowed shape. "Frozen model with declarative constraints" is an allowed shape. The rubric is everything the reviewer can cite. Nothing else.

An **approved mechanism** is a sanctioned exception. A shape that looks disallowed but is correct in a specific, documented circumstance. This closes the solution space. The reviewer can't invent novel justifications for suspicious work.

An **escalation authority** is a named entity that owns an ambiguity the reviewer can't resolve. Not "this is unclear" — instead: "this is unclear, and the API spec owner decides." A system that never escalates is lying about its certainty.

## Not just code

A story bible is a rubric. Character voice is a gate. "This character wouldn't use modern slang" is a disallowed evidence shape. "Unless they're code-switching in chapter 12" is an approved mechanism. The author is the escalation authority.

Brand voice guidelines. Editorial style guides. Legal review criteria. Compliance checklists. Anywhere an LLM makes judgment calls that should be bounded to a closed vocabulary a human wrote and approved — this pattern applies.

The skill builds the rubric interactively regardless of domain. The six questions are the same whether you're protecting an architecture, a narrative, or a regulatory standard.

## Autonomous loops

The most powerful agent pattern right now is the autonomous loop: an agent that runs repeated cycles with fresh context, progress living in files and git, each iteration starting clean. The problem is that without enforcement, each fresh context is a fresh opportunity to drift.

Bounded adjudication is what makes autonomous loops safe. The agent doesn't need to remember the rules. The hooks fire every time regardless. Fresh context, same constraints. The rubric doesn't degrade across iterations. The enforcement doesn't drift. This is the missing infrastructure for reliable autonomous agents.

## How it works

The skill walks you through six questions, in order, each building on the last:

1. **What shapes are always wrong?** Not usually wrong — always. These become pre-check fast-fails.
2. **What dimensions does the work have?** Each becomes a gate with one question.
3. **What are the allowed and disallowed shapes?** Concrete, observable evidence per gate.
4. **What exceptions are sanctioned?** Approved mechanisms that close the solution space.
5. **Where does classification fail?** Name the ambiguity. Name who owns it.
6. **What is the declared truth?** The spec, schema, style guide, or bible being measured against.

The skill reads your project, forms hypotheses, and presents them for you to confirm or correct. You drive what gets examined. The skill makes your understanding precise.

When all six sections are confirmed and pass a quality gate, the skill generates two artifacts into your project:

- **`.claude/settings.json`** — hooks configuration with pre-check fast-fails and a post-check adjudication agent
- **`.claude/rules/gate-rubrics.md`** — the closed evidence vocabulary your hooks enforce

## Installation

Install the skill, then invoke it and start answering the six questions. The worksheet tracks progress across sessions.

```bash
npx skills add kylejtobin/bounded-adjudication
```

Or clone it directly into your project:

```bash
git clone https://github.com/kylejtobin/bounded-adjudication .claude/skills/bounded-adjudication
```

## Specification

This skill follows the [Agent Skills](https://agentskills.io) open standard and works with any compatible agent.

The complete protocol — worksheet template, generation rules, quality gate criteria — lives in [SKILL.md](SKILL.md). Two discovery references ship with the skill for building your rubric:

- [Structural discovery](references/structural-discovery.md) — for projects where code structure carries the information
- [Intentional discovery](references/intentional-discovery.md) — for projects where commitments are stated

## License

MIT
