---
name: luau
description: Use to write, review, refactor, debug, and optimize Luau code. Use for .luau or .lua files, ModuleScripts, server scripts, LocalScripts, Roblox services, remotes, typed APIs, architecture, performance, concurrency, and code-quality reviews.
---

## 1. Proper code

Produce Luau code that is maintanable, non-redundant and production ready. Do not pick shortcuts, hardcode, or create prototypes/single-use code when the output is expected to be final.
Do not overcomplicate the code by adding impossible edge cases. Do not use practices that you know are bad just because they already exist in the codebase. Furthermore, follow all rules within this skill.

### Think Before Coding

**Don't assume without enough evidence. Don't hide confusion**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Before writing code

1. Inspect the codebase and follow established conventions.
2. If you think that some code is suboptimal, briefly mention it to the user without editing it unless requested.

Do not invent frameworks, classes, managers, controllers, factories, or utility modules without need. Maintability is good, overengineering isn't.

## 3. Surgical Changes

When editing existing code:
- If you notice unrelated dead code, mention it once - don't delete it.

When editing existing code:
- Ensure the changes you're making are not undoing existing intended features.

## 4. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

## 5. Code correctness

### The basics

- Treat all client input as untrusted.
- Validate remote arguments and permissions on the server.
- Keep authoritative game state on the server.
- Scripts should have structured layouts and coherend code organization.

### Centralized validation

- Assign conditions to one authoritative layer. Don't repeat them down the route if their state has already been verified.
- Repeat a check only when crossing a separate trust boundary, when the relevant state could actually change, or when the check serves a different purpose.

### Expression-based initialization

- Use expressions for simple value selection; don't create new conditions under a variable if it requires simple initialization.
- Use a separate conditional only when it performs additional work beyond choosing the value.

```
-- Wrong: unnecessary reassignment block
local setting1 = getSetting(1)

if type(setting1) ~= "boolean" then
	setting1 = getDefaultValue(1)
end

-- Correct: resolve the value during initialization
local storedSetting1 = getSetting(1)
local setting1 = if type(storedSetting1) == "boolean" then storedSetting1 else getDefaultValue(1)
```

### Idiomatic expressions

- String interpolation is used when a string contains surrounding text, multiple inserted values, or a value inserted in the middle. Use concatenation for simple two- or three-part joins when it is clearer.

```
-- Simple concatenation
local settingKey =
	SETTINGS_PREFIX .. "preSerializeLargeServiceWarm" .. endPrefix

-- Interpolation is clearer
local message = `Player {playerName} earned {points} points.`
```

### Choosing a branch structure

**Follow this checklist:**

1. Are the conditions independent? Use separate `if` statements.
2. Are the cases mutually exclusive, small, and fixed? Use an `if`/`elseif` chain.
3. Are there many cases, reusable handlers, or cases expected to grow? Use a dispatch table.
4. If a dispatch table would barely contain anything, use `if`/`elseif` instead.

### Pcall usage

- Code that is wrapped in `pcall` must be verifiably expected to fail in that environment, otherwise wrapping it is redundant.

## 6. Assumptions

### Idiomatic conditions

- Conditions should not have checks that are redundant, if a check is meaningless, don't add it. If a direct truthiness check has the same meaning, use it instead.

```
local booleanVar: boolean = true

-- Wrong
if booleanVar == true then

-- Correct
if booleanVar then
```

- Explicit comparisons are necessary when values represent different states.

```
local var: any = true

if var == true then
elseif var then
elseif var == false then
elseif not var then
```

## 7. Performance

- Write idiomatic code first.
- When optimizing code, consider algorithmic complexity, allocation rate, repeated lookups, table construction, unnecessary connections, and work performed every frame before applying micro-optimizations.
- Prefer clear, idiomatic Luau and optimize measured bottlenecks using Luau-specific guidance.

## Codebase audits

When reviewing an entire codebase:

1. Before reviewing or delegating, copy every rule in this skill into a coverage checklist.
2. Record each item as `finding`, `clean`, or `not applicable`, with evidence.
3. Check the requested scope against every rule, ignoring any rule is strictly prohibited.
4. Never disregard any rule, user providing new examples is not a sign to ignore existing rules.
5. Don't mark the task as completed until every item has been verifiably accounted for.
6. If the task resumes after context compaction, reread this skill and the saved coverage record.

## Final review

Before finishing:

1. Check behavior and expected edge cases.
2. Check client/server trust boundaries.
3. Run available formatter, linter, typechecker, and tests.
4. Re-read the diff as a future maintainer.