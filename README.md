# CombatUI for Mudlet

CombatUI is a floating combat status panel for Mudlet, packaged for Muddler.
The default package ships the runtime only: core UI, prompt parser, combat-event
handlers, bootstrap, and the trigger objects that wire them together.

The supported install path is the generated `.mpackage`. The old manual
script-by-script install path has been retired.

## Features

- HP, MP, XP, and AP gauges
- Enemy health tracking with low-health highlight
- Combat event log for damage, abilities, heals, rewards, and deaths
- TTK and TTD prediction with a risk indicator
- AP-per-hour tracking with session total
- Collapsible buffs and debuffs sections
- Saved panel position and collapsed state

## Package Layout

The repository now contains two Muddler packages:

- `./` builds the default `CombatUI` package.
- `optional/debug/` builds the optional `CombatUI_Debug` add-on package.

The default package excludes debug aliases and test helpers so the shipped runtime
stays as small and stable as possible.

## Runtime Structure

The default package preserves the current runtime split:

- `CombatUI Core` provides the UI engine and public API.
- `CombatUI Prompt Handler` parses the prompt line and calls `updateCombatUI()`.
- `CombatUI Event Handlers` handles attack, damage, death, reward, and heal lines.
- `CombatUI Bootstrap` initializes the window after all runtime functions exist.

The optional debug package adds the following developer conveniences:

- `combattest`
- `combatdebug`
- `combatecho`

## Alternate Integration Mode

`examples/CombatUI_Combat_Trigger.lua` remains a reference implementation for
MUDs that emit a single combined combat-status line instead of the prompt format
used by the default package. It is not shipped as an active trigger in the main
package.

## Unofficial Squaresoft MUD Prompt Setup

For Unofficial Squaresoft MUD, the default package expects the game prompt to be
set to this exact format before the packaged prompt trigger will work:

```text
hpset UI>HP=$$tok_hp$$/$$tok_max_hp$$ MP=$$tok_mp$$/$$tok_max_mp$$ XP=$$tok_xpnl%$$
AP=$$tok_apnl$$,$$tok_apnl%$$ T=$$tok_target$$,$$tok_target_condition$$ B=$$tok_buffs_short$$
D=$$tok_debuffs_short$$
```

Without that prompt format, the main `UI Prompt` trigger will not receive the
structured data that CombatUI expects.

## Validation Inputs

The `validation/` directory contains captured combat output used to verify the
prompt and event patterns during package testing.

## Release Notes

Current packaged release changes:

- migrated the project to a Muddler package layout
- made `src/` the canonical runtime source
- retired the manual install workflow in favor of `.mpackage` import
- documented the required `hpset` prompt for Unofficial Squaresoft MUD
- added a per-encounter combat summary in state

## Combat Summary

CombatUI now keeps a summary of the most recent encounter in state. The summary
captures:

- damage dealt
- damage received
- HP healed
- MP spent
- the enemy that was killed
- rewards from the battle
- largest hit dealt and taken
- attack, ability, and healing counts
- duration and average DPS

The summary is intended to give a clean end-of-fight view of what happened
without changing the core combat panel layout yet.

### Known Limits

The summary is useful, but not perfect. Keep these in mind:

- MP spent is inferred from prompt MP decreases, so it is approximate
- biggest-hit values use the damage number reported by the combat line
- reward values are captured only when the game emits the reward line
- non-kill fights may finalize as interrupted, lost target, or ended
- if the game reports a combined multi-hit action as one damage line, that
	combined value is what the summary sees

## Build And Install

See [INSTALL.md](INSTALL.md) for the Muddler build,
package import, trigger expectations, and optional debug-package workflow.
