# Plan: Confluence Skill

## Context

There is already a local skill: `secure-confluence-cli`.

That means the practical path is probably not "invent a Confluence skill from scratch", but one of these:
- audit and improve the existing secure skill
- split it into a stricter secure skill plus a simpler day-to-day skill
- keep one skill and tighten its docs, scripts, and validation

## Goal

Create a reliable skill for working with Confluence that:
- is safe by default
- supports useful read flows first
- only enables writes with explicit approval
- stays compact and easy to maintain

## Recommended Direction

Start from `secure-confluence-cli` and improve it instead of creating a totally new skill.

Reason:
- the security model is already defined
- write restrictions already exist
- there is already a wrapper script and supporting references
- this reduces duplicate logic and policy drift

## Scope

The skill should cover:
- listing spaces
- search
- finding pages by title
- reading page content
- page metadata and children
- export flow
- controlled create/update flow with explicit confirmation

The skill should not allow by default:
- delete
- bulk destructive actions
- silent writes
- token leakage into notes, scripts, or workspace files

## Work Plan

1. Audit the current skill
- Read `SKILL.md`
- Inspect `scripts/secure_confluence.py`
- Review `references/` for anything too long, stale, or duplicated
- Check whether the current description is clear enough to trigger correctly

2. Decide the skill shape
- Option A: keep one secure skill and improve it
- Option B: keep `secure-confluence-cli` for sensitive work and add a second lighter skill for simple read-only usage
- Default recommendation: Option A unless there is a strong need for two distinct workflows

3. Tighten the trigger and frontmatter
- keep `name` and `description` concise
- make the description clearer about read-only by default and approval before writes
- avoid stuffing general Confluence advice into `SKILL.md`

4. Refine the workflow
- make read-only commands the obvious first path
- define the approval flow for writes in one short sequence
- show exact command examples only where they reduce ambiguity
- remove repeated or generic instructions

5. Harden the script layer
- confirm blocked actions really fail closed
- confirm allowlist checks happen before write execution
- confirm write enable flags are required together, not loosely
- make errors readable and specific

6. Improve references
- move long policy explanation to `references/`
- keep setup, audit, and write-playbook docs separate
- add one short troubleshooting reference if missing

7. Add validation
- YAML frontmatter validation
- smoke-test the wrapper for safe read commands
- negative tests for blocked write/delete actions
- optional fixture-based tests if the script is growing

8. Test real usage
- search for a page
- read a page by id
- list child pages
- simulate a write request and verify the approval gate blocks it until explicitly enabled

9. Final cleanup
- trim `SKILL.md` so it stays lean
- keep brittle command syntax, security rules, and approval steps
- document only what future-you will actually need

## Deliverables

- updated `secure-confluence-cli/SKILL.md`
- improved `scripts/secure_confluence.py` if needed
- cleaned `references/`
- small validation routine or test notes
- one example workflow for read
- one example workflow for approved write

## Open Questions

- Do you want one secure Confluence skill only, or two separate skills:
  one strict secure skill and one lighter read-only helper?
- Should the skill stay purely CLI-based, or later grow optional Notion/Obsidian handoff flows for drafts and exports?
- Do you want allowlisting to be mandatory or optional by default?

## Suggested First Task

Do a focused audit of the existing `secure-confluence-cli` skill and turn that into a concrete improvement checklist before creating anything new.
