# Summary
1. A Tamagotchi multiplayer game for S&Box. But uses Terry (the Citizen model, the iconic default character of S&Box).
2. Is 2D, the characters walk around and exist on the floor, SOME forward and back movement available on the navmesh, can even fall off the front (but automatically teleported after falling arbitrary distance).
3. Primarily, this is an overlay for Twitch Streamers, ui must be at a minimum. Host-side, no UI, Orthographic camera, floor and skybox is a greenscreen. But on Client-Side, 3D 3/4 view and buttons for the twitch commands + twitch chat.
4. Hosts get a bunch of options to configure their game. Only non-configured games contribute to the Leaderboards.
5. Terrygotchis can only die of hp below zero.
6. Speaking of which, 2 preconfigured defaults are available: Realistic and Daily. These tie into Leaderboards.
7. Players interact via twitch chat commands, and most interactions involve spawning an object near their Terrygotchi, who they assign via a command: zMyTerry {Terrygotchi name}.

# North Star:
Terrygotchi evolve and as they do, become easier to care for. If their Hunger or Weight or Happiness goes above 10 or below 0, they become ill. During Ill state, they lose hp. hp hitting below 0 is death. Falling ill twice in a row in quick succession should garantee death as a design philosophy. 

- SBOX Clone unique features:
	+ Configurable Session Options:
		- STRIKETHROUGH ---Toggleable "Characters age while offline" so that when you resume a closed session, it takes your local time and applies values as if it was always on. Default: off. Turning it on would make it behave like the original toy. --- STRIKETHROUGH. THIS FEATURE IS DISABLED FOR NOW.
		- Permadeath: Terrygotchi that die cannot be brought back and are deleted from the local save. Also unpairs the player from the Terrygotchi as if they typed zNoTerry.
		- Values anywhere in this document where a value is mentioned like "X (default Y)". For example, changing how long the day or night cycle is, or Acceleration of a BabyTerry form.
	+ Leaderboards: (Using Sandbox Leaderboard Services)
		- Only the 2 configurations are available for Leaderboard tracking: Realistic and Daily.
		- Shows player, name of their Terrygotchi, and Seconds Alive.
		- Realistic has slower speeds, should take a week of gameplay to get to Adult. (If playing for 4 hours per day, that's 4 Hours * 60 Minutes * 60 Seconds * 7 days = 100,800 seconds.
		- Daily has faster speeds, should take a single session (4 hours) to get to Adult. (14,400 Seconds)
   

# Key Feeling:
Keep the feeling of something that must be maintained and cared for with an amount of 'friction' that gives JUST enough character to give the terrygotchi personality. Such that the "cute!!" moments are infrequent, and therefor more valuable.

# Section 1 — Essential Core Mechanics (the moment-to-moment loop):
1. Feed:
    Terrygotchis can be fed Cake or Potato.
2. Clean:
    Younger evolution stages poop at earlier times. The pooping is stealthy so as to keep you attentive. If poop remains on screen long enough, it becomes toxic. If a Terrygotchi collides with Toxic Poop, the Terrygotchi's happiness drops every time; the Toxic Poop is NOT consumed and persists until removed by bleach (zClean). Can cause Illness indirectly.
3. Play: A Twitch Chat Hi/Low guessing mini-game played over a small number of rounds. Chat collectively guesses a hidden target; the median of submissions within the vote window is taken as the round's guess. Earlier correct guesses grant larger Happiness/Weight rewards to every Terrygotchi in the session; an incorrect final guess has no penalty. See the Play Game system in Details for exact rounds and rewards.
4. Medicine: 
    Terrygotchis can be fed Painkiller or Antiviral.
5. Discipline:
    Occasionally, a "MisbehaveChance" call is executed. Then, upon random chance, the Terrygotchi enters 'Misbehaving' state. This state is not mutually exclusive, a Terrygotchi can have 'Misbehaving' AND have 'Illness' for example. When a Terrygotchi is in the 'Misbehaving' state, any Happiness boosting becomes less effective.  Misbehaving only ends when you Tickle or Spank the Terrygotchi.
6. Thought-Bubbles:
    Terrygotchis have a thought bubble spawn above their heads where text emoticons can communicate information to the player. Terrygotchi can be asked 'zHowAreYou', rendering text hearts (HP) and then text smiley faces (Happiness). See the HowAreYou action in Details for exact thresholds. The Thought-Bubble can pop up, not prompted, warning players of scenarios or states the terrygotchi is now under (e.g. falling ill, misbehaving, hunger/happiness extremes, sleeping, waking, dying).
7. Sleep: The game session swaps back and forth between daytime state and nighttime state. Once nighttime hits, anything else going on in the game session ends and Terrygotchi fall asleep where they gain hp and happiness.
8. Stats: All character states can be seen via animations, shaders, thought-bubble, and/or facial animations. But all audio will be a second priority for now.

# Section 2 — Meta-Features and Lifecycle Systems (the systems that make the loop meaningful):
    Death: Each Terrygotchi can die. Illness hurts them. Various things cause Illness.
    Timers: All Per-Terrygotchi-Scope timers are based off birth-date (local real time and day) of a Terrygotchi.
    Illness: A Terrygotchi can fall ill, gaining the "Illness" status. "Illness" lasts for X (default 30) seconds. Every X (default 5) seconds, "Illness" causes -1 to Health Points. Illness Timer triggers it. On illness onset a thought bubble announces it (the player-facing cue for illness; there is no body-color/green tint).
    Client-Side: Per session, if a player wishes to interact via the SBox game instead of via the twitch chat, there is a 'chat-box' inside the game and all the z-prefixed commands are available as buttons that will auto-target YOUR terry. The in-game chat-box renders only for client users and will also be a copy of the twitch-chat that the host is connected to. A single human player could log in via S&Box Game Client and Twitch and session doesn't care, the single user could pair themselves to the same Terrygotchi via both Twitch and their game or different ones.
    Immortality System: When a Terrygotchi is spawned via zResume, it gets the "Immortal" status. This is so players have an opportunity to bring their Terrygotchi back from the dead if it's been a long time since the previous session. Lifetime governed by Immortality Timer.
    Minimum Values: Individual per-Terrygotchi-scope other value rules aside, a Terrygotchi value cannot go below -1.
    Aging: Each individual Terrygotchi ages from the moment they are spawned. Each real-world second that passes contributes to their by 1.
	Evolution:
      Sausage->BabyTerry->ChildTerry->TamaTerry->AdultTerry
     - Sausage: Just a physically based bendy Sausage model. Effectively the "egg" tamagotchi equivalent.
     - BabyTerry: Is now the moving player model. High on shader fx? Speed is X% of AdultSpeed (matches size, 15%). Acceleration is X% (matches size, 15%). 15% of engine default size.
     - ChildTerry: 25% of engine default size, Speed is X% of AdultSpeed (matches size, 25%). Acceleration is X% (matches size, 25%).
     - TamaTerry: 50% of engine default size, Speed is X% of AdultSpeed (matches size, 50%). Acceleration is X% (matches size, 50%).
     - AdultTerry:100% of engine default size, Speed is X% of AdultSpeed (matches size, 100%). Acceleration is X% (matches size, 100%).
     - SageTerry: 110% of engine default size, Speed is X% of AdultSpeed (matches size, 110%). Acceleration is X% (matches size, 110%).
    Final Stages: The final stage of every Terrygotchi is either death (if Permadeath enabled) or reaching one of 8 Different AdultTerry Terrygotchi forms OR one of 4 different SageTerry Terrygotchi forms. If requirements for one or more SageTerry requirements are met, that takes priority of an AdultTerry form. AdultTerry forms are based on final values. Whereas SageTerry are based on either more esoteric requirements or average stats throughout the stages of life.
    Value Caps: Natural-recovery sources (Sleep wake-up bonus) will not raise Happiness such that each individual Terrygotchi could be made ill.

## Chat Commands
All interactions are done via chat-box commands prefixed with the letter **z** (e.g. `zFeed Cake`). The prefix is **z**, not `/`, because Twitch intercepts `/`-prefixed messages as its own commands and never delivers them to chat readers. Commands are per-player triggered and affect their paired Terrygotchi only. Players pair with a Terrygotchi (Many players can pair to a single Terrygotchi) via zMyTerry or zNewTerry. zNewTerry spawns a new Terrygotchi.
  - Commands:
    - zResume {Terrygotchi name} - Spawns the named Terrygotchi. If the session host has no record of you, spawns from your local save. If neither exists, an error is written to chat. If the Terrygotchi is already spawned, pairs the command caller to it. Cannot resume a Permadeath'd Terrygotchi.
    - zMyTerry {Terrygotchi name}- all commands will target that Terrygotchi.
    - zNewTerry {New Terrygotchi name}- Spawns a new Terrygotchi at Sausage stage and pairs that command caller to that Terrygotchi. Can't use an existing name. Can only be called occasionally. Can't use name on Leaderboards either. Otherwise, allowed.   
    - zNoTerry - Unpairs the player from their currently paired Terrygotchi.
    - zFeed Cake - Spawns a cake above your Terrygotchi.
    - zFeed Potato - Spawns a potato above your Terrygotchi.
    - zClean - Spawns a bleach jug above the oldest poop in session.
    - zMedicine Painkiller - Spawns a pill above your Terrygotchi.
    - zMedicine Antiviral - Spawns a syringe above your Terrygotchi.
    - zPlay - Initiates a countdown that then begins a new mini-game inside the chat box. Can only be called occasionally. All other zPlay are ignored until reset by Play Timer.
    - zDiscipline Spank - Spawns a spanking gloved hand above your Terrygotchi.
    - zDiscipline Tickle - Spawns a tickling gloved hand above your Terrygotchi.
    - zHowAreYou - Spawns the Thought-Bubble above your Terrygotchi that expresses Happiness and HP.
    
## Details

Rules are grouped by scope. **Session-Scope** = one instance across the whole session (host-side singleton). **Per-Terrygotchi-Scope** = one instance per Terrygotchi. 

## Saving
For the host, the session is saved. In a host save file is:
All Terrygotchis currently spawned and alive with their current primary and other values and a list of players tied to them. If they are dead but Permadeath is off, they will be saved. (Death status is not saved). In addition the following is also saved: Statuses like Illness, and Misbehaving. Averages (for SageTerry), Antiviral accumulation.
For the client, the session is saved only upon disconnect. In a client save file is:
Same as the host EXCEPT only Terrygotchi that the client had paired to at least once during the session. They will be saved with the state they were at during disconnect.

### Session-Scope

**Session Constants:**
  - PoopWeight: 5
  - AdultSpeed (int): Min: 0, Max: inf., Initial: 10
  - AdultAcceleration (int): Min: 0, Max: inf., Initial: 80


**Session Timers:**
 - Death Timer: Every X (default 10) seconds, checks to see if any Terrygotchis HPs are below zero. If so, the Terrygotchi dies.
 - Evolve-Up Timer: Every X (default 2) seconds, checks to see if the Age value of any Terrygotchis are high enough for them to evolve. If so, puts them in 'Evolving' state.
 - Play Timer: Every X (default 120) seconds, allows 'Play' action to be used. Timer restarts at the end of the game.
 - Day/Night Cycle Timer: Every X (default 300) seconds of daytime, the whole session swaps to nighttime state. "Sleep" call is executed on all Terrygotchis. Nighttime lasts Y (default 30) seconds, after which the session returns to daytime. Like the Play Action, when a Terrygotchi is deciding next action, "Sleep" overrides everything, even "Play". Terrygotchis will go into "Sleep" state until nighttime is over.
    - When a Terrygotchi has Sleep state, they:
       1. Will not move.
       2. Any live games are canceled.
       3. Terrygotchis will not poop, eat, consume medicine, nor respond to tickling or HowAreYou calls. BUT if they have 'illness' status or 'Misbehaving' status, those remain and the impacts continued. For example, terrygotchi can die in their sleep if falling asleep while ill. All timers are paused at night except for: Aging, Day/Night Cycle, and Death timer.
       4. At the end of the sleep cycle, all Terrygotchi (except Sausages) get +5 HP and +2 Happiness, but not enough Happiness to make you sick.
 - Toxic Poop Timer: Every X (default 2) seconds, checks to see if poop becomes toxic by checking its lifetime. If lifetime > X (default 60) seconds, it becomes toxic.
 - Play Countdown Timer: When zPlay is issued, a X (default 5) second countdown precedes the mini-game start.
 - Play Vote Timer: During each round of the Play Game, chat submissions are collected for X (default 10) seconds; at expiry the median submission is taken as the round's guess.
 - New Terry Timer (per-player within a session): A player may call zNewTerry only once every X (default 300) seconds.

**Session Systems:**
 - Play Game: A Twitch-chat Hi/Low guessing mini-game played by every Terrygotchi in the session. Any chat containing solely a valid integer is accepted.
   - Round 1: Target is hidden between 1 and 10. Chat collectively guesses (median over the Play Vote Timer window). Response: "High", "Low", or "Correct". A correct guess at Round 1 ends the game and grants +3 Happiness and -2 Weight to every Terrygotchi in the session.
   - Round 2 (if Round 1 incorrect): The range is bracketed by the previous guess (if too high: "between 1 and Guess"; if too low: "between Guess and 10"). A correct guess at Round 2 ends the game and grants +2 Happiness and -1 Weight to every Terrygotchi in the session.
   - Round 3 (if Round 2 incorrect): Same bracketing. A correct guess at Round 3 grants +1 Happiness and -1 Weight to every Terrygotchi. An incorrect Round 3 guess has no penalty.
 - Leaderboards: 2 Values, Terrygotchi name and its age in seconds.
 - Naming System: Once a player joins a session, they can choose to care for a spawned Terrygotchi via "zMyTerry", create a new Terry via "zNewTerry" OR spawn their own Terrygotchi via zResume. A player can only pair to one Terrygotchi at a time, so zMyTerry and zNewTerry and zResume aren't available again until the player sends zNoTerry.

### Per-Terrygotchi-Scope

**Sausage (pre-state):** Just a Terry model without arms and legs and 2 rigidbodies to simulate a bendy sausage. Evolves into BabyTerry after a period of time. If the player's real Terry has a hat, the sausage has their hat. Has no mass and can be pushed around by other Terrygotchi. Cannot die, stats are static (except for age) and unaffected by everything. But will sleep during sleep time. Is 10% of engine default size.

**Primary Terrygotchi Values:**
 - Hunger (int): Min: -1, Max: inf., Initial: 0
 - Happiness (int): Min: -1, Max: inf., Initial: 10
 - Weight (int): Min: -1, Max: inf., Initial: 0
 - Age (Seconds, int):  Min: -1, Max: inf., Initial: 0
 - HP (int): Min: -1, Max: inf., Initial: 10

 **Other Terrygotchi Values**
 - Spanked (int): Min: 0, Max: inf., Initial: 0
 
**Rates (apply to each Terrygotchi based on its current life stage):**
 - Hunger:      BabyTerry: +1 / 10 Seconds, ChildTerry: +1 / 20 Seconds, TamaTerry: +1 / 30 Seconds, AdultTerry: +1 / 60 Seconds, SageTerry: +0 / 0 Seconds (sage hunger permanently at 0)
 - Happiness:   BabyTerry: -1 / 10 Seconds, ChildTerry: -1 / 20 Seconds, TamaTerry: -1 / 30 Seconds, AdultTerry: -1 / 60 Seconds, SageTerry: +0 / 0 Seconds (sage happiness permanently at 10)
 - Weight:      BabyTerry: Weight-Check / 60 Seconds, ChildTerry: Weight-Check / 90 Seconds, TamaTerry: Weight-Check / 100 Seconds, AdultTerry: Weight-Check / 120 Seconds, SageTerry: +0 / 0 Seconds (Weight permanently at 1)
 - Age:         +1 per Second
 - HP:          Recover-Check / 60 Seconds

**Cumulative Age Req. >= Before Evolving:**
 - Sausage: 60 Seconds
 - BabyTerry: 120 Seconds
 - ChildTerry: 210 Seconds
 - TamaTerry: 300 Seconds
 - AdultTerry: Doesn't evolve.
 - SageTerry: Doesn't evolve.
 Note: These are 1x values. These values need to be multiplied by a shared factor to appropriately reflect the 2 Leaderboard-supporting modes. (Players can customize down to 0, with floating point values allowed)


**Wandering (light mechanic, per stage):**
A light, ambient mechanic to breathe life into Terrygotchi: they periodically wander to a new spot on the navmesh. Driven by these variables, all per-stage EXCEPT MinDistance (a single stage-independent value):
 - WalkSpeed: movement speed when wandering normally.
 - RunSpeed: movement speed when the wander becomes a run.
 - MaxDistance: farthest a wander target can be.
 - MinDistance: nearest a wander target can be (stage-independent).
 - RunChance: chance a given wander is a run instead of a walk.
 - WanderChance: chance, each tick, to start a wander.
How it works: every second (session tick), there is a WanderChance that the Terrygotchi picks a new navmesh location between MinDistance and MaxDistance and moves there. If it does wander, there is a RunChance it runs (RunSpeed) instead of walking (WalkSpeed). Sausages do not wander.
The numbers below are RELATIVE RANKS from 1 (lowest) to 5 (highest). They are NOT percentages or real values (5 is not 100%, 1 is not 0%); real values come from playtesting. They only express relative intent.
 - BabyTerry:  WalkSpeed 1, RunSpeed 3, MaxDistance 5, RunChance 2, WanderChance 3.
 - ChildTerry: WalkSpeed 2, RunSpeed 3, MaxDistance 5, RunChance 3, WanderChance 4.
 - TamaTerry:  WalkSpeed 3, RunSpeed 5, MaxDistance 5, RunChance 4, WanderChance 5.
 - AdultTerry: WalkSpeed 2, RunSpeed 4, MaxDistance 5, RunChance 2, WanderChance 2.
 - SageTerry:  WalkSpeed 2, RunSpeed 4, MaxDistance 3, RunChance 2, WanderChance 2. (The specific SageTerry type may modify these.)
 - Sausage:    does not wander.

**Thought-Bubble (Terrygotchi-Player Communication) **
Situations call for communicating to the player details about their Terrygotchi. Sometimes prompted via zHowAreYou, sometimes not prompted.

Prompted or not prompted, each "ThoughtBubbleSpawnInstance" is placed into a queue, so if multiple calls for a thought-bubble to spawn are called, each is given it's time for the player to read it.
 - Prompted: This occurs through the zHowAreYou call. Further details for that can be found in the descriptions for that call.
 - Not Prompted: The following situations result in a not prompted thought-bubble to be queued up:
  - Misbehave State Started -> Text renders "#$^@% $&@!"
  - Hunger has gone above X (default 10) -> "( ￣﹃￣)" with a cooldown of X (default 300 seconds).
  - Hunger has gone below 2 -> "(๑ᵔ⤙ᵔ๑)" with a cooldown of X (default 300 seconds).
  - Happiness has gone above 8 -> "٩(◕‿◕｡)۶" with a cooldown of X (default 300 seconds).
  - Happiness has gone below 2 -> "(º﹃º )" with a cooldown of X (default 300 seconds).
  - If the Wander is a Running Wander -> "?" with a cooldown of X (default 300 seconds).
  - If the Terrygotchi dies -> "x_x" with a cooldown of X (default 300 seconds).
  - When the Terrygotchi sleeps -> "zzzzz" with a cooldown of X (default 300 seconds).
  - When the Terrygotchi wakes up -> "(^o^)" with a cooldown of X (default 300 seconds).
 

**Actions (each targets one Terrygotchi via the issuing player's pairing):**
 - Feed:
  - Feed Potato: Spawns Potato, if eaten: -1 Hunger, +1 Weight.
  - Feed Cake: Spawns Cake, if eaten: -1 Hunger, +2 Weight, +1 Happiness.
 - Clean: Spawns a bleach model that, if collides w item: deletes item.
 - Play (per-Terrygotchi state; underlying game is session-scoped — see Play Game above): When the session has an active Play Game, every Terrygotchi enters "Play" state. "Play" overrides next-action selection (except for "Sleep"). Terrygotchis remain in "Play" state until the game ends. Disables the Play action being issued again until the Play Timer re-enables it.
 - Feed Medicine:
  - Feed Painkillers: Spawns Painkiller, if eaten: +1 Happiness and +1 HP.
  - Feed Antivirals: Spawns Antiviral, if eaten, permanently add +X (default 1) second(s) to the Illness Timer check for that Terrygotchi. Gains 'HasAntiviralEaten' status for X (default 60) seconds wherein the Terrygotchi will not eat another Antiviral. Can be increased indefinitely.
 - Discipline:
  - Tickle: Spawns tickling gloved hand, upon collision gives the Terrygotchi +2 Happiness.
  - Spank: Spawns spanking gloved hand, upon collision, gives the Terrygotchi -1 Happiness.
 - HowAreYou: Text rendered with the Thought-Bubble animated above head, first showing 1 - 4 heart emoticons "<3" (representing HP), then 1 - 4 smiley faces ":)" (representing Happiness). Thresholds:
   - Hearts (HP):           HP <= 3: 1 Heart; HP >= 4 and HP <= 5: 2 Hearts; HP >= 6 and HP <= 8: 3 Hearts; HP > 8: 4 Hearts.
   - Smiley Faces (Happy):  Happiness <= 3: 1 Smiley Face; Happiness >= 4 and Happiness <= 5: 2 Smiley Faces; Happiness >= 6 and Happiness <= 8: 3 Smiley Faces; Happiness > 8: 4 Smiley Faces.
 - (Sleep is not an 'action', just happens automatically given the Day/Night Cycle Timer under Session-Scope.) !! todo: this may need to change, terms-wise. Sleeping may need to be defined as an action now. Considering earlier i described anything that depends on time a "action".

**Per-Terrygotchi Timers:**
 - Averages Tracking Timer: Every X (default 10) seconds, throughout the life of this Terrygotchi once it becomes a BabyTerry, average its Hunger, Happiness, and Weight values.
 - Illness Timer: Every X (default 60) seconds, checks to see if any Happiness, Hunger, or Weight of this Terrygotchi are below 0 or above 10. If so, it gets 'Illness' state.
 - Misbehave Timer: Every X (default 20) seconds, gives a 10% chance to apply "Misbehaving" state that stacks with other states of this Terrygotchi. During Misbehaving state, any single instance of its happiness going up causes an additional -1 Happiness to occur. Colliding w a Tickle glove or Spank glove ends the Misbehaving state.
 - Immortality Timer: A Terrygotchi cannot lose HP until the Immortality timer has finished. Lasts for X (default 120) seconds.

**Per-Terrygotchi Systems:**
 - Death: When a Terrygotchi dies, it plays a death animation with dead eyes. Stops aging and being affected by all timers, rates and systems other than Death. Will "Revive" at X (default 10) HP unless Permadeath is enabled. Since a 'Revive' is just the end of the death animation, its 'Respawn Location' is just wherever it died. Hunger revives at X (default 0), Happiness revives at X (default 10), Weight at X (default 0). Doesn't impact Player-Terrygotchi pairing so long as Permadeath is off.
 - Toxic Poop: When a Terrygotchi collides with Toxic Poop, the Terrygotchi gains -5 Happiness on every collision. The Toxic Poop is not deleted on contact; it persists and is only removed by bleach (zClean).
 - Weight-Check: If Weight > PoopWeight, spawn poop at location, animate pooping, and reduce weight by PoopWeight.
 - Sage System: Using results from the Averages Tracking Timer, when a TamaTerry evolves, it will become a SageTerry instead of a AdultTerry of a specific type if one of the following conditions are met (random if multiple are met):
  - Angel SageTerry: If over the course of its life, the happiness values maintained an average of 9 or greater.
  - Devil SageTerry: If you spanked your Terrygotchi X (default 100) times or more.
  - Bone SageTerry: If over the course of its life, the hunger value maintained an average of 9 or greater.
  - Steel SageTerry: If over the course of its life, the Weight value maintained an average of 3 or less.
 - Evolving: When the Evolve-Up timer puts a Terrygotchi into the 'Evolving' state, an animation occurs for X (default 3) seconds and then the Terrygotchi becomes the next Evolution stage up. Unless it's an Adult or Sage.
 - Adult System: Based on final values (instead of lifetime averages like SageTerry) when evolving from TamaTerry to AdultTerry, the final Terry will have the additional final cosmetics of 8, and if multiple conditions are satisfied, the Adult type is random:
   (High > 5, Low < 6, --- = not tracked.)
  - 1. SwordAdult:      Happiness High, Weight Low, Hunger ---      Carries a sword in the right hand and a shield on the back.
  - 2. SwimAdult:       Happiness Low,  Weight Low, Hunger Low      A Beachwear cosmetic.
  - 3. InvisibleAdult:  Happiness ---,  Weight Low, Hunger High     Body doesn't render.
  - 4. FatAdult:        Happiness High, Weight High,Hunger High     Width is scaled up and height shortened and speed slowed.
  - 5. NormalAdult:     Happiness ---,  Weight ---, Hunger ---      Always possible. No changes.
  - 6. HipsterAdult:    Happiness Low,  Weight High,Hunger High     Wears extra large glasses.
  - 7. GamerAdult:      Happiness ---,  Weight High,Hunger Low      Has videogame controllers for hands.
  - 8. RealAdult:       Happiness Low,  Weight High,Hunger Low      Uses the SBox Human Model (not the Citizen Terry model).
 - Revive Timer: Triggers at death of the Terrygotchi. If Permadeath is off, will revive the Terrygotchi after X (default 200) seconds. The Terrygotchi's death animation ends and it resumes normal animation and navmesh-agent control.
 - Recover-Check: +1 HP (frequency defined in "Rates: HP").

Design Intentions:
 - Too much of a good thing is a bad thing. Cake, Tickle, and Painkiller SHOULD push happy Terries into illness if done too much.

Sessions:
- When a Host first opens a new server, and 'OK' the configurations, a Session begins.
- Terrygotchis are completely independent of sessions.
- No timers persist across sessions.
 
DESIGN NOTES:Add spinning cat rave secret meme. Players do not HAVE to spawn their own Terrygotchi. Instead they can just help care for another. Old-Age system idea, where AdultTerry start losing HP over time? And SageTerry get a less demanding Old-Age system? PartySage idea, where if during Sausage stage, a mini game was occurring, chance of evolving into PartySage.

CAVEATS AND EXTRA DETAILS:
 - SteelTerry may be too easy/common.
 - NormalAdult isn't a fallback. Simply just always in the mix.
 - Section 1 is intended to be more a plain english explanation. Mechanics for all in section 1 w numeric detail should be found in the '# Details' section.
 - Negative-value icon display. Happiness/HP at -1 → 1 icon.
 - Evolution animation timing detail stays in the "Systems" section.
 - Sleep wording duplicate the same rule in prose, this is intentional.
 - HP has no documented upper cap, this is intentional.
 - Misbehave Timer prose — paraphrase duplication, this is intentional.
 - 8 Adult rules don't enumerate every High/Low/--- combination, this is intentional.
 - A 200 Age Terrygotchi that dies will still be 200 when revived.
