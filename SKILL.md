---
name: media-template-production
description: "Use when generating media from templates. Preserve layers."
version: 1.0.0
author: Giovanni Alvarez (CodeMonkeyG)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [media, templates, svg, inkscape, instagram, social-media, image-assets]
    related_skills: [obsidian, claude-design, hermes-agent]
---

# Media Template Production

Use this skill to turn a supplied media-template directory into a verified set of branded assets while preserving the designer's template decisions.

## Core Principle

Treat the user's SVG template as the source of truth. Do not redesign the template unless the user explicitly asks for a redesign.

Preserve:

- Canvas dimensions
- Font families and weights
- Font sizes
- Text coordinates and alignment
- Main-text and shadow-text layers
- Filter definitions and shadow settings
- Layer IDs and labels
- Background crop and placement
- Colors, opacity, and spacing

The job is to replace content and backgrounds, not to substitute a new visual system.

## When to Use

Use this skill when the user supplies SVG/JPG media templates and wants branded social, presentation, flyer, or image assets generated from them. Load it before inspecting the template directory or replacing text and backgrounds.

## Inputs

A typical input directory contains:

- One or more JPG/PNG background files
- A title-slide SVG template
- A follow-slide SVG template
- Other slides that the user may define as part of the the request
- Optional additional SVG templates for specific slide types
- Optional source copy from an Obsidian note or Master Kanban board

Resolve the exact directory path before acting. Do not assume capitalization or folder names.

## Standard Workflow

### 1. Inspect the directory

Use filesystem tools to list the directory and identify:

- Background image filenames and dimensions
- SVG template filenames and dimensions
- Which template is intended for the title slide
- Which template is intended for subsequent slides

Use `identify` or an equivalent image tool to verify dimensions.

### 2. Inspect the SVG templates

Read the SVG structure or search it for:

- `font-family`
- `font-size`
- `font-weight`
- `filter`
- `text-anchor`
- `id`
- `data-name`
- `tspan`

Build a content map by matching each visible main-text object with its corresponding shadow object. Common patterns include:

- `Main_Text` and `Main_Text_Shadow`
- `Logo` and `Logo_Shadow`
- Paired text elements with identical coordinates and font settings

Never replace only the visible text while leaving the shadow text unchanged.

### 3. Read the source content

If the user references an Obsidian Kanban or note:

- Read the exact Kanban file first.
- Follow the linked note to obtain the full caption and slide breakdown.
- Use the note's slide order unless the user specifies another order.
- Keep claims and wording grounded in the source note.

Do not invent credentials, prices, guarantees, service claims, or local facts.

### 4. Map copy into the template

Replace the text in both paired layers. Preserve the original:

- Font size
- Font family
- Font weight
- Fill color
- Transform matrix
- Coordinates
- Text anchoring

If copy is too long, prefer this order:

1. Split it across existing lines.
2. Shorten the wording without changing the meaning.
3. Use an additional existing text line if available.
4. Ask the user before changing font size or layout.

Do not allow text to run outside the canvas. A line that clips at either edge is a failed output.

### 5. Replace backgrounds

When a template embeds a background as a base64 data URI:

- Replace only the image data URI.
- Preserve the image element's transform, dimensions, clipping, and aspect-ratio settings.
- Use the matching background for each slide in sequence.

If a template links to a local background and the renderer does not load it reliably, embed the image as a base64 data URI in the generated SVG. Keep the output self-contained.

### 6. Create output masters

Use descriptive, stable names:

```text
phase2-slide-01-title.svg
phase2-slide-02-follow.svg
phase2-slide-03-follow.svg
```

The generated SVG is the editable master because it preserves the template's text and shadow structure.

If an intermediate revision is necessary, use a suffix such as `-v2` or `-v3`, then copy the verified revision to the stable final filename.

### 7. Render outputs

Use Inkscape to render each SVG to PNG. Use the original SVG dimensions; do not resize during export.

The generated SVG is the editable master. The PNG is the final flattened image asset for posting, sharing, or importing into other tools.

Recommended outputs per slide:

```text
slide.svg   # editable master with preserved SVG layers
slide.png   # final flattened image/export
```

### 8. Verify every slide

Run technical checks:

- File exists
- SVG parses or renders successfully
- PNG has the expected dimensions
- Background is visible
- Main and shadow text are both present
- No text is clipped
- Company footer is readable
- Font settings match the template

Use visual inspection for every rendered slide, not only the first one. Pay special attention to:

- Long centered headlines
- Footer logos
- Lines near the left or right margin
- Multi-line CTA text
- High-contrast backgrounds behind text

If a slide fails visual inspection, correct the copy wrapping or layer mapping before reporting completion.

## Tooling Patterns

Typical local tools:

```bash
identify input.jpg
inkscape input.svg --export-filename=output.png
identify output.png
file output.png
```

Use `write_file` for new text/SVG artifacts when possible. Use targeted patching for small edits to existing files. For large embedded-image SVGs, avoid stale overwrites: read or create a new revision filename before replacing the file.

## Obsidian Integration

When content comes from the user's vault:

- Default vault: `~/Documents/Obsidian Vault`
- Master Kanban: `~/Documents/Obsidian Vault/Master Kanban.md`
- Follow wikilinks to the source note.
- Preserve the source note's slide sequence and approved wording.

Do not modify Kanban status or source notes unless the user explicitly requests it.

## Output Report

Report briefly:

- Input directory
- Final output directory
- Files created
- Source note or Kanban item used
- Dimensions verified
- Visual verification status

## Failure Conditions

Stop and ask for clarification if:

- The directory contains multiple plausible template sets
- The intended slide order is unclear
- A required font is missing and fallback would change the design
- The template has no identifiable main/shadow pairing
- Copy cannot fit without changing the requested font sizes
- A background or source asset cannot be read

Do not silently redesign, shrink fonts, remove shadow layers, or substitute a different template.
