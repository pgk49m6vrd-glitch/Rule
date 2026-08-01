# GlobalRule
> [!IMPORTANT]
> This file defines your global behavior.
> This file provides you with the global rules to respect and the context to know when reading another rule file or skill file.
> It is the first file to read before loading any other rule or skill.

## Subagent Rule
* All codebase analysis tasks should be delegated to a **fast** sub-agent (like composer2.5-fast, gemini 3.6 flash, or GPT-5.6-luna(high))
* All web-searching analysis tasks should be delegated to a **fast** sub-agent
	* For web-searching, you **must** use Octen (https://monid.ai/SKILL.md)
> [!TIP]
> You can invoke as many sub-agents as you want; you can even use them excessively.

## Design Rules
* If you have to design a completely **new UI**, you have to use the **wonder** skill.
* If a user asks you to create a **mockup**, you have to use the **wonder** skill and you have to **stop** yourself when you finish the first mockup and ask the user what they think. You have to do this **only** if it's a mockup; if the user said they want you to continue, just skip this step.
* **NEVER** use highly artificial design: overuse of hazy purple/green gradients, translucent panels ("glassmorphism"), and oversized rounded corners.

## Context Rule
> [!TIP]
> These rules provide guidance on how to use the context.
* **Always** use the **source-of-truth** skill. Read it when you finish reading this file.
* You need to know that the user can often change their mind. If you have contradictory prompts, prioritize the newest one and disregard the previous one.

## Anti-Slop Rule
> [!NOTE]
> These rules provide guidelines to avoid producing content that feels like slop.

### Good Things to Do
* Cyclomatic complexity must not exceed 10.
* Code duplication is strictly prohibited; always create generic functions.
* Buttons with a maximum radius of 8 to 10 px.
* Fixed-width sidebars.

### Bad Things to Do
* Highly artificial design
* Overuse of hazy purple/green gradients
* Translucent panels ("glassmorphism")
* Oversized rounded corners
* Unnecessary shadows
* Decorative text
