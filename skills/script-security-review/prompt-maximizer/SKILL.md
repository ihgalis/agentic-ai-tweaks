---
name: prompt-maximizer
description: >
  Always improve user prompts before executing them. Use this skill whenever the user asks to write,
  improve, execute, analyze, plan, code, search, automate, or delegate a prompt/task. The skill rewrites
  the user's raw request into a high-quality, explicit, structured prompt, critiques it, improves it once
  more, and then either presents the optimized prompt or proceeds using the optimized version.
---

# Prompt Maximizer Skill

## Purpose

This skill transforms any user prompt or task request into a clearer, more complete, more executable prompt before the agent acts on it.

Use this skill as a pre-execution gate.

The goal is not to make prompts longer for their own sake. The goal is to make them:
- clearer
- safer
- more specific
- more complete
- better scoped
- easier for an agent to execute
- less ambiguous
- easier to verify

## When to Use

Use this skill whenever the user provides:
- a prompt they want improved
- a task they want the agent to execute
- an instruction for another agent
- a system prompt
- a coding request
- a research request
- an automation request
- a command-generation request
- a troubleshooting request
- a content-generation request
- a planning request
- any vague or underspecified instruction

Default assumption: if the user gives a prompt or asks the agent to do something, this skill should run first.

## Do Not Use When

Do not run the full verbose optimization process when:
- the user explicitly says: "do not improve the prompt"
- the user asks for a very small direct answer
- optimizing would delay an urgent safety-critical answer
- the user is asking for a simple factual answer and no prompt will be reused
- the user has already provided a strict final prompt and only wants execution

Even then, silently apply light clarity improvements when useful.

## Core Workflow

For every eligible prompt, follow this five-step workflow.

### Step 1: Capture the Raw Prompt

Identify the user's actual instruction.

Preserve:
- original goal
- constraints
- tone
- language
- target model or agent
- tools mentioned
- security boundaries
- output format
- success criteria

If something is missing, infer reasonable defaults instead of asking too many questions.

### Step 2: Diagnose Prompt Weaknesses

Check for:
- vague goal
- missing context
- missing constraints
- missing output format
- missing role
- missing examples
- missing success criteria
- hidden assumptions
- unsafe or overly broad tool permissions
- unclear order of operations
- lack of verification step
- lack of stop conditions
- lack of error handling
- no distinction between planning and execution

### Step 3: Produce an Optimized Prompt

Rewrite the prompt using this structure:

1. Role
2. Mission
3. Context
4. Inputs
5. Constraints
6. Allowed actions
7. Forbidden actions
8. Process
9. Verification
10. Output format
11. Escalation / clarification rules

The improved prompt should be directly usable by another agent.

### Step 4: Self-Critique the Optimized Prompt

Ask internally:

- Is the goal explicit?
- Are tool permissions clear?
- Are dangerous actions gated?
- Does the prompt prevent unnecessary verbosity?
- Does it include verification?
- Does it define output format?
- Does it handle ambiguity?
- Does it reduce hallucination risk?
- Does it preserve the user's intent?

Then improve the prompt one more time.

### Step 5: Output or Execute

If the user asked for a prompt:
- show the optimized prompt
- show a short explanation of what changed

If the user asked the agent to perform the task:
- use the optimized prompt internally
- briefly mention that the request was normalized
- proceed with the task
- do not dump the full optimized prompt unless useful

## Mandatory Output Format When User Asks For A Prompt

Use this format:

```text
# Optimized Prompt

[final optimized prompt]

# Why This Is Better

- [short explanation]
- [short explanation]
- [short explanation]

# Optional Compact Version

[shorter version if useful]