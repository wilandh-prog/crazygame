# Game Design Document: Blob Battalion

## Game Title & Genre
**Title:** Blob Battalion
**Genre:** Merge / Idle Defense Hybrid

## Core Mechanic
Buy blob eggs that hatch into colorful blob warriors. Drag two identical blobs together to merge them into a stronger evolution. Blobs auto-attack incoming enemy waves — survive as long as possible, earn gold, and build the ultimate blob army.

## Gameplay Loop (per session)
1. **Buy** — Spend gold to purchase blob eggs (tap/click a button)
2. **Merge** — Drag matching blobs onto each other to evolve them (higher tier = stronger)
3. **Defend** — Blobs auto-attack enemies approaching from the right side
4. **Earn** — Defeated enemies drop gold; wave completion gives bonus gold
5. **Repeat** — Buy more eggs, merge higher, face tougher waves

## Progression / Retention Loop
- **In-run progression:** Blob tiers (Tier 1-10), wave number, gold accumulation
- **Meta-progression (persists between runs):**
  - Permanent upgrades: blob damage, blob attack speed, gold multiplier, starting gold
  - Unlockable blob types with unique attack patterns (unlocked at wave milestones)
  - Highest wave record (local leaderboard)
- **"One more round" hooks:**
  - Show "X more gold needed" for next upgrade after death
  - Show preview of next unlockable blob type
  - Daily login bonus (bonus starting gold)

## Target Session Length
- **Single run:** 3-10 minutes (gets longer with upgrades)
- **Full session:** 15-30 minutes (multiple runs with upgrades between)

## Visual Style
- **Geometric/cute blobs** — circles with expressive eyes, vibrant colors per tier
- **Procedural art** — all drawn with Canvas shapes (no sprite assets needed)
- **Squash & stretch animations** — blobs bounce, squish on merge, shake on attack
- **Particle effects** — merge sparkles, enemy death explosions, gold coin trails
- **Neon/vibrant color palette** on a dark background

## Controls
- **Desktop:** Mouse click to buy, drag to merge, hover for info
- **Mobile:** Tap to buy, drag to merge
- **No keyboard required** for core gameplay (avoids WASD/AZERTY issues entirely)

## Tech Stack: Phaser 3 (via CDN)
**Justification:**
- Built-in drag & drop input handling (critical for merge mechanic)
- Tweens engine for smooth animations (squash/stretch, easing)
- Particle system for visual juice
- Scene management for menu/game/upgrade screens
- ~1MB from CDN, well within size limits
- Handles game loop, delta time, and rendering efficiently
- Large community and documentation

## Why This Will Work on CrazyGames
1. **Genre fit:** Merge and idle are both top trending genres on the platform right now
2. **Instant fun:** First blob appears in <3 seconds, starts fighting immediately
3. **Zero learning curve:** Buy (click button) → Merge (drag) → Watch blobs fight. Visual onboarding only.
4. **Mouse/touch only:** Perfect for both desktop and mobile, no keyboard layout issues
5. **Strong retention:** Meta-progression gives reason to return, daily bonus incentivizes sessions
6. **Natural ad breaks:** Between waves (midgame), after death (midgame), watch ad for 2x gold (rewarded)
7. **Tiny file size:** All procedural graphics, no image assets — initial load will be <5MB
8. **Satisfying feedback:** Merge animations, particle explosions, screen shake, escalating power fantasy

## Ad Integration Plan
- **Midgame ad:** After player dies (between runs), minimum 3 min apart
- **Rewarded ad:** "Watch ad for 2x gold from this run" after death screen
- **Rewarded ad:** "Watch ad for free premium egg" on buy screen (once per run)
- **Never:** Before first gameplay, during active gameplay

## Save Data
- Persistent upgrades (damage, speed, gold multiplier, starting gold levels)
- Unlocked blob types
- Highest wave reached
- Total gold earned (lifetime stat)
- Daily login timestamp
