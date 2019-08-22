---
layout: project
title: Variable Syncing in Plazma Burst 2 Multiplayer
subtitle: Finding a solution to syncing level variables in Plazma Burst 2's online multiplayer, as well as creation of a whole new game mode.
img1: tcccg/screenshot1.png
project-url:
description:
start-date: 2018-06-06
end-date: 2018-06-14
made-with: PB2 Advance Level Editor
thumbnail: pb2-var/thumbnail.png
categories: demos
---
<p>
Plazmaburst 2 is a web game released in 2011. The addition of a level editor allowed players to create, share, and play with others on custom maps.
</p>
<br>
<p>
One of the unique features in the level editor is the trigger system, a cause and effect system that players can invoke during gameplay.
There are many ways triggers can be activated. It could be on a timer, a player killing a npc, or a player entering a region of the map. Triggers has over 100+ different actions they can perform upon activation. Things like spawning weapons, moving players, or performing operations on variables.
</p>
<br>
![](\assets\images\projects\pb2-var\trigger_actions.png)
<br>
<br>
<p>
The aforementioned allowed level creators to implement their own gamemodes into the game, with one caveat.

The game does not sync variables from triggers between players, leaving each player to run the level's logic locally on their computer.

Triggers activated outside a player's vision will not activate for that player at all.
This leads to desynchronization between players as level elements are activated for some but not others.
</p>
<br>
<p>
The community got up and a quest to find a way to synchronize variables was found.
Many attempts were made, like using a NPC's heath bar. Unfortunately NPC health only synchronized when the NPC is on screen.

Apparently there were more than just variables that remained unsynced.

This makes sense as in normal gameplay, a player's clients does not need to know the health of offscreen characters, only whether or not they're alive.

Character coordinates are also synced, but there's no trigger to read their coordinates.
</p>
<br>
<p>
Eventually this trigger came across  my attention...
</p>
<br>
![](\assets\images\projects\pb2-var\set_value.png)
<br>
<br>

<p>
At first I brushed it off as madness, certainly you can store values by counting the number of NPCs in a room, but it would be entirely unfeasible.
</p>
<br>
<p>
Spawning and killing NPCs has a severe toll on the game's performance, especially in large numbers. Now do that every time you want to sync a value, and it's a unplayable mess.
</p>
<br>
<p>
Unless there's a way to represent large values with a small number of NPCs, this idea will remain on the shelf.
</p>
<br>
<p>
Presenting...the NPC binary counter!
</p>

<br>![](\assets\images\projects\pb2-var\8b_counter.png)
<br>
<br>

<p>
By using the NPCs as bits and their presence in a region as on/off we can represent values of 2^n with n number of NPCs.
</p>
<br>

<p>
Getting everyone's clients to sync is easy, synchronization is done every 10 seconds via a local timer. Desynchronization is also resolved, since NPC location is always synced between players. If a player's actor is in the wrong spot, it'll simply snap back to the median position. Regardless, a bit's value is enforced across all players.
</p>
<br>
And thus, variable synchronization is solved! In a roundabout way, but it's the best that we can do in this Turing tar-pit.

<br>
Here it is in action:

<br>
This map is originally Team Deathmatch, but with the magic of (quite a few) triggers, I've managed to convert it into a capture-the-point style game mode. Players run around capturing points and try to deplete the other team's ticket counter to 0.

<br>![](\assets\images\projects\pb2-var\ctp.png)
<br>
<br>
Point values and team tickets are the only variables that needs to be synced between clients. In total, there are 32 actors for all 3 control points and ticket counters for both teams.

<br>

Map performance is acceptable, but I've received complaints from players using older computers that the map runs at ~10 fps for them. I'm using a ternary counter or even a quaternary counter would improve performance, as less NPCs are used. Although the cause for the oss of performance is unknown. It could either be the having a large number of actors or the triggers if regions are occupied. Or maybe just having numerous triggers on your map degrades performance.

<br>

Overall, I'm satisfied with the outcome of this project despite sub-par performance on some computers.
<br>