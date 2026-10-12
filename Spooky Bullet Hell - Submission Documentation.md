# Spooky Bullet Hell — Project 1 Documentation

**Developer:** Brady Truong  
**GitHub repository:** https://github.com/Ztaco10/Spooky_Bullet_Hell  
**Unity version:** 6000.3.23f1 (Unity 6.3 LTS)  
**Build platform:** Windows

## Game overview and controls

Spooky Bullet Hell is a top-down 2D survival game in a rectangular arena. Move with **WASD**, aim the flashlight with the **mouse**, and press **Escape** to pause. Survival time is the score. The room begins bright and transitions to darkness, introducing the flashlight and nearby light aura. Normal projectiles and homing ghosts spawn at circular distances around the player, within the arena. Ghosts receive a directional sound and arrow warning before appearing; the flashlight reveals, slows, and eventually banishes them. Later threats include circling goblins that telegraph a lunge and flying witches that drop bombs, leaving temporary damaging areas. The camera follows the player while remaining within the arena boundaries.

## Script summaries

The project contains 22 gameplay scripts:

| Script | Responsibility |
|---|---|
| PlayerMovement.cs | Reads the Move action through an InputActionReference and applies Rigidbody2D velocity; normalizes input to prevent faster diagonal movement. |
| PlayerHealth.cs | Initializes configurable health, receives damage, disables movement on death, and ends the run through RunManager. |
| FlashlightAim.cs | Converts mouse position to world coordinates and rotates the flashlight pivot after camera movement; stops while paused. |
| BulletProximityWarning.cs | Points the arrow toward a pending ghost spawn, then tracks that ghost for a configurable duration. The single arrow shows the latest warning. |
| Bullet.cs | Moves normal projectiles in their launch direction, damages the player through a trigger, and deactivates after impact or expiration. |
| BulletPool.cs | Creates normal projectile instances once and reuses inactive bullets; skips shots when the pool is full. |
| EdgeSpawner.cs | Despite its original name, now spawns normal bullets on a circle around the player and aims them toward the player's position at firing time. |
| CircularSpawnPosition.cs | Finds points at a fixed radius around the player while respecting arena bounds and wall clearance. |
| GhostBullet.cs | Homes toward the player, detects flashlight exposure, smoothly slows and recovers speed, displays exposure progress, and deactivates after banishment or a hit. |
| GhostBulletPool.cs | Reuses ghosts, plays directional stereo warnings before delayed spawning, notifies the arrow, and manages pending launches and paused audio. |
| GhostSpawner.cs | Schedules circular ghost spawns after their introduction, using the current difficulty interval. |
| CirclingEnemy.cs | Controls goblin approach, orbit, warning, locked-direction lunge, damage, arena limits, and optional disappearance after a charge. |
| CirclingEnemySpawner.cs | Introduces and reuses goblins with configurable spawn radius, interval, and active limit; configures player collision filtering. |
| FlyingWitchSpawner.cs | Reuses a witch for horizontal flybys near the player and drops pooled bombs along its route. |
| BombHazard.cs | Shows a harmless falling-bomb warning, activates a timed damage zone using a filtered 2D overlap check, applies a damage cooldown, and deactivates on expiration. |
| CameraFollow2D.cs | Smoothly follows the player with configurable offset and clamps the camera view within arena limits. |
| MechanicIntroduction.cs | Uses survival time to fade room lighting, introduce the flashlight and aura, and unlock ghosts. |
| DifficultyController.cs | Gradually reduces normal and ghost spawn intervals between configured starting and ending values. |
| RunManager.cs | Tracks run state and survival time, ends the run, and saves and loads the longest survival time through PlayerPrefs. |
| GameHUD.cs | Displays current/best survival time, shows final results after death, and restarts Gameplay. |
| PauseMenu.cs | Toggles the Canvas pause panel with Escape, freezes time, and implements Resume, Restart, and Quit to Main Menu. |
| MainMenuController.cs | Handles Start and Quit and restores normal time scale when entering the menu or starting Gameplay. |

## Scenes, key GameObjects, and interactions

**MainMenu:** The first scene in the build. MainMenuCanvas contains the title, Start and Quit buttons, printed controls, and survival instructions. Button events call MainMenuController: Start loads Gameplay and Quit exits the application. Main Camera renders the scene, and EventSystem handles Canvas interaction through the Input System UI module.

**Gameplay:** The playable survival arena contains these connected systems:

- **Player:** PlayerMovement, Rigidbody2D, Collider2D, and PlayerHealth provide movement and damage handling. FlashlightPivot contains the aiming light and flashlight artwork; DimPlayerLight supplies the aura. WarningPivot/WarningArrow displays ghost direction warnings.
- **Grid / Ground / Walls:** Tilemaps define the floor and rectangular boundary. Walls uses Tilemap Collider 2D to block the player.
- **BulletPool / EdgeSpawner and GhostPool / GhostSpawner:** Spawners choose positions and request reusable projectiles from their pools. GhostPool also handles advance-warning audio and arrow notifications.
- **CirclingEnemySpawner / FlyingWitchSpawner:** Introduce pooled goblins, witch flybys, and bomb hazards as survival time advances.
- **RunManager / DifficultyController:** Maintain run state, score, persistence, and projectile spawn pressure. MechanicIntroduction controls lighting progression and the ghost unlock.
- **Main Camera:** CameraFollow2D follows the player. Global Light 2D, the flashlight, and the aura provide changing visibility.
- **GameCanvas:** GameHUD updates score text and GameOverPanel. PauseMenu controls PausePanel and its buttons. EventSystem routes UI input.
- **BackgroundMusic:** Plays looping gameplay music through an Audio Source.

When health reaches zero, PlayerHealth ends the run, RunManager updates the saved record if needed, and GameHUD displays the results. Restart reloads Gameplay; Quit to Main Menu returns to MainMenu. Only MainMenu and Gameplay are enabled in the build scene list.

## Satisfaction of the eight requirements

1. **Scenes and scene management:** MainMenu and Gameplay are connected through Start and Quit to Main Menu using SceneManager.
2. **Input System and 2D physics:** An Input Actions asset supplies movement through InputActionReference; the player moves using Rigidbody2D and has Collider2D collision.
3. **Tilemap layout:** Ground and Walls form the arena, with Tilemap Collider 2D on Walls providing collision.
4. **Prefab pooling:** BulletPool and GhostBulletPool create and reuse projectile prefab instances through activation/deactivation; goblins, witches, and bombs also use pooling.
5. **Layers and trigger interactions:** Normal bullets, ghosts, and goblins damage PlayerHealth through OnTriggerEnter2D; physics layers filter relevant collisions, and hazards use a player-layer overlap check.
6. **Canvas pause menu:** Escape opens a Canvas menu and sets Time.timeScale to zero; Resume, Restart, and Quit to Main Menu are implemented.
7. **PlayerPrefs persistence:** RunManager saves the longest survival time under LongestSurvivalTime and reloads it across application sessions; GameHUD displays the best time.
8. **Lighting/material effect:** URP Light2D components provide room lighting, a mouse-directed flashlight, and a local aura; flashlight exposure also changes ghost behavior.

## Assets, packages, tutorials, and assistance

- **Artwork:** Sprite artwork by **TUTU**, used for player, flashlight, projectiles, enemies, and arena visuals. Map design by **Brady Truong**. The bomb hazard's simple circular marker was generated programmatically during implementation.
- **Music:** “Spooky Scary Skeletons (Remix) - Extended Mix,” by **The Living Tombstone**, used as looping gameplay background music.
- **Ghost warning audio:** Sound from **Phasmophobia**, shortened using **mp3cut.net**, used for advance ghost warnings.
- **Course resources:** Weeks **2–7 code-alongs** were adapted for movement, score/high-score tracking, pooling, projectiles, scene transitions, pause/menu behavior, and buttons.
- **Runtime packages:** **Universal Render Pipeline 17.3.0** for 2D rendering and lighting; **Input System 1.20.0** for movement, mouse/keyboard input, and UI input; **Unity UI (uGUI) 2.0.0 / TextMeshPro** for Canvas menus and text; Unity's **2D Sprite and Tilemap tooling** for artwork and level layout. Additional template packages are installed but are not claimed as implemented gameplay features.
- **Development tooling:** **Unity Pipeline 0.8.0-exp.1** and the Unity CLI were used for Editor inspection and configuration.
- **AI assistance:** **OpenAI Codex** assisted with design discussions, C# implementation, troubleshooting, component/prefab configuration, and documentation. The student directed design decisions, adjusted settings, and playtested the game.

## Build testing

The final Windows build was extracted into a separate folder and tested outside the Unity Editor. Checks included scene navigation, movement, aiming, projectile behavior, ghost warnings and removal, pause/resume, restart, audio, quitting, and best-score persistence after closing and reopening the application.
