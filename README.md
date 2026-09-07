Minecraft Resource Pack Builder

Build complete, ready-to-install resource packs for Minecraft Java or Bedrock Edition. The output is always a .zip file the user can drop directly into their resource packs folder.

Workflow
Step 1: Clarify scope (ask only if not obvious)

Before building, confirm these details if the user hasn't already specified them:

Edition — Java or Bedrock? Default to Java if not specified (it's far more common). Mention the assumption so the user can correct.
MC version — required for picking the right pack_format. If unclear, ask. Don't guess silently.
Pack name and description — short, used in the in-game pack list.
What to retexture/replace — which blocks, items, sounds, etc.

If the request is small and obvious ("retexture diamond as obsidian for 1.21"), don't ask — just build it.

Step 2: Look up the correct pack_format

This is the most common source of broken packs. Each MC version requires a specific pack_format number, and packs with the wrong number won't load (or will show a "made for older/newer version" warning).

Read references/pack-formats.md to get the right number. Don't guess from memory — the table changes with every MC release.

Step 3: Read the edition-specific reference
Java Edition → read references/java-edition.md for folder structure, pack.mcmeta format, model/texture conventions.
Bedrock Edition → read references/bedrock-edition.md for manifest.json, UUID requirements, and RP-specific structure.
Step 4: Generate textures

For each texture the user wants:

If the user provides a reference image or specific pixel-art instructions, follow them.
If the user describes a theme ("medieval", "neon cyberpunk", "obsidian-themed tools"), generate the PNGs programmatically with Pillow. See references/texture-generation.md for patterns.
If generation isn't feasible (e.g., highly detailed textures requiring artistic input), create simple placeholder PNGs at the correct dimensions (usually 16×16) and clearly tell the user which files are placeholders they should replace.

Default texture size is 16×16 (vanilla resolution). Use 32×32, 64×64, or higher only if the user asks for HD.

Step 5: Assemble the pack

Build the folder structure in /home/claude/<pack-name>/, then zip it. The zip must contain the pack files at the root of the archive, not nested in a subfolder — this is a frequent mistake that makes packs invisible to Minecraft.

Correct (Java):

my-pack.zip
├── pack.mcmeta          ← at root
├── pack.png             ← optional icon, 64×64 or 128×128
└── assets/
    └── minecraft/
        └── textures/...

Wrong:

my-pack.zip
└── my-pack/             ← extra folder, breaks loading
    ├── pack.mcmeta
    └── assets/...

Use the zip command or Python's zipfile module. Example:

bash
cd /home/claude/my-pack && zip -r ../my-pack.zip . -x "*.DS_Store"
Step 6: Validate before delivering

Before presenting the file, sanity-check:

pack.mcmeta (Java) or manifest.json (Bedrock) is valid JSON.
All texture PNGs are the expected dimensions.
File paths use / (forward slashes), even on Windows hosts.
Texture filenames match vanilla names exactly when overriding (case-sensitive: diamond_sword.png, not Diamond_Sword.png).
The zip has files at root, not in a subfolder.
Step 7: Deliver

Move the final .zip to /mnt/user-data/outputs/ and use present_files to share it. In the response, briefly tell the user:

Which MC version it's built for
How to install (drop into resourcepacks/ folder for Java, or resource_packs/ for Bedrock)
Which textures (if any) are placeholders they should customize
Common request patterns

"Retexture X as Y" — single or small group of textures. Generate, place at assets/minecraft/textures/<category>/<name>.png, zip.

"Make a [theme] pack" — coherent set of textures around a theme. Pick a representative subset (don't try to cover all 1000+ vanilla textures unless asked). Generate matching textures programmatically.

"Change the [sound] sound" — sound replacement. Sounds go in assets/minecraft/sounds/ as .ogg files, with a sounds.json mapping. See references/java-edition.md.

"Custom font" — provide font in assets/minecraft/font/default.json or a custom font file. See references/java-edition.md.

"Translate Minecraft to X" — language pack. Goes in assets/minecraft/lang/<code>.json (Java) — note: language packs only override existing translations, they don't add new locales without extra setup.

"Custom model" — replace an item or block model. JSON model files go in assets/minecraft/models/. See references/java-edition.md for model JSON structure.

Things to never do
Never invent a pack_format number. Always check the table in references/pack-formats.md.
Never zip with a subfolder at root. Pack files must be at the zip's root level.
Never use uppercase or spaces in texture filenames when overriding vanilla — they're all lowercase with underscores.
Never mix Java and Bedrock structures in the same pack. They're incompatible.
Never skip pack.png/pack icon silently — it's optional but the pack looks broken without one. Generate a simple 64×64 default if the user doesn't provide one.
Reference files
references/pack-formats.md — MC version → pack_format table for both editions. Always read this before building.
references/java-edition.md — Java pack structure, pack.mcmeta, models, sounds, fonts, lang.
references/bedrock-edition.md — Bedrock pack structure, manifest.json, UUIDs, texture mapping JSON files.
references/texture-generation.md — Pillow patterns for generating pixel-art textures programmatically.
Asset templates
assets/pack-mcmeta-template.json — minimal valid Java pack.mcmeta.
assets/manifest-template.json — minimal valid Bedrock manifest.json.
assets/pack-icon-default.png — fallback 64×64 pack icon.
