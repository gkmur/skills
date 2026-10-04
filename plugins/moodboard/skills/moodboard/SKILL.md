---
name: moodboard
description: Apply the user's saved visual taste - their own reference library of images, boards, or screenshots - as creative influence on whatever is being designed or built - a UI, landing page, component, brand, deck, or image prompt. Has an adjustable influence dial, from strictly mirroring a named reference to loosely seasoning a build with their whole taste profile. Use whenever the user mentions their boards, saved references, moodboard, Pinterest, "my taste", or pulling inspo/inspiration from things they've saved; asks for a moodboard or a design brief grounded in their references; or wants the aesthetic of anything they're building to feel like them instead of generic AI defaults - including mid-build ("make this feel more like my saved editorials"). Also use for first-time setup when the user wants to point the skill at their reference library. NOT for critiquing existing designs.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
---

# Moodboard

Give the AI the user's saved visual taste as a creative mind to draw on, then apply it to whatever is being built. Do not make them pick images, answer questionnaires, or approve candidate rounds. Infer the influence level, load the right layer, translate it to the target medium, apply it.

## The taste profile

Lives at `~/.claude/moodboard/taste-profile.md`. Its header records where the user's reference library is and when it was last compiled; the body is a distilled read of their taste. If it exists, trust it.

If it does not, run setup once:
1. Ask where their references live (one question, then go). A folder of images, a Pinterest export, a screenshots dump, subfolders-as-boards.
2. Read the library, preferring caption or metadata sidecars over vision, and thumbnails when they exist. For big libraries sample every collection, not every image.
3. Compile the profile: `## Corpus taste profile` (palette tendencies with real hexes, materials and textures, composition habits, photographic mood, era and subculture signals, typography sensibility, anti-patterns derived from what the library consistently avoids), then `## Collections` (one block each: what it is, its DNA, dominant hexes, "use for: ..."). Mark low-signal collections (memes, utility screenshots) in one line.
4. Stamp the header with library path, layout notes, and compile date. Offer to recompile when the library has visibly grown past the stamp.

## The dial

Infer the level from the phrasing; default **guided**. The user can move it mid-flight ("stricter", "looser", "just use these two").

| Level | They say | Load | References act as |
|---|---|---|---|
| **strict** | "use this image / this board exactly", names specific references | The named images and their metadata | Hard constraints: palette from those exact references, composition and texture mirrored closely |
| **guided** (default) | "moodboard for X", "make it feel like my editorials", "pull inspo for this" | The 1-3 relevant collections (route via the profile's "use for" lines); look at the few images that drive decisions | Strong direction: distill DNA fresh, cite the driving references |
| **ambient** | "use my taste", "my saved stuff", anything broad | The taste profile only | Seasoning: build freely, break ties toward the profile |

## Flow

1. **Read the ask:** what is being built plus the dial level. Ask only if you cannot infer both, one question max.
2. **Load the layer** per the table. For guided, grep caption files when the ask is thematic ("warm wood", "harsh flash") rather than collection-shaped.
3. **Read the driving references.** When specific images carry the direction (strict always, guided usually), read `references/reading-lenses.md` and extract principles through its lenses, not just a palette. Each can transfer at a different literalness (quote, translate, abstract, invert).
4. **Translate to the target medium.** Transfer the attribute, not the artifact: an archive editorial lends its contrast curve and severity, not a model on the landing page. Palette and contrast carry over directly; material and texture become surface treatment (grain, border weight, shadow character); silhouette and structure become layout; styling density becomes spacing; photographic mood becomes contrast curve and motion feel; era and subculture become a typography direction, not a font pick; what the references never do becomes anti-patterns derived from the actual library.
5. **Apply.** Mid-build, fold the influence into the work (tokens, CSS, copy tone, image prompt, slide styling) with no file unless asked. For a brief ("write a moodboard", "give me a brief"), fill `assets/moodboard.template.md` into `MOODBOARD.md` in cwd. For the palette run `node scripts/build-palette.mjs "#hex" "#hex" ...` with the driving references' dominant hexes; it assigns bg, surface, ink, and accent by luminance and chroma and emits a W3C token block. Render only the roles it returns (a monochrome selection legitimately lacks accent or surface). Never invent a hex.

## Honesty

- If the user's taste does not cover the ask, say so and build without forcing off-brand references.
- Cite the specific references that drove decisions (path or URL).
- ASCII only, no emojis, in anything written to files.
