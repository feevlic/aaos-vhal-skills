# Android 14 Automotive OS VHAL AI Instructions

This directory contains strictly validated, production-ready instructions for AI coding assistants (like GitHub Copilot, Cursor, Claude, or Codex) to help you build against the Android 14 Vehicle Hardware Abstraction Layer (VHAL). All properties, data types, and constraints map exactly to the canonical AOSP source.

## How to use it

1. Copy this directory into your project.
2. Point your AI assistant to the `SKILL.md` file. You can do this by:
   - Adding a reference in your `.cursorrules` or `.vscode/copilot-instructions.md`.
   - Uploading it to your Claude Project Knowledge.
   - Feeding it directly to your CLI agent.
3. Keep `SKILL.md` and `properties-reference.md` in the same directory. The AI relies on relative linking to resolve properties contextually.

## Why the files are split

You will notice that the core directives (`SKILL.md`) and the VHAL property definitions (`properties-reference.md`) are separated. This is an intentional design choice for modern LLMs:

- **Context Window Optimization:** The Android 14 VHAL contains over 220 complex properties. Injecting all of them into a single prompt wastes tokens and degrades the AI's memory for your actual code.
- **Progressive Loading:** By separating the registry into a reference document, AI agents can employ progressive loading or retrieval-augmented generation (RAG). The agent keeps the core coding patterns from `SKILL.md` in mind, but only reads `properties-reference.md` when a specific property lookup is required.
- **Latency and Accuracy:** Keeping the primary instruction file lean reduces AI response times and prevents hallucinations during general development tasks.
