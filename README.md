# MediaAssetBuilderSkill

This is a Hermes skill for turning supplied media-template directories into verified branded assets while preserving the designer's template decisions.

## What it does

Use this skill when you have:

- One or more JPG/PNG background images
- SVG templates for a title slide and follow-up slides
- Optional source copy from an Obsidian note or Master Kanban board

The skill replaces content and backgrounds while preserving the original template structure, including:

- Canvas dimensions
- Font families, weights, and sizes
- Text coordinates, alignment, and anchoring
- Main-text and shadow-text layers
- Filter definitions and shadow settings
- Layer IDs and labels
- Background crop, placement, colors, opacity, and spacing

## Core principle

Treat the user's SVG template as the source of truth. Do not redesign the template unless the user explicitly asks for a redesign.

## Standard workflow

1. Inspect the directory and identify the background images and SVG templates.
2. Verify image and SVG dimensions.
3. Inspect the SVG structure and map each visible text layer to its shadow layer.
4. Read the source content from the requested note or Kanban item.
5. Map the copy into the template without changing layout or typography.
6. Replace embedded backgrounds while preserving transforms and cropping.
7. Create editable SVG masters and render PNG outputs with Inkscape.
8. Verify every slide for dimensions, visibility, clipping, and layer integrity.

## Output

Typical outputs include:

- `phase2-slide-01-title.svg`
- `phase2-slide-02-follow.svg`
- `phase2-slide-03-follow.svg`
- Matching rendered PNG files

## Notes

- Preserve paired main/shadow text layers.
- Keep output self-contained when needed by embedding backgrounds as base64 data URIs.
- Do not silently redesign, shrink fonts, remove shadow layers, or substitute a different template.
- Stop and ask for clarification if the template set, slide order, or font fit is unclear.
