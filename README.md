# Seven Hearts

This repository contains the Lua script and XML file for the "Seven Hearts" implementation in Tabletop Simulator.

## The Game

This card game originates from *The Legend of Heroes: Trails through Daybreak II* by Falcom, where it appears as a strategic and entertaining minigame within the world of Zemuria ([description on Neoseeker](https://www.neoseeker.com/the-legend-of-heroes-trails-through-daybreak-ii/Seven_Hearts)). Four players take turns playing cards to accumulate damage on the field. If a player cannot — or chooses not to — play a card, they must take all the accumulated damage and draw cards equal to the total damage value. The game continues until one player loses all their life points or the deck is depleted. At the end, each player's remaining life points and the number of cards in their hand are added together. The player with the highest total wins. In case of a tie, the winner is the player who would have taken the next turn in the original play order. "Seven Hearts" is designed for four players. Empty seats can be controlled by NPCs, which can be toggled on or off via the in-game interface.

## Features

This Tabletop Simulator version of Seven Hearts faithfully replicates the original rules. However, unlike the original — where you can only play solo against three NPCs (non-player characters) — this version supports up to four human players. Any unoccupied seats can be automatically filled with NPCs, whose presence is indicated by a colored NPC icon above their hand area. Real players will display their Steam avatar instead.

An additional feature is Team Mode, which existed in the original but was kind of poorly implemented. In this version, players sitting opposite each other form teams, and team points are always visible on the right side of the screen. The UI also shows whose turn it is with a colored indicator, the number of remaining cards in the deck, and each player's HP and hand size.

Keep in mind that Tabletop Simulator is a true "simulator" — players can technically do anything, including spawning random objects. However, the game script prevents players from cheating by restricting card movement: you can't take cards from other players, the deck, or the discard pile, and you can't play cards illegally.

Summary of Features:
- Fully implements the original game rules
- Play with real people or optional bots
- Includes team mode with improved visibility
- Anti-cheat scripting for fair gameplay

## How to Use

First, you’ll need [Tabletop Simulator](https://store.steampowered.com/app/286160/Tabletop_Simulator/) from Steam — a sandbox for simulating tabletop games. Since it supports 3D objects and Lua scripting, it's ideal for creating game prototypes or adapting existing games like this one.

You **could** manually copy the contents of `Global.-1.ttslua` (Lua script) and `Global.-1.xml` (UI layout) into your own Tabletop Simulator save, but this likely won't work well due to the many 3D objects that depend on specific GUIDs (Globally Unique Identifiers).

The better option is to simply subscribe to the Workshop item:

https://steamcommunity.com/sharedfiles/filedetails/?id=3474959689

Maybe you ask yourself, why I published the code on GitHub then? Because extracting and editing code in Tabletop Simulator is a nightmare due to its limited in-game editor. This GitHub repo makes it easier for others to reuse and learn from the code.

## Useful Elements for Your Own Scripts

Here are some helpful features that may inspire your own scripted games in Tabletop Simulator. I've included the relevant function or file names in parentheses:

- UI with buttons and panels (`Global.-1.xml`)
- Toggle game modes (e.g., `toggleBotMode`)
- Card activation logic (`activateCardEffect`)
- Swapping hands using coroutines (`swapHands`)
- Hover color effects (`onInfoHover`, `onInfoUnhover`)
- Combining player colors for UI visibility (`updateInfoOverlayVisibility`)
- Timer updates (`startTimerCounter`, `showCounter`)
- Game-end detection with `Wait.condition` (`startGame`, `reachedGameEnd`)
- Save and respawn objects via JSON (`onLoad`, `setupGame`, using `originalDeckJson`)
- Move cards between zones (`clearDropZone`)
- Control legal plays (`onObjectDropped`, `onObjectPickedUp`)
- Prevent stacking — e.g. cards to decks — in certain zones (`tryObjectEnterContainer`)

Also interesting but simple helper functions:
- `contains`: checks if a value exists in a table (returns boolean)
- `containsWithIndex`: same as above, but returns index if found
- `getMaxIndices`: returns highest value and its index in a table
- `dump`: string representation of a table for debugging

## NPC Behavior

This section describes how NPCs choose which card to play. If the hand is empty, the NPC skips their turn. If there's damage on the field and no playable cards, the NPC takes the damage. When cards are available, NPCs filter them by legality and use the following logic:

- Prioritize normal element cards (random pick in them)
  - Prefer "Tear" or "Earth Guard" if field damage ≤ 4 and HP > 2
  - Prefer "Earth Guard" over "Tear" if field damage > HP + 2
  - Prefer "Fire Bolt" over "Forte" if field damage ≤ 2
  - Prefer special cards over "Tear" or "Earth Guard" if field damage is high
- Hold onto special cards unless no normal ones are playable
  - Exception: use "Lost Mobius" if any opponent has 2 HP left
  - When playing "Crescent Eye", choose the player with the most cards
    - Exception: in team mode, don't target your teammate

## FAQ

**Why not "Blade" or "Vantage Master"?**  
I've played all (english) Trails games. I loved both "Blade" and "Vantage Master", but back when Blade came out (Cold Steel I & II), I didn’t have time — or Tabletop Simulator wasn't around yet. Vantage Master is just too complex (more like Hearthstone). When "Trails through Daybreak II" released in English in 2025, I liked the simplicity of Seven Hearts and decided to code it. Maybe I’ll do Blade someday — it’s even simpler.

**Why use "NPCs" in-game but "bots" in code?**  
“Bots” has a negative connotation these days, especially in social media. “NPCs” is a more game-appropriate term. But in code, "bot" is more concise and conventional.

**Where do the images come from?**  
Card and info images are from *Trails through Daybreak II* by Falcom. The rules sketch and bot icons were created by me using Microsoft Co-Pilot and further edited in Photoshop to match the game's style.

**Is the code free to use?**  
Yes, it's licensed under MIT. That covers the code, not the images (which are not hosted on GitHub and retain their original rights).

**Why do bots have a "thinking" delay?**  
Two reasons:
1. **Atmosphere** – Instant NPC turns feel unnatural. A 3-second pause simulates real human gameplay, but it can be adjusted with the variables at the beginning of the code (`botThinkingTime`)
2. **Code faster than Physics** – Some game elements check 3D objects, which may not have finished moving when a script runs. So I use `Wait.time` to avoid conflicts and make sure objects are settled before proceeding.

**Is the code final? Could it be improved?**  
Definitely! For better stability, card handling could rely on GUIDs instead of names. But for simplicity and readability, I chose string-based logic. NPC logic could also be expanded (e.g., prefer "Refreshing Veil" when the last player has 0 cards), but I stopped at a good balance between complexity and clarity.

**What’s your favorite Trails game?**  
I love the whole series, but the Crossbell Remaster arc on Switch was a standout. Still, the original trilogy remains my favorite. Estelle is the best protagonist so far (and only female one!), and Kevin in part 3 is just awesome — I can’t wait for his return in part 13 (*Trails Beyond the Horizon*)!
