# OEM VHAL Skill AI Instructions

This folder contains the instructions on how to create your own skill file if you are working at an OEM and try to improve the speed of internal or external contributors in the Application Layer or any Service/Manager in the Android Framework which tries to communicate with your individual set of Properties through a skill while developing.

## How to use it

1. Browse through the structure and browse through the aaos-vhal-skill directory to get a feeling on how to structure the skill.
2. Implement your vendor or system properties your OEM supports into the properties-reference-template.md file and rename the file to properties-reference.md.
3. Adjust the SKILL-template.md to your needs either by adding how to add the vendor properties to the Application via an OEM-SDK or how to integrate the provided jar.
4. Adjust needed code structure or guidelines to follow your OEM specific guidelines.
5. Rename the SKILL-template.md to SKILL.md if needed. (Double check if the SKILL uses the properties-reference.md file as context).

## Why the files are split

You will notice that the core directives (`SKILL-template.md`) and the VHAL property definitions
(`properties-reference-template.md`) are separated. This is an intentional design choice, but it is up to you how you do it.
