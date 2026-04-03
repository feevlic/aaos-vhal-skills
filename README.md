> [!NOTE]
> This repository contains the official VHAL Properties (DEFAULT / SYSTEM) of Android 14 and Android 15 defined by Google. Be aware that OEMs may also develop new VENDOR Properties, which may or may not be available for 3rd Party Apps.

# VHAL Skills for Android Automotive OS

A massive thank you to [@google](https://github.com/google) for loving open source. Without your documentation, this repository wouldn't exist. Keep being awesome!

## Structure

- [**aaos-vhal-skills**](./aaos-vhal-skills/): Holds the standard AOSP properties.
  - [**android14-release**](./aaos-vhal-skills/android14-release/): Android 14 rules and properties.
  - [**android15-automotiveos-release**](./aaos-vhal-skills/android15-automotiveos-release/): Android 15 rules and properties.
- [**oem-vhal-skills-template**](./oem-vhal-skills-template/): A starter kit for auto makers (OEMs) to document their own custom properties.

## How to use

1. Pick the folder for the Android version you're building against.
2. Point your IDE tool (Copilot, Cursor, whatever) to the `SKILL.md` file in that folder.
3. Keep `SKILL.md` and `properties-reference.md` together. The AI reads the reference file when it needs to look up a specific property, so they have to stay bundled.

## Future Versions

If Google ever decides to bless us with a new Android OS version and a completely revamped VHAL, I'll eventually get around to releasing a new skill for it.
