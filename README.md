================================================================================
                    DEFEND THE BASE - ULTIMATE EDITION
                         A Complete Player's Guide
================================================================================

WHAT IS DEFEND THE BASE?
Defend the Base is a tower defense game where you must protect your base wall
from 10 waves of increasingly dangerous enemies. You place different types of
towers on a grid, earn money by defeating enemies, and use that money to build
a stronger defense before the next wave arrives.

The game ends when either all 10 waves are defeated (Victory) or the enemies
drain your base down to zero health (Game Over).

================================================================================
HOW TO PLAY
================================================================================

STEP 1: START THE GAME
Open index.html in a browser. You will see the title screen with the
"INITIALIZE" button. Click it to begin Wave 1.

STEP 2: PLACE TOWERS
Left-click anywhere on the grid (right of the base wall) to place the
currently selected tower type. Towers cost money — make sure you have enough
before clicking.

    - Towers snap to a 50x50 pixel grid automatically
    - You cannot place two towers on the same cell
    - You cannot place towers on or left of the base wall (x ≤ 50)

STEP 3: SELECT A TOWER TYPE
Click one of the five buttons in the build menu at the top of the screen:

    🗼 SHOOTER  — $25   General-purpose. Fast fire rate, medium range.
    🛡️ WALL     — $25   Does not shoot. Blocks enemies and absorbs hits.
    💣 CANNON   — $100  Slow but powerful. Explodes on impact (area damage).
    🎯 SNIPER   — $75   Extreme range. High single-target damage.
    ❄️ FREEZE   — $80   Slows enemies. Pairs well with other towers.

A white border highlights the currently selected tower.

STEP 4: SURVIVE THE WAVE
Enemies spawn from the right side of the screen and walk left toward your base.
They will attack any tower in their path. If they reach the base wall, you lose
a life. Defeat all enemies in a wave to advance.

STEP 5: COLLECT MONEY AND BUILD MORE
Every enemy you kill earns you money. Kill enemies quickly in a row to trigger
a COMBO multiplier (up to x5) for bonus cash. Use that money to place more
towers before the next wave starts.

STEP 6: SELL TOWERS IF NEEDED
Right-click any placed tower to sell it for 50% of its original cost. A gold
"+$XX SOLD" text will confirm the sale. Use this to reposition your defense
between waves.

STEP 7: REACH WAVE 10 AND DEFEAT THE BOSS
Wave 10 contains a massive BOSS enemy (👹). It has 25,000 HP and takes 3 lives
if it reaches your base. Destroy it to achieve VICTORY and clear the sector.

================================================================================
CONTROLS
================================================================================

┌──────────────────────────────────────────────────┐
│  LEFT CLICK    │  Place selected tower            │
│  RIGHT CLICK   │  Sell tower (50% refund)         │
│  SPACE         │  Pause / Unpause the game        │
│  TAB           │  Toggle 2× speed mode            │
└──────────────────────────────────────────────────┘

================================================================================
TOWER REFERENCE
================================================================================

TOWER     │ COST │ HP  │ RANGE │ DAMAGE │ COOLDOWN │ SPECIAL
----------|------|-----|-------|--------|----------|-------------------------
SHOOTER   │ $50  │ 100 │  350  │   25   │  40 fr   │ —
WALL      │ $25  │ 420 │   —   │    0   │   —      │ Blocks only, no attack
CANNON    │ $100 │ 130 │  280  │   60   │  90 fr   │ Area-of-effect explosion
SNIPER    │ $75  │  80 │  720  │   80   │  60 fr   │ Lightning bolt visual
FREEZE    │ $80  │  90 │  230  │    4   │  50 fr   │ Slows enemy to 30% speed

CANNON area-of-effect radius: 70 pixels
FREEZE slow duration: 140 frames (~2.3 seconds)
Cooldown is measured in frames at 60 FPS (e.g. 40 fr ≈ 0.67 seconds)

================================================================================
ENEMY REFERENCE
================================================================================

ENEMY    │ EMOJI │ HP (Wave 1) │ SPEED  │ ARMOR │ LIVES DRAINED │ REWARD
---------|-------|-------------|--------|-------|---------------|--------
ZOMBIE   │  🧟   │     50      │  slow  │   0%  │       1       │  $20
RUNNER   │  💨   │     23      │  fast  │   0%  │       1       │  $15
ARMORED  │  🪖   │    200      │  slow  │  40%  │       1       │  $35
BRUTE    │  👊   │    335      │ medium │  20%  │       2       │  $50
BOSS     │  👹   │  25,000     │  slow  │   0%  │       3       │ $200

ARMOR reduces all incoming damage by a percentage.
LIVES DRAINED is how many base HP you lose if that enemy reaches the wall.
Enemy HP and speed increase each wave.

WHEN ENEMIES FIRST APPEAR:
    Wave 1-2  : Zombies only
    Wave 3-4  : Zombies + Runners
    Wave 5-6  : + Armored added
    Wave 7-9  : + Brutes added
    Wave 10   : All types + final BOSS

================================================================================
HOW THE GAME WORKS
================================================================================

THE GRID
The play area is an 800×500 pixel canvas divided into 50×50 pixel cells.
Towers snap to cell positions. The left 50 pixels are always the base wall.

THE BASE WALL
The base wall on the left side has 3 HP. It shows:
    - Visible brick cracks as HP drops
    - A red danger glow when at 2 HP or below
    - A small internal HP bar at the center of the wall
    - Heart icons (❤) in the UI — one per remaining HP

ENEMY MOVEMENT & THE FSM
Each enemy runs a Finite State Machine (FSM) with four states:

    SPAWN       → Brief 20-frame entry delay before the enemy starts moving
    WALK        → Enemy moves left. Checks for tower collisions each frame.
    ATTACK      → Enemy stops and hits a tower every 30 frames (deals damage).
    REACHED BASE→ Enemy touches the left edge, drains base HP, then is removed.
    DEAD        → Enemy HP reached 0. Triggers reward and death effects.

Enemies only attack towers that are physically in their path. If a tower is
destroyed, the enemy returns to WALK state and continues toward the base.

SHOOTER / SNIPER / CANNON / FREEZE TARGETING
These towers scan enemies each frame:
    - FREEZE    searches radially for the nearest unfrozen enemy in range
    - All others scan for enemies directly to the right within range (same row)

The first valid target found triggers a shot. Each tower type has a cooldown
before it can fire again.

BULLET SYSTEM
Bullets travel right-to-left from their firing tower. On hitting an enemy:
    - CANNON  → triggers area-of-effect damage to all nearby enemies
    - FREEZE  → applies slow effect (30% base speed for 140 frames)
    - SNIPER  → draws a lightning bolt visual effect
    - Others  → direct damage, reduced by enemy armor

Each bullet has a trail effect whose color and width matches its tower type.
A 12% critical hit chance doubles bullet damage.

================================================================================
VISUAL EFFECTS SYSTEM
================================================================================

The game uses a layered effects system that plays automatically:

EFFECT          │ TRIGGER
----------------|----------------------------------------------------------
Particles       │ Explosions, tower placement, enemy deaths
Floating text   │ Damage numbers, combo labels, sell confirmations
Shockwaves      │ Expanding rings on hits, cannon blasts, deaths
Blood decals    │ Splatter left on ground where enemies die
Screen shake    │ Cannon hits, enemy reaching base, boss death
Screen flash    │ Red on base hit, orange on cannon, white on boss kill
Lightning bolt  │ Sniper fires — jagged line from tower to target
Rain            │ 140 animated raindrops falling at all times
City silhouette │ Dark skyline visible in the background horizon

================================================================================
SCORING & COMBO SYSTEM
================================================================================

MONEY EARNED  │ CONDITION
--------------|---------------------------------------------
$15 – $200    │ Killing an enemy (varies by enemy type)
x2 to x5      │ Kill combo multiplier for rapid kills

COMBO RULES:
    - Kill an enemy to start a combo timer (120 frames / 2 seconds)
    - Each additional kill before the timer expires increases the combo
    - Combo multiplier caps at x5
    - Multiplier is applied to the killed enemy's base reward
    - Combo resets if no enemy is killed within the 2-second window
    - Orange floating text appears for x2+, larger text for x3+

================================================================================
WAVE STRUCTURE
================================================================================

WAVE │ ENEMIES SPAWNED │ NOTES
-----|-----------------|----------------------------------------------
  1  │       7         │ Zombies only
  2  │       9         │ Zombies only
  3  │      11         │ Runners introduced
  4  │      13         │ Runners continue
  5  │      15         │ Armored introduced
  6  │      17         │ Armored continue
  7  │      19         │ Brutes introduced
  8  │      21         │ Brutes continue
  9  │      23         │ Brutes continue
 10  │      12         │ All types + BOSS as final spawn

Enemies spawn at intervals — faster intervals each wave (minimum 20 frames).
Each wave, enemy HP and speed scale upward.

================================================================================
TECHNICAL DETAILS
================================================================================

CANVAS SIZE       : 800 × 500 pixels
GRID CELL SIZE    : 50 × 50 pixels
BASE WALL WIDTH   : 50 pixels (column 0)
FRAME RATE        : 60 FPS (requestAnimationFrame)
AUDIO             : Web Audio API — procedurally generated, no audio files

SOUND EFFECTS:
    shoot   → Short square-wave blip
    sniper  → High-pitched crack
    cannon  → Deep sawtooth boom with layered overtone
    freeze  → Rising sine sweep
    hit     → Short sawtooth impact
    kill    → Low falling sawtooth rumble
    lose    → Deep descending loss tone

================================================================================
TIPS FOR WINNING
================================================================================

PLACE WALLS EARLY
Walls are cheap ($25) and have 420 HP — the highest of any tower. Use them as
a front line to slow enemies while your shooters deal damage from behind.

STAGGER YOUR TOWERS
Don't cluster all your towers in one column. Spread them across several rows
so enemies must fight through multiple towers to reach the base.

BUILD A FREEZE + CANNON COMBO
Place a Freeze tower close to the enemy spawn and a Cannon slightly left of it.
Frozen enemies are slower targets — easier for the Cannon's area damage to hit
multiple clustered enemies at once.

USE SNIPERS FOR ARMORED AND BRUTES
Armored enemies reduce incoming damage by 40%. Snipers deal 80 damage per shot
(the highest of any tower) — use them specifically on tanky enemies.

SELL AND REBUILD BETWEEN WAVES
You can right-click towers during the "Wave Cleared" screen too. Selling a
$100 Cannon gives you $50 to reposition it somewhere more effective.

DON'T IGNORE THE BOSS ON WAVE 10
The boss has 25,000 HP and moves slowly. Build up as many towers as possible
before Wave 10. Prioritize Snipers and Cannons, then freeze towers to buy time.

WATCH YOUR BASE HP
When the base drops to 2 HP, it glows red. That's your warning — if you're
short on money, sell non-essential towers and place Walls to plug gaps.

USE 2× SPEED WISELY
Press TAB to toggle 2× speed. Use it when the wave is nearly over and enemies
are spread out. Turn it off during heavy multi-enemy waves.

================================================================================
COMMON SITUATIONS
================================================================================

SITUATION: Enemies keep reaching the base on the left side
SOLUTION: Place Walls at x=50 (the first column after the base). Their 420 HP
acts as a buffer. Back them up with a Shooter directly behind.

SITUATION: Armored enemies are absorbing too much damage
SOLUTION: Switch to Snipers. Their 80 base damage outpaces armor reduction
better than Shooters at 25 damage.

SITUATION: I'm running out of money between waves
SOLUTION: Kill combos are the fastest way to earn extra money. Place towers
close together so multiple towers focus the same enemy — kills happen faster,
keeping your combo multiplier active longer.

SITUATION: The Cannon keeps missing enemies
SOLUTION: Cannon targets enemies in the same horizontal row. Make sure the
Cannon's row aligns with an active enemy lane. Combine with a Freeze tower to
slow enemies into the blast zone.

SITUATION: The Boss is almost at my base wall
SOLUTION: If you have money, spam Walls at the leftmost column to stall it.
Every extra second lets your other towers deal more damage.

SITUATION: I want to restart mid-game
SOLUTION: Refresh the page. There is no in-game reset button — only the
"REBOOT SYSTEM" button on the Game Over screen and "PLAY AGAIN" on the
Victory screen.

================================================================================
QUICK REFERENCE CARD
================================================================================

BUILD MENU:
┌──────────────────────────────────────────────────────────┐
│  🗼 Shooter  $50  │  💣 Cannon  $100  │  ❄️ Freeze  $80  │
│  🛡️ Wall     $25  │  🎯 Sniper   $75  │                  │
└──────────────────────────────────────────────────────────┘

CONTROLS:
┌──────────────────────────────────────────────────────────┐
│  LEFT CLICK  = Place tower    │  SPACE = Pause           │
│  RIGHT CLICK = Sell tower     │  TAB   = 2× Speed        │
└──────────────────────────────────────────────────────────┘

BASE HP:
┌──────────────────────────────────────────────────────────┐
│  ❤❤❤ = Full    │  ❤❤ = Damaged    │  ❤ = Critical        │
└──────────────────────────────────────────────────────────┘

ENEMY WAVES:
┌──────────────────────────────────────────────────────────┐
│  Waves 1-2  : 🧟 Zombies                                 │
│  Waves 3-4  : 🧟 + 💨 Runners                           │
│  Waves 5-6  : + 🪖 Armored                              │
│  Waves 7-9  : + 👊 Brutes                               │
│  Wave 10    : All types + 👹 BOSS                        │
└──────────────────────────────────────────────────────────┘

================================================================================
GOOD LUCK, COMMANDER.

Place smart. Defend hard. Don't let them breach the wall.

The deeper you get into the waves, the more you'll learn which tower
combinations work and which lanes enemies favor.

HOLD THE LINE.
================================================================================
