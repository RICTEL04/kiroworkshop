# Requirements Document

## Introduction

Flappy Kiro is a retro-style browser-based endless scroller game where players guide a ghost character through a series of pipes. The game features simple one-button controls, progressive difficulty, score tracking, and audio feedback. The game runs entirely in the browser using HTML5 Canvas and JavaScript.

## Glossary

- **Game_Engine**: The core system that manages game state, rendering, and game loop
- **Ghost_Character**: The player-controlled sprite that moves through the game world
- **Pipe_Obstacle**: Vertical barriers with gaps that the Ghost_Character must navigate through
- **Game_Canvas**: The HTML5 canvas element where the game is rendered
- **Score_System**: The subsystem that tracks and displays player score
- **Audio_System**: The subsystem that plays sound effects and background music
- **Background_Music**: The looping audio track that plays during gameplay
- **Collision_Detector**: The component that detects intersections between game objects
- **Particle_System**: The subsystem that generates and animates visual particle effects
- **Particle_Trail**: Visual effect particles that follow the Ghost_Character movement
- **Score_Indicator**: Animated visual feedback displayed when points are scored
- **Screen_Shake**: Visual effect that temporarily offsets the Game_Canvas to simulate impact
- **Input_Handler**: The component that processes player input events
- **Game_State**: The current mode of the game (menu, playing, paused, game_over)
- **High_Score_Storage**: The browser localStorage mechanism for persisting high scores across sessions
- **Session_High_Score**: The highest score achieved in the current browser session
- **All_Time_High_Score**: The highest score ever achieved, persisted across browser sessions
- **Physics_Engine**: The subsystem that manages gravity, velocity, and movement calculations
- **Gravity_Constant**: The constant downward acceleration applied to the Ghost_Character
- **Ascent_Velocity**: The upward velocity applied when the player provides jump input
- **Terminal_Velocity**: The maximum falling speed of the Ghost_Character

## Requirements

### Requirement 1: Game Initialization

**User Story:** As a player, I want the game to load in my browser, so that I can start playing immediately.

#### Acceptance Criteria

1. WHEN the page loads, THE Game_Engine SHALL initialize the Game_Canvas with dimensions 800x600 pixels
2. WHEN the page loads, THE Game_Engine SHALL load the ghost sprite from assets/ghosty.png
3. WHEN the page loads, THE Audio_System SHALL preload jump.wav, game_over.wav, score.wav, and background_music.mp3 audio files
4. WHEN initialization completes, THE Game_Engine SHALL display the start menu with the game title and instructions
5. THE Game_Canvas SHALL render at 60 frames per second
6. WHEN the page loads, THE Particle_System SHALL initialize particle pools for trail and score effects

### Requirement 2: Ghost Character Control

**User Story:** As a player, I want to control the ghost character with simple input, so that I can navigate through obstacles.

#### Acceptance Criteria

1. WHEN the player presses the spacebar, THE Input_Handler SHALL trigger a jump event
2. WHEN the player clicks the mouse button, THE Input_Handler SHALL trigger a jump event
3. WHEN the player taps the screen, THE Input_Handler SHALL trigger a jump event
4. WHEN a jump event is triggered, THE Physics_Engine SHALL set the Ghost_Character velocity to the Ascent_Velocity
5. WHEN the Ghost_Character receives jump input, THE Audio_System SHALL play jump.wav with volume 0.6
6. THE Audio_System SHALL play jump.wav as a short upward swoosh sound with duration between 100 and 200 milliseconds

### Requirement 3: Physics System

**User Story:** As a player, I want realistic and predictable physics, so that I can master the game controls through practice.

#### Acceptance Criteria

1. THE Physics_Engine SHALL define the Gravity_Constant as 0.5 pixels per frame squared
2. THE Physics_Engine SHALL define the Ascent_Velocity as -8 pixels per frame
3. THE Physics_Engine SHALL define the Terminal_Velocity as 10 pixels per frame
4. WHILE the Game_State is playing, THE Physics_Engine SHALL apply the Gravity_Constant to the Ghost_Character velocity each frame
5. WHEN the Ghost_Character velocity exceeds the Terminal_Velocity, THE Physics_Engine SHALL clamp the velocity to the Terminal_Velocity
6. WHEN the Ghost_Character velocity is less than negative Terminal_Velocity, THE Physics_Engine SHALL clamp the velocity to negative Terminal_Velocity
7. THE Physics_Engine SHALL update the Ghost_Character y-position by adding the current velocity each frame
8. THE Physics_Engine SHALL use linear interpolation for position updates to ensure smooth movement between frames
9. WHEN the Ghost_Character velocity changes, THE Physics_Engine SHALL preserve momentum by maintaining velocity until modified by gravity or input
10. THE Physics_Engine SHALL calculate all physics updates before rendering each frame to ensure consistent behavior

### Requirement 4: Pipe Obstacle Generation

**User Story:** As a player, I want obstacles to appear continuously with increasing difficulty, so that the game remains challenging and engaging over time.

#### Acceptance Criteria

1. WHEN the game starts, THE Game_Engine SHALL generate the first Pipe_Obstacle at x-position 400 pixels
2. WHEN a Pipe_Obstacle moves off the left edge of the Game_Canvas, THE Game_Engine SHALL generate a new Pipe_Obstacle at x-position 900 pixels
3. THE Game_Engine SHALL maintain a horizontal spacing of 300 pixels between consecutive Pipe_Obstacle center points
4. THE Game_Engine SHALL position each Pipe_Obstacle with a random gap center between y-positions 150 and 450 pixels using uniform random distribution
5. THE Game_Engine SHALL create each Pipe_Obstacle with a vertical gap height of 180 pixels between the upper and lower pipe sections
6. THE Game_Engine SHALL render each Pipe_Obstacle with a width of 80 pixels
7. WHEN the game starts, THE Game_Engine SHALL set the initial Pipe_Obstacle movement speed to 3 pixels per frame
8. WHEN the player score reaches a multiple of 5, THE Game_Engine SHALL increase the Pipe_Obstacle movement speed by 0.5 pixels per frame
9. THE Game_Engine SHALL limit the maximum Pipe_Obstacle movement speed to 8 pixels per frame
10. WHILE the Game_State is playing, THE Game_Engine SHALL move all Pipe_Obstacle objects leftward at the current movement speed

### Requirement 5: Collision Detection

**User Story:** As a player, I want the game to detect when I hit obstacles with precise hitboxes and visual feedback, so that the game ends fairly and I understand what happened.

#### Acceptance Criteria

1. THE Collision_Detector SHALL define the Ghost_Character hitbox as a rectangle with width 34 pixels and height 34 pixels
2. THE Collision_Detector SHALL position the Ghost_Character hitbox centered on the Ghost_Character sprite with 3-pixel padding on all sides
3. THE Collision_Detector SHALL define each Pipe_Obstacle hitbox as two rectangles: upper pipe and lower pipe, each with width 80 pixels
4. THE Collision_Detector SHALL calculate the upper pipe hitbox from y-position 0 to the gap start position
5. THE Collision_Detector SHALL calculate the lower pipe hitbox from the gap end position to y-position 600
6. WHEN the Ghost_Character hitbox intersects with any upper pipe hitbox, THE Collision_Detector SHALL trigger a game over event
7. WHEN the Ghost_Character hitbox intersects with any lower pipe hitbox, THE Collision_Detector SHALL trigger a game over event
8. WHEN the Ghost_Character hitbox left edge is at x-position 0 or less, THE Collision_Detector SHALL trigger a game over event
9. WHEN the Ghost_Character hitbox right edge is at x-position 800 or greater, THE Collision_Detector SHALL trigger a game over event
10. WHEN the Ghost_Character hitbox top edge is at y-position 0 or less, THE Collision_Detector SHALL trigger a game over event
11. WHEN the Ghost_Character hitbox bottom edge is at y-position 600 or greater, THE Collision_Detector SHALL trigger a game over event
12. WHEN a collision is detected, THE Game_Engine SHALL render a red flash effect on the Game_Canvas for 200 milliseconds with 40% opacity
13. WHEN a collision is detected, THE Game_Engine SHALL apply a Screen_Shake effect with amplitude 10 pixels for 300 milliseconds
14. WHEN a collision is detected, THE Screen_Shake SHALL use a damped sine wave pattern that decreases amplitude from 10 pixels to 0 pixels over the duration
15. WHEN a collision is detected, THE Screen_Shake SHALL randomly offset the Game_Canvas x and y positions within the current amplitude bounds each frame
16. WHEN a collision is detected, THE Ghost_Character SHALL display a red color tint with 60% opacity for 200 milliseconds before the game over screen appears
17. WHEN a collision is detected, THE Audio_System SHALL play game_over.wav with volume 0.7
18. THE Audio_System SHALL play game_over.wav as a descending tone or crash sound with duration between 300 and 600 milliseconds
19. WHEN the game starts, THE Game_Engine SHALL grant the Ghost_Character invincibility for 1000 milliseconds
20. WHEN the Ghost_Character is invincible, THE Collision_Detector SHALL not trigger game over events
21. WHEN the Ghost_Character is invincible, THE Game_Engine SHALL render the Ghost_Character with 50% opacity flashing at 10 Hz frequency
22. THE Collision_Detector SHALL use axis-aligned bounding box intersection algorithm for all collision checks
23. THE Collision_Detector SHALL perform collision checks after physics updates and before rendering each frame

### Requirement 6: Score Tracking

**User Story:** As a player, I want to see my score increase and track high scores across sessions, so that I can track my progress and compete with myself over time.

#### Acceptance Criteria

1. WHEN the game starts, THE Score_System SHALL initialize the score to 0
2. WHEN the Ghost_Character passes the center point of a Pipe_Obstacle, THE Score_System SHALL increment the score by 1
3. WHEN the score is incremented, THE Audio_System SHALL play score.wav with volume 0.5
4. THE Audio_System SHALL play score.wav as a bright chime or bell sound with duration between 100 and 300 milliseconds
5. WHEN the score is incremented, THE Particle_System SHALL create a Score_Indicator at the Ghost_Character position
6. THE Score_Indicator SHALL display "+1" text in yellow color with 32-pixel font size
7. THE Score_Indicator SHALL animate upward at 2 pixels per frame for 30 frames
8. THE Score_Indicator SHALL fade from 100% opacity to 0% opacity over 30 frames using linear interpolation
9. WHEN the Score_Indicator animation completes, THE Particle_System SHALL remove the Score_Indicator
10. THE Score_System SHALL display the current score in the top center of the Game_Canvas
11. WHEN the game ends, THE Score_System SHALL display the final score on the game over screen
12. THE Score_System SHALL track the Session_High_Score as the highest score achieved in the current browser session
13. WHEN the page loads, THE Score_System SHALL retrieve the All_Time_High_Score from the High_Score_Storage
14. WHEN the High_Score_Storage contains no previous score, THE Score_System SHALL initialize the All_Time_High_Score to 0
15. WHEN the current score exceeds the All_Time_High_Score, THE Score_System SHALL update the All_Time_High_Score
16. WHEN the All_Time_High_Score is updated, THE Score_System SHALL persist the new value to the High_Score_Storage using localStorage key "flappyKiroHighScore"
17. THE Score_System SHALL display the All_Time_High_Score on the main menu screen

### Requirement 7: Game Over Handling

**User Story:** As a player, I want clear feedback when the game ends with detailed statistics, so that I can understand my performance and restart easily.

#### Acceptance Criteria

1. WHEN a game over event is triggered, THE Game_Engine SHALL change the Game_State to game_over
2. WHEN the Game_State changes to game_over, THE Audio_System SHALL play game_over.wav
3. WHEN the Game_State changes to game_over, THE Game_Engine SHALL stop all Pipe_Obstacle movement
4. WHEN the Game_State changes to game_over, THE Audio_System SHALL stop the Background_Music with a 500 millisecond fade out
6. WHEN the Game_State is game_over, THE Game_Engine SHALL display "Game Over" text in white color with 64-pixel font size
7. WHEN the Game_State is game_over, THE Game_Engine SHALL display the final score with label "Score:" in white color with 36-pixel font size
8. WHEN the Game_State is game_over, THE Game_Engine SHALL display the Session_High_Score with label "Session Best:" in white color with 28-pixel font size
9. WHEN the Game_State is game_over, THE Game_Engine SHALL display the All_Time_High_Score with label "All-Time Best:" in yellow color with 28-pixel font size
10. WHEN the Game_State is game_over and the final score equals the All_Time_High_Score, THE Game_Engine SHALL display "NEW HIGH SCORE!" text in yellow color with 32-pixel font size
11. WHEN the Game_State is game_over, THE Game_Engine SHALL display "Press SPACE to restart" instruction in white color with 24-pixel font size
12. WHEN the Game_State is game_over, THE Game_Engine SHALL display "Press ESC for menu" instruction in white color with 24-pixel font size
13. WHEN the Game_State is game_over, THE Particle_System SHALL clear all active Particle_Trail and Score_Indicator particles
14. WHEN the player presses spacebar and the Game_State is game_over, THE Game_Engine SHALL reset the game and change Game_State to playing
15. WHEN the player presses escape key and the Game_State is game_over, THE Game_Engine SHALL change Game_State to menu

### Requirement 8: Visual Rendering

**User Story:** As a player, I want a retro visual style, so that the game has a nostalgic aesthetic.

#### Acceptance Criteria

1. WHILE the Game_State is playing, THE Particle_System SHALL generate Particle_Trail particles at the Ghost_Character position every 2 frames
2. THE Particle_System SHALL create each Particle_Trail with a circular shape of radius 4 pixels
3. THE Particle_System SHALL initialize each Particle_Trail with white color at 60% opacity
4. THE Particle_System SHALL animate each Particle_Trail by decreasing opacity from 60% to 0% over 20 frames using linear interpolation
5. THE Particle_System SHALL animate each Particle_Trail by decreasing radius from 4 pixels to 1 pixel over 20 frames using linear interpolation
6. WHEN a Particle_Trail opacity reaches 0%, THE Particle_System SHALL remove the particle
7. THE Particle_System SHALL render all Particle_Trail particles before rendering the Ghost_Character sprite
8. THE Game_Engine SHALL render the Ghost_Character sprite at its current position each frame
9. THE Game_Engine SHALL render all Pipe_Obstacle objects with solid green color (#4CAF50)
10. THE Game_Engine SHALL render the background with a light blue color (#87CEEB)
11. THE Game_Engine SHALL render score text in white color with 48-pixel font size
12. THE Game_Engine SHALL render game over text in white color with 64-pixel font size
13. THE Game_Engine SHALL use pixel-perfect rendering without anti-aliasing for retro appearance

### Requirement 9: Game State Management

**User Story:** As a player, I want smooth transitions between game states with a main menu, pause capability, and persistent high scores, so that the game feels polished and I can control my gameplay experience.

#### Acceptance Criteria

1. WHEN the page loads, THE Game_Engine SHALL set the Game_State to menu
2. WHEN the Game_State is menu, THE Game_Engine SHALL display "Flappy Kiro" title text in white color with 72-pixel font size
3. WHEN the Game_State is menu, THE Game_Engine SHALL display the All_Time_High_Score with label "High Score:" in yellow color with 36-pixel font size
4. WHEN the Game_State is menu, THE Game_Engine SHALL display "Press SPACE to start" instruction in white color with 28-pixel font size
5. WHEN the Game_State is menu, THE Game_Engine SHALL display game controls instruction "SPACE / CLICK / TAP to jump" in white color with 20-pixel font size
6. WHEN the Game_State is menu, THE Game_Engine SHALL render the Ghost_Character sprite at a fixed position as a preview
7. WHEN the Game_State is menu and the player presses spacebar, THE Game_Engine SHALL change the Game_State to playing
8. WHEN the Game_State changes to playing, THE Audio_System SHALL start playing the Background_Music in a continuous loop
9. THE Audio_System SHALL play the Background_Music with volume 0.3
10. THE Background_Music SHALL be an upbeat retro-style music track suitable for endless runner gameplay
11. WHEN the Game_State changes to playing, THE Game_Engine SHALL reset the Ghost_Character position to x:100, y:300
12. WHEN the Game_State changes to playing, THE Game_Engine SHALL reset the Ghost_Character velocity to 0
13. WHEN the Game_State changes to playing, THE Game_Engine SHALL clear all existing Pipe_Obstacle objects
14. WHEN the Game_State changes to playing, THE Score_System SHALL reset the current score to 0
15. WHEN the Game_State changes to playing, THE Game_Engine SHALL reset the Pipe_Obstacle movement speed to the initial value of 3 pixels per frame
16. WHEN the Game_State changes to playing, THE Particle_System SHALL clear all existing particles
17. WHEN the player presses escape key and the Game_State is playing, THE Game_Engine SHALL change the Game_State to paused
18. WHEN the Game_State changes to paused, THE Game_Engine SHALL preserve all game object positions and velocities
19. WHEN the Game_State changes to paused, THE Audio_System SHALL pause the Background_Music
20. WHEN the Game_State changes to paused, THE Game_Engine SHALL stop updating physics and collision detection
21. WHEN the Game_State changes to paused, THE Particle_System SHALL pause all particle animations
22. WHEN the Game_State is paused, THE Game_Engine SHALL display "PAUSED" text in white color with 64-pixel font size
23. WHEN the Game_State is paused, THE Game_Engine SHALL display the current score with label "Current Score:" in white color with 28-pixel font size
24. WHEN the Game_State is paused, THE Game_Engine SHALL display "Press SPACE to resume" instruction in white color with 24-pixel font size
25. WHEN the Game_State is paused, THE Game_Engine SHALL display "Press ESC for menu" instruction in white color with 24-pixel font size
26. WHEN the Game_State is paused, THE Game_Engine SHALL render all game objects at their preserved positions with 70% opacity
27. WHEN the player presses spacebar and the Game_State is paused, THE Game_Engine SHALL change the Game_State to playing
28. WHEN the Game_State changes from paused to playing, THE Audio_System SHALL resume the Background_Music
29. WHEN the Game_State changes from paused to playing, THE Game_Engine SHALL restore all game object positions and velocities
30. WHEN the Game_State changes from paused to playing, THE Particle_System SHALL resume all particle animations
31. WHEN the player presses escape key and the Game_State is paused, THE Game_Engine SHALL change the Game_State to menu
32. WHEN the Game_State changes from paused to menu, THE Audio_System SHALL stop the Background_Music
33. WHEN the Game_State changes from paused to menu, THE Game_Engine SHALL discard the current game session
34. WHEN the Game_State changes from paused to menu, THE Particle_System SHALL clear all active particles

### Requirement 10: Browser Compatibility

**User Story:** As a player, I want the game to work in modern browsers, so that I can play without compatibility issues.

#### Acceptance Criteria

1. THE Game_Engine SHALL run in Chrome version 90 or later
2. THE Game_Engine SHALL run in Firefox version 88 or later
3. THE Game_Engine SHALL run in Safari version 14 or later
4. THE Game_Engine SHALL run in Edge version 90 or later
5. THE Game_Canvas SHALL be responsive and centered in the browser window

### Requirement 11: Performance Requirements

**User Story:** As a player, I want smooth gameplay, so that I can react precisely to obstacles.

#### Acceptance Criteria

1. THE Game_Engine SHALL maintain 60 frames per second during gameplay
2. WHEN rendering 4 or more Pipe_Obstacle objects, THE Game_Engine SHALL maintain 60 frames per second
3. WHEN rendering 50 or more active particles, THE Game_Engine SHALL maintain 60 frames per second
4. THE Game_Engine SHALL complete each collision detection check within 1 millisecond
5. THE Audio_System SHALL play sound effects with latency less than 50 milliseconds from trigger event
6. THE Particle_System SHALL limit the maximum number of active Particle_Trail particles to 100
7. THE Particle_System SHALL limit the maximum number of active Score_Indicator particles to 10
