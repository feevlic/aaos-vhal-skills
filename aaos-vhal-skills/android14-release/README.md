# Android 14 Automotive OS VHAL Instructions

This directory contains instructions for coding assistants (like GitHub Copilot, Cursor, Claude, or Codex) to help you build against the Android 14 Vehicle Hardware Abstraction Layer (VHAL). All properties, data types, and constraints map to the AOSP source.

This skill covers 223 properties.

## Structure

- [**properties-reference.md**](./properties-reference.md): Holds the standard AAOS 14 properties.
- [**SKILL.md**](./SKILL.md): Android 14 Skill

## How to Use

1. Copy this directory into your project.
2. Point your assistant to the `SKILL.md` file. You can do this by:
   - Adding a reference in your `.cursorrules` or `.vscode/copilot-instructions.md`.
   - Uploading it to your Claude Project Knowledge.
   - Feeding it directly to your CLI tool.
3. Keep `SKILL.md` and `properties-reference.md` in the same directory. The tools rely on relative linking to resolve properties.

## File Organization

You will notice that the core directives (`SKILL.md`) and the VHAL property definitions (`properties-reference.md`) are separated. This is an intentional design choice:

- **Token Limits:** The Android 14 VHAL contains over 220 complex properties. Injecting all of them into a single prompt wastes space and degrades the tool's memory for your actual code.
- **On-Demand Loading:** By separating the registry into a reference document, the tools can look up information as needed. The core coding patterns from `SKILL.md` are always kept in mind, but `properties-reference.md` is only read when a specific property lookup is required.
- **Latency and Accuracy:** Keeping the primary instruction file lean reduces response times and prevents mistakes during general development tasks.
