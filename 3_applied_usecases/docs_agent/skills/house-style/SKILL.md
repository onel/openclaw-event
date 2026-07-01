---
name: house-style
description: How this project's documentation should read. Seed defaults here; learned preferences in MEMORY.md override them.
---

# House style

Defaults below. Anything in `MEMORY.md` wins over them, because that is what the human has actually corrected.

## Voice
- Plain and specific. Say what the reader does and what happens.
- No marketing language in docs unless the project explicitly wants it.
- Match the surrounding doc: heading levels, code-block style, terminology.

## Structure
- Document the change where a reader would look for it, not where it is easiest to bolt on.
- Update examples and config snippets so they actually run, not just the prose around them.
- For a removed flag or changed default, fix every place the old behavior is stated, including quickstarts.

## Changelog
- Follow the format named in `USER.md`. One entry per user-facing change, newest first unless told otherwise.

## Learn
After each accepted or rejected edit, refine one durable rule in `MEMORY.md` rather than restating it here.
