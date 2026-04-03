# OEM VHAL Skill Instructions

This folder contains the instructions on how to create your own skill file if you are working at an OEM and want to improve the speed of internal or external contributors taking care of the Application Layer or any Service/Manager in the Android Framework that communicates with your individual set of Properties.

## How to Use

1. Browse the structure of the `aaos-vhal-skills` directory to get a feel for how to lay out the files.
2. Add the vendor or system properties your OEM supports into the `properties-reference-template.md` file and rename it to `properties-reference.md`.
3. Adjust `SKILL-template.md` to your needs, either by adding details on how to integrate the vendor properties into the Application via an OEM-SDK or by documenting how to include your provided libraries.
4. Update the code structure rules and guidelines to follow your OEM-specific standards.
5. Rename `SKILL-template.md` to `SKILL.md`. (Double check that it references the `properties-reference.md` file).

## File Organization

You will notice that the core directives (`SKILL-template.md`) and the VHAL property definitions (`properties-reference-template.md`) are separated. This is an intentional design choice to keep the files focused, but it is ultimately up to you how you want to structure them.
