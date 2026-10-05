---
name: moodboard
description: Apply the user's own saved visual references (image boards, a Pinterest mirror, photo albums, screenshots) as creative direction for whatever is being made - a UI, page, brand, deck, garment, print, room or image prompt. Use when they mention their boards, saved references, moodboard, Pinterest, their photos as inspiration, or "my taste"; when they want something to feel like them; when they ask for a moodboard or a brief grounded in their references; and to set up or refresh their taste profile.
when_to_use: Also mid-build ("make this feel more like my saved editorials", "stricter", "looser", "show me a few ways to combine these"). Not for critiquing a finished design, not for finding or showing one specific photo, and not for work where the look does not matter.
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

The user's saved references are the source of their taste. Read them, pull out principles that survive leaving the image, and recombine those principles in the medium at hand. The user should never have to pick images, fill in a questionnaire, or approve candidate rounds.

## Profile status

```!
head -12 ~/.claude/moodboard/taste-profile.md 2>/dev/null || echo "No taste profile yet. Run setup."
```

If the block above is empty or reads as disabled, read the first lines of `~/.claude/moodboard/taste-profile.md` yourself.

## The taste profile

It lives at `~/.claude/moodboard/taste-profile.md` and has four parts.

- **Header.** One line per source, with where it lives, how it is laid out, and when it last changed, plus the date the profile was compiled.
- **`## Corpus taste profile`.** What repeats across every source, covering palette with real hexes, materials, composition habits, light and mood, era and subculture, type sensibility, and what the references never do.
- **`## Collections`.** One block per board, album or folder, with what it is, its character, its dominant hexes, and a "use for" line. Low-signal collections (memes, utility screenshots) get one line.
- **`## Principles`.** The user's own values, in their words. These decide whether a combination is right for them, which the images alone cannot.

Trust the profile when it exists and is current.

## Setup and refresh

Run setup when there is no profile. Refresh when a source changed after the compile date in the header, or when the user asks.

1. **Sources.** Ask once where the references live. There can be several, such as image folders with subfolders as collections, a board mirror with caption files, or a photo library reached through another tool or skill. For a photo library, compile only the albums or favorites the user names, since a camera roll is mostly not taste.
2. **Read.** Prefer captions and metadata sidecars, then thumbnails, then originals. For a big source, sample every collection.
3. **Compile** the corpus read and the collections. Derive the anti-patterns from what the sources avoid.
4. **Principles.** Ask the user for them, and write down what they say. Images cannot supply these, so leave the section marked empty when the user skips it.
5. **Stamp** the header with each source and the compile date. On a refresh, say in two or three lines what moved since the last compile.

## The dial

Infer the level from the phrasing and default to guided. The user can move it mid-build.

| Level | They say | Load | References act as |
|---|---|---|---|
| **strict** | "use this image exactly", names specific references | The named images and their metadata | Hard constraints, with palette, composition and texture mirrored closely |
| **guided** | "moodboard for X", "make it feel like my editorials" | The one to three relevant collections, and the few images that drive decisions | Strong direction, with the driving references cited |
| **ambient** | "use my taste", anything broad | The profile only | Seasoning, where ties break toward the profile |

## Flow

1. **Read the ask** for what is being made, in which medium, and at which level. Ask one question only when you cannot infer these.
2. **Load the layer** from the table. For a thematic ask ("warm wood", "harsh flash"), grep the caption files.
3. **Read the driving references** through `references/reading-lenses.md` whenever specific images carry the direction. End each look with a principle that still makes sense away from the image.
4. **Choose collections far from the target medium** when you can. A garment or a room says more about a web page than another web page does, because it has to be translated.
5. **Translate.** Carry the attribute across, and leave the artifact behind. Palette and contrast carry directly. Material becomes surface. Silhouette and structure become layout or cut. Density becomes spacing. Light and mood become contrast curve, motion and tone. Era becomes a type or styling direction. What the references never do becomes the anti-patterns.
6. **Combine** (next section).
7. **Check against `## Principles`.** A combination that looks right and breaks one of them is wrong for this user. Say which principle decided a close call.
8. **Apply.** Mid-build, fold the influence into the work with no extra file. For a brief, fill `assets/moodboard.template.md` into `MOODBOARD.md` in the working directory.

## Combinations

One set of references supports many results, and the range comes from two choices, which references drive and how literally each attribute moves (quote, translate, abstract, invert, all defined in the lenses file).

When the ask is open, or the user asks for options, give two or three combinations. Each has different drivers or a different mix of moves, and each is named by what it bets on ("the editorials' severity on the prints' color"). Averaging every reference into one result produces the generic middle, so pick drivers and let the rest go.

The same references applied twice should give different work.

## Palette

Roles come from the script, and hexes come from the references.

```bash
node "${CLAUDE_PLUGIN_ROOT}/skills/moodboard/scripts/build-palette.mjs" "#hex" "#hex" ...
```

It assigns bg, surface, ink and accent by luminance and chroma and prints a W3C token block. Use only the roles it returns, since a monochrome selection has no accent.

## Honesty

- When the user's references do not cover the ask, say so and build without forcing them in.
- Cite the references that drove decisions, by path or URL.
- Files this skill writes are ASCII.
