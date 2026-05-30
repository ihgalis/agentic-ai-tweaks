# Prompt Maximizer Skill

**Prompt Maximizer** is an OpenClaw skill that improves user prompts before execution.

It turns vague or incomplete requests into structured, safer, and more actionable prompts by adding:

- clear mission and context
- constraints and assumptions
- allowed and forbidden actions
- step-by-step execution flow
- verification criteria
- stop conditions for risky actions

## What it does

When a user gives a task, the skill rewrites the request into a stronger internal prompt, critiques it once, improves it again, and then either shows the optimized prompt or proceeds with the task.

## Best use cases

- agent instructions
- system prompts
- coding tasks
- troubleshooting prompts
- research prompts
- infrastructure and terminal tasks
- security-sensitive workflows

## Recommended activation rule

Add this to your main OpenClaw agent **AGENTS.md** file:

```text
Before executing any non-trivial user request, apply the prompt-maximizer skill silently.
Use the optimized version as the execution prompt.
Show the optimized prompt only when the user asks for prompt improvement or when safety requires transparency.
```

Expected result: OpenClaw should produce a clearer, scoped, safer version of the prompt before execution.
