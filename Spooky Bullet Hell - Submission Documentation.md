# Spooky Bullet Hell — Project 1 Documentation

**GitHub:** https://github.com/Ztaco10/Spooky_Bullet_Hell  
**Unity version:** 6000.3.23f1 (Unity 6.3 LTS)  
**Build platform:** Windows

## Game overview

Spooky Bullet Hell is a top-down 2D survival game set in a dark rectangular arena. The player moves with WASD and aims a flashlight with the mouse. Normal bullets travel into the arena from its edges, while invisible homing ghosts spawn from the left or right. A ghost spawn plays a sound and briefly displays a directional arrow. The flashlight reveals and slows ghosts; building up one second of exposure removes them, while exposure quickly decays when the light moves away. Survival time is the score, and bullet spawning becomes more frequent as the run continues. One hit ends the run with the current health setting. Escape opens the pause menu.

## Script summaries

| Script | Purpose |
|---|---|
| PlayerMovement.cs | Reads the Move action from an Input Actions asset and applies Rigidbody2D velocity. Normalizes input so diagonal movement is not faster and exposes movement speed for ghost behavior. |
| FlashlightAim.cs | Reads mouse position through the Input System, converts it into world coordinates with the camera, and rotates the flashlight pivot toward the cursor. Stops aiming updates while paused. |
| PlayerHealth.cs | Initializes Inspector-configurable health, receives damage, disables movement when health reaches zero, and tells RunManager to end the run. |
| BulletProximityWarning.cs | Despite its original name, now exclusively shows the arrow toward a newly launched ghost for 0.75 seconds. Another ghost launch replaces the target and resets the timer; pausing freezes it. It does not detect nearby normal bullets. |
| Bullet.cs | Launches a normal projectile, moves it using Rigidbody2D, and deactivates it after its lifetime or a hit on the player. Uses OnTriggerEnter2D to apply damage. |
| BulletPool.cs | Creates normal bullet instances once, then reuses inactive instances for new shots. Skips a shot when the pool is full. |
| EdgeSpawner.cs | Selects a random arena edge and fires a pooled normal bullet toward the player's position at firing time. Gets the current firing interval from DifficultyController. |
| GhostBullet.cs | Homes toward the player and checks whether it is inside the flashlight cone and range. Controls visibility, slower movement when illuminated, exposure accumulation and decay, the exposure bar, removal, and trigger damage. |
| GhostBulletPool.cs | Creates and reuses up to four ghost instances, reserves pending launches, assigns runtime references, and plays the assignable spawn sound with centered stereo. Triggers the ghost warning arrow and pauses/resumes its audio with gameplay. |
| GhostSpawner.cs | Schedules ghosts at random positions along the left or right arena edge. Uses DifficultyController for the spawning interval. |
| DifficultyController.cs | Uses survival time to gradually reduce normal and ghost spawn intervals between configurable starting and ending values. |
| RunManager.cs | Tracks survival time and run state, ends the run, and saves/reloads the longest survival time using PlayerPrefs. |
| GameHUD.cs | Updates survival and best-time text, shows final results after death, and reloads Gameplay when Restart is selected. |
| PauseMenu.cs | Handles Escape and the Canvas pause panel. Freezes gameplay with Time.timeScale = 0 and provides Resume, Restart, and Quit to Main Menu. |
| MainMenuController.cs | Loads Gameplay from Start, exits the application from Quit, and restores normal time scale when entering the menu or starting a game. |

## Scenes and key GameObjects

### MainMenu

MainMenu is the first scene in the build. MainMenuCanvas contains the title, Start and Quit buttons, controls, and survival instructions. Its MainMenuController receives button events: Start loads Gameplay, and Quit closes the application. EventSystem processes Canvas input through the Input System UI module, and Main Camera provides the scene camera.

### Gameplay

- **Player:** Uses PlayerMovement, Rigidbody2D, Collider2D, and PlayerHealth for movement and damage. Its FlashlightPivot and Spot Light 2D follow mouse aim, while DimPlayerLight provides faint nearby illumination. WarningPivot/WarningArrow briefly indicates the latest ghost spawn.
- **Grid, Ground, and Walls:** Tilemaps form the arena floor and boundary. Walls uses Tilemap Collider 2D for collision with the player.
- **BulletPool and EdgeSpawner:** Reuse the Bullet prefab and fire normal projectiles from the arena edges toward the player.
- **GhostPool and GhostSpawner:** Reuse the GhostBullet prefab and schedule left/right ghost launches. Ghost instances contain a visual and exposure bar; the pool also manages spawn sounds and arrow notifications.
- **RunManager and DifficultyController:** Track run state and score, save the best time, and increase spawn frequency as survival time rises. Spawners stop scheduling enemies when the run ends.
- **GameCanvas:** Displays current and best survival times. GameHUD shows GameOverPanel and final results after death; PauseMenu manages PausePanel and its Resume, Restart, and Main Menu buttons.
- **Main Camera, Global Light 2D, and player lights:** Present the arena from above. Global lighting is set to zero, leaving the flashlight and dim player light to reveal the environment and lit sprites.
- **BackgroundMusic:** An Audio Source automatically plays looping, non-directional background music at low volume.
- **EventSystem:** Processes menu and HUD button input.

The project also contains a URP2DSceneTemplate scene supplied by the template; it is not included in the submitted build's scene list.

## How the eight requirements are satisfied

1. **Scenes and scene management:** MainMenu and Gameplay are included in the build; Start loads Gameplay, and the pause menu's Quit to Main Menu returns to MainMenu.
2. **Input System and 2D physics:** PlayerMovement uses an InputActionReference from an Input Actions asset and moves a player equipped with Rigidbody2D and Collider2D; mouse aim and Escape also use the new Input System.
3. **Tilemap level:** Ground and Walls Tilemaps define the rectangular arena, with Tilemap Collider 2D on Walls providing player collision.
4. **Prefab pooling:** BulletPool and GhostBulletPool create projectile prefab instances once and reuse them by deactivating and relaunching them rather than repeatedly creating and destroying them.
5. **Layers and trigger interactions:** Normal bullets and ghosts use OnTriggerEnter2D to damage PlayerHealth; the project also defines Player, World, and EnemyBullet physics layers.
6. **Canvas pause menu:** Escape opens a Canvas pause menu and sets Time.timeScale to zero; Resume, Restart, and Quit to Main Menu are available.
7. **PlayerPrefs persistence:** RunManager saves the longest survival time under LongestSurvivalTime and reloads it when Gameplay starts, allowing the best score to persist across application sessions.
8. **Lighting/material effect:** URP Light2D components provide mouse-directed flashlight illumination and faint local lighting in an otherwise dark arena; illumination also reveals and slows ghosts.

## Assets, packages, and assistance

- **Unity packages:** Universal Render Pipeline 17.3.0 supplies 2D lighting and rendering; Input System 1.20.0 supplies player and UI input; Unity UI (uGUI) 2.0.0 and TextMeshPro provide Canvas menus and text; 2D Tilemap and Sprite tooling support the arena and sprite workflow. Other template packages are installed but are not claimed as gameplay features.
- **Editor tooling:** Unity Pipeline 0.8.0-exp.1 and the Unity CLI were used to inspect and configure the project during development.
- **Background music:** “Spooky Scary Skeletons (Remix) - Extended Mix.mp3” is used as looping gameplay music. Creator/remixer and source: **The Living Tombstone**.
- **Ghost spawn sound:** A shortened GhostBulletSound clip is used when a ghost launches; the project includes a file marked as trimmed through mp3cut.net. Original creator and source: **Phasmophobia**.
- **Artwork:** GroundTile.png and WallTile.png provide arena tiles; player and projectile visuals use the project's assigned sprites. Artwork origin and any edits: **Map design by: Brady Truong**  **Sprite artowrk made by artist: TUTU**.
- **Course resources/tutorials:** **Code-alongs from weeks 2-7 used for player movment, score tracking, highscore tracking, pooling, bullets, pause menu, main menu, and buttons**.
- **AI assistance:** OpenAI Codex assisted with gameplay design, explanations, C# code generation, troubleshooting, and some Unity Editor configuration. Development was completed step by step, with implementation, code revisions and playtesting by the student.

## Build testing

The final Windows build was extracted into a separate folder and tested outside the Unity Editor. Checks included scene navigation, movement, aiming, projectile behavior, ghost warnings and removal, pause/resume, restart, audio, quitting, and best-score persistence after closing and reopening the application.
