# CK3-Blackadder AI Coding Instructions

## Project Overview
This is a Crusader Kings III (CK3) mod that adds a Blackadder-themed alternative history bookmark (1178) and 1066 scenarios with custom characters, dynasties, and mechanics. The mod is self-contained and does not modify game files outside England in 1178.

**Key Constraint**: This is a **Paradox Script** (.txt and .yml) project, not a conventional programming codebase. All changes must follow CK3's domain-specific language format.

## Architecture Overview

### File Organization Pattern
```
CK3-HouseOfBlackadder/
├── common/           # Game mechanics definitions
│   ├── traits/       # Custom trait definitions (tba_blackadder_trait)
│   ├── dynasties/    # Dynasty metadata (names, cultures, mottos)
│   ├── dynasty_houses/
│   ├── bookmarks/    # Playable start scenarios with character rosters
│   ├── deathreasons/ # Custom death types (death_sacrificed)
│   └── dna_data/     # Character appearance data
├── history/          # Historical game state
│   ├── characters/   # Character definitions (births, traits, relations, deaths)
│   └── titles/       # Title ownership, succession, laws at game start
├── events/           # Event triggers and responses
├── gfx/              # Graphics assets (character portraits, UI icons)
├── localization/     # English translations (YAML format with l_english keys)
└── music/            # In-game soundtrack definitions
```

### Core Data Flow
1. **Characters** (`history/characters/TBA_characters.txt`) are the primary entities
2. **Dynasties** link characters to shared names and cultures
3. **Traits** (especially `tba_blackadder_trait` applied to ~15 chars) identify Blackadder cast
4. **Bookmarks** select which characters are playable at game start (1178 or 1066)
5. **Localization** provides display names for all identifiers
6. **Events** trigger character actions (relations, claims, jobs) at specific dates

## Critical Naming Conventions

**All new content must follow this prefix system** to avoid conflicts:
- `TBA_` prefix for characters (e.g., `TBA_plantagenet_blackadder`)
- `tba_` prefix for mechanics (e.g., `tba_blackadder_trait`)
- `dynn_` + `tba` prefix for dynasty/house translations (e.g., `dynn_tbablackadder`)
- Keys in localization use these same prefixes for consistency

**Character IDs use full names** after prefix: `TBA_edmund_i_blackadder`, `TBA_baldrick_III`, `TBA_bob`

## Key Workflows

### Adding a New Character
1. Define in `history/characters/TBA_characters.txt` with proper structure:
   ```paradox
   TBA_newchar_blackadder = {
       name = "Display Name"
       dna = TBA_blackadder          # Reference existing DNA (for consistency)
       dynasty = dynasty_tbablackadder # Link to dynasty
       father/mother = TBA_parent     # Establish family tree
       trait = tba_blackadder_trait   # Mark as Blackadder cast (if applicable)
       YYYY.M.D = { birth = yes }
       YYYY.M.D = { death = { death_reason = death_old_age } }
   }
   ```
2. Add **localization entry** in `TBA_l_english.yml` for any custom death reasons or nicknames
3. If adding to bookmarks, update `common/bookmarks/bookmarks/TBA_bookmarks.txt` with character reference and relations

### Modifying Character Traits or Relations
- **Traits** are added/removed via date blocks inside character definition
- **Relations** (lover, friend, rival, nemesis, employer) set via `set_relation_*` in date blocks
- **Council positions** assigned via `give_council_position` (e.g., `councillor_steward`)
- All date-based changes in `TBA_characters.txt` use format: `YYYY.M.D = { change = effect }`

### Updating Localization
- File: `localization/TBA_l_english.yml` (YAML format)
- Every user-facing string must have a key starting with `tba_` or bookmark names (`bm_1178_`, etc.)
- Use `\n` for line breaks in long descriptions
- Markdown-like tags: `#bold text#!` for emphasis

## Integration Points & Dependencies

### External References
- **History ID 204500**: Henry II (from base game) — many characters father/uncle/employer
- **History ID 183010**: Generic Plantagenet ancestor (TBA_plantagenet_blackadder's father)
- **History ID 7374**: Generic Bavarian character (Ludwig's mother)
- **History ID 206510**: Spanish title holder (TBA_infanta's father)
- Titles like `k_england`, `c_west_riding`, `c_lothian` are base game — referenced for claims and holdings

### DNA Data
- Custom DNA blocks stored in `common/dna_data/TBA_dna.txt` (referenced as `dna = TBA_blackadder`, etc.)
- Each main character lineage has unique DNA (TBA_blackadder, TBA_baldrick, TBA_percy, TBA_flashheart, etc.)

### Supported CK3 Version
- **Supported Version**: 1.18.* (check `descriptor.mod`)
- **Roads to Power DLC**: Required for Flashheart and Ludwig to load properly (otherwise they're deleted)
- Other DLCs optional (cosmetics only)

## Common Pitfalls & Patterns

1. **Date Format**: Always `YYYY.M.D` (not zero-padded). Examples: `1178.1.1`, `1190.6.10`
2. **Death Reasons**: Custom ones like `death_sacrificed` must be defined in `common/deathreasons/00_custom_deaths.txt`
3. **Trait Inheritance**: The `tba_blackadder_trait` is applied to main playable cast to identify them; not a hereditary trait
4. **Family Flags**: Use `add_character_flag = do_not_generate_starting_family` for important historical figures to prevent game-generated spouses/children
5. **Bookmark Character Selection**: Characters in bookmarks must have matching `history_id` pointing to their definition in `TBA_characters.txt`
6. **Council Positions**: Only one position per character per time period; changing positions requires a new date block

## Validation Checklist
- [ ] All character IDs, trait IDs, dynasty IDs start with `TBA_`/`tba_`/`dynn_tba`
- [ ] Every new gameplay string has localization key in `TBA_l_english.yml`
- [ ] Character definitions include at least one birth and one death date
- [ ] References to external character IDs (like 204500) are documented in a comment
- [ ] Bookmark character `history_id` matches character definition in `TBA_characters.txt`
- [ ] Date blocks use `YYYY.M.D` format with no zero-padding
