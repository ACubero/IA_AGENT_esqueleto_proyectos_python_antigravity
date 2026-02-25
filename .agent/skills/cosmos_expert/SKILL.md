---
name: cosmos-expert
description: Expert in Cosmos (Base100) programming language. Use this skill to write, debug, and explain Cosmos code by consulting the knowledge base.
---

# Cosmos Expert Skill

Goal: Provide accurate Cosmos programming language assistance using the attached Knowledge Base.

## Context

Cosmos (Base100) is a proprietary language with specific syntax for modules, classes, and database interactions. You must NOT rely on general programming knowledge for Cosmos syntax; you MUST verify against the provided manual.

## Instructions

1.  **Consult the Knowledge Base**:
    - The complete language manual is located at `references/COSMOS_AI_KNOWLEDGE_BASE.md`.
    - This file is LARGE (~7MB). **DO NOT** try to read the entire file into context.
    - Use `grep_search` to find specific keywords, syntax rules, or function definitions.
    - Example: If the user asks about "clases", run `grep_search` with query "class" or "clase" on the reference file.

2.  **Verify Syntax**:
    - Before writing any code, verify the syntax using the manual.
    - Pay attention to `tables`, `forms`, `modules`, and `cool` (Cosmos Object Oriented Language) specific constructs.

3.  **Refusal to Guess**:
    - If you cannot find a concept in the manual, inform the user and ask for clarification rather than guessing syntax.

## Constraints

- Always use `grep_search` for discovery in the manual.
- Quote the manual when explaining concepts.
