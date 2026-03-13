# Implementation Plan: Flappy Kiro

## Overview

This implementation plan breaks down the Flappy Kiro game into discrete coding tasks following a component-based architecture. The game will be built with vanilla JavaScript and HTML5 Canvas, targeting 60 FPS performance with centralized configuration management.

The implementation follows this sequence:
1. Project structure and configuration system
2. Core game engine and rendering foundation
3. Physics and collision detection
4. Game entities (Ghost, Pipes, Particles)
5. Audio and visual effects
6. Game state management and UI
7. Integration and polish

## Tasks

- [ ] 1. Set up project structure and configuration system
  - Create HTML file with canvas element (800x600px)
  - Create main JavaScript file with module structure
  - Create config directory and default game-config.json file
  - Implement GameConfig class with load(), get(), and category getter methods
  - Add configuration validation and fallback to defaults
  - _Requirements: 1.1, 10.5_

- [ ] 2. Implement core game engine and rendering foundation
  - [ ] 2.1 Create GameEngine class with initialization and game loop
    - Implement constructor with canvas setup
    - Implement init() method to load GameConfig and assets
    - Implement startGameLoop() using requestAnimationFrame at 60 FPS
    - Implement update(deltaTime) and render() methods
    - Add frame timing and delta time calculations
    - _Requirements: 1.1, 1.5, 11.1_
  
  - [ ] 2.2 Implement background rendering with caching
    - Create BackgroundCache class for off-screen canvas
    - Render background color from GameConfig
    - Integrate background rendering into main render loop
    - _Requirements: 8.10_
  
  - [ ] 2.3 Implement PerformanceMonitor class
    - Track frame times over 60-frame window
    - Calculate average FPS
    - Log warnings when frame budget exceeded
    - _Requirements: 11.1, 11.2, 11.3_

- [ ] 3. Implement physics engine
  - [ ] 3.1 Create PhysicsEngine class
    - Implement applyGravity() using GameConfig gravity constant
    - Implement applyJump() using GameConfig ascent velocity
    - Implement clampVelocity() using GameConfig terminal velocity
    - Implement updatePosition() with linear interpolation
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6, 3.7, 3.8, 3.9, 3.10_
  
  - [ ]* 3.2 Write property tests for physics engine
    - **Property 4: Velocity clamping**
    - **Property 5: Gravity accumulation**
    - **Property 6: Position update consistency**
    - **Validates: Requirements 3.4, 3.5, 3.6, 3.7, 3.8**

- [ ] 4. Implement collision detection system
  - [ ] 4.1 Create CollisionDetector class
    - Implement aabbIntersect() for rectangle intersection
    - Implement checkCollision() for ghost-pipe collisions
    - Implement checkBoundaryCollision() using GameConfig canvas dimensions
    - Implement invincibility tracking using GameConfig duration
    - Add spatial partitioning for efficient pipe filtering
    - Cache hitbox calculations per frame
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8, 5.9, 5.10, 5.11, 5.19, 5.20, 5.22, 5.23, 11.4_
  
  - [ ]* 4.2 Write property tests for collision detection
    - **Property 7: AABB intersection symmetry**
    - **Property 8: Boundary collision detection**
    - **Property 9: Invincibility protection**
    - **Validates: Requirements 5.8, 5.9, 5.10, 5.11, 5.19, 5.20, 5.22**

- [ ] 5. Implement Ghost character
  - [ ] 5.1 Create Ghost class
    - Implement constructor with position and velocity from GameConfig
    - Implement jump() method to set ascent velocity
    - Implement update(deltaTime) to integrate with PhysicsEngine
    - Implement render(ctx) with sprite drawing
    - Implement getHitbox() using GameConfig dimensions
    - Implement setInvincible() with flashing effect at GameConfig frequency
    - Load ghost sprite from assets/ghosty.png
    - _Requirements: 1.2, 2.4, 5.1, 5.2, 5.21_

- [ ] 6. Implement pipe obstacle system with object pooling
  - [ ] 6.1 Create PipePool class for memory optimization
    - Implement constructor with pre-allocation of pipe objects
    - Implement acquire() to get pipe from pool
    - Implement release() to return pipe to pool
    - Implement createPipe() and resetPipe() helper methods
    - Use maxPipes from GameConfig for pool size
    - _Requirements: 11.2_
  
  - [ ] 6.2 Create PipeManager class
    - Implement generatePipe() using GameConfig spawn position and gap parameters
    - Implement updatePipes() to move pipes at current speed
    - Implement removePipe() to release pipes back to pool
    - Implement checkPipePassed() for score tracking
    - Implement increaseDifficulty() using GameConfig speed progression
    - Integrate PipePool for all pipe creation/destruction
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8, 4.9, 4.10, 8.9_
  
  - [ ]* 6.3 Write property tests for pipe generation
    - **Property 10: Gap positioning bounds**
    - **Property 11: Speed progression**
    - **Property 12: Pipe spacing consistency**
    - **Validates: Requirements 4.3, 4.4, 4.7, 4.8, 4.9**

- [ ] 7. Checkpoint - Verify core gameplay mechanics
  - Ensure physics, collision, and pipe generation work correctly
  - Verify object pooling eliminates GC pauses
  - Ensure all tests pass, ask the user if questions arise

- [ ] 8. Implement particle system with object pooling
  - [ ] 8.1 Create ParticlePool class
    - Implement separate pools for trail and score particles
    - Implement acquireTrailParticle() and acquireScoreParticle()
    - Implement releaseTrailParticle() and releaseScoreParticle()
    - Use LRU strategy when pool exhausted
    - Use maxCount values from GameConfig
    - _Requirements: 11.6, 11.7_
  
  - [ ] 8.2 Create ParticleSystem class
    - Implement createTrailParticle() using GameConfig parameters
    - Implement createScoreIndicator() using GameConfig parameters
    - Implement update(deltaTime) for particle animations
    - Implement render(ctx) with batched rendering
    - Implement clear(), pause(), and resume() methods
    - Integrate ParticlePool for all particle creation
    - _Requirements: 1.6, 6.5, 6.6, 6.7, 6.8, 6.9, 8.1, 8.2, 8.3, 8.4, 8.5, 8.6, 8.7, 11.3_
  
  - [ ]* 8.3 Write property tests for particle system
    - **Property 13: Particle lifetime**
    - **Property 14: Particle count limits**
    - **Property 15: Opacity fade linearity**
    - **Validates: Requirements 8.4, 8.6, 8.8, 11.6**

- [ ] 9. Implement score tracking system
  - [ ] 9.1 Create ScoreSystem class
    - Implement incrementScore() method
    - Implement getCurrentScore(), getSessionHighScore(), getAllTimeHighScore()
    - Implement saveHighScore() to localStorage with key "flappyKiroHighScore"
    - Implement loadHighScore() from localStorage
    - Implement reset() method
    - Handle localStorage unavailable gracefully
    - _Requirements: 6.1, 6.2, 6.10, 6.11, 6.12, 6.13, 6.14, 6.15, 6.16, 6.17_
  
  - [ ]* 9.2 Write property tests for score system
    - **Property 16: Score increment uniqueness**
    - **Property 17: High score persistence**
    - **Validates: Requirements 6.2, 6.15, 6.16**

- [ ] 10. Implement audio system
  - [ ] 10.1 Create AudioSystem class
    - Implement preload() for all audio assets
    - Implement playSound() with volume from GameConfig
    - Implement playBackgroundMusic() with looping
    - Implement pauseBackgroundMusic() and resumeBackgroundMusic()
    - Implement stopBackgroundMusic() with fade out using GameConfig duration
    - Handle audio loading failures gracefully
    - _Requirements: 1.3, 2.5, 2.6, 5.17, 5.18, 6.3, 6.4, 7.2, 7.4, 9.8, 9.9, 9.28, 9.32, 11.5_

- [ ] 11. Implement visual effects system
  - [ ] 11.1 Create VisualEffects class
    - Implement triggerScreenShake() using GameConfig amplitude and duration
    - Implement triggerRedFlash() using GameConfig opacity and duration
    - Implement triggerColorTint() using GameConfig parameters
    - Implement update(deltaTime) for effect timers
    - Implement getCanvasOffset() for screen shake
    - Use damped sine wave for screen shake pattern
    - _Requirements: 5.12, 5.13, 5.14, 5.15, 5.16_

- [ ] 12. Implement input handling
  - [ ] 12.1 Create InputHandler class
    - Implement bindEvents() for keyboard, mouse, and touch
    - Implement handleKeyDown() for SPACE and ESC keys
    - Implement handleMouseDown() for click events
    - Implement handleTouchStart() for touch events
    - Map inputs to game actions based on current state
    - _Requirements: 2.1, 2.2, 2.3, 7.14, 7.15, 9.7, 9.17, 9.24, 9.25, 9.27, 9.31_

- [ ] 13. Implement game state management
  - [ ] 13.1 Add state machine to GameEngine
    - Implement setState() method for state transitions
    - Implement menu state rendering with title and instructions
    - Implement playing state with active gameplay
    - Implement paused state with preserved game state
    - Implement game_over state with statistics display
    - Handle state-specific rendering and updates
    - _Requirements: 7.1, 7.3, 7.6, 7.7, 7.8, 7.9, 7.10, 7.11, 7.12, 7.13, 9.1, 9.2, 9.3, 9.4, 9.5, 9.6, 9.11, 9.12, 9.13, 9.14, 9.15, 9.16, 9.18, 9.19, 9.20, 9.21, 9.22, 9.23, 9.26, 9.29, 9.30, 9.33, 9.34_
  
  - [ ] 13.2 Implement reset() method for game restart
    - Reset ghost position and velocity using GameConfig
    - Clear all pipes and particles
    - Reset score to 0
    - Reset pipe speed to initial value from GameConfig
    - Grant invincibility period from GameConfig
    - _Requirements: 9.11, 9.12, 9.13, 9.14, 9.15, 9.16_

- [ ] 14. Implement UI rendering
  - [ ] 14.1 Add UI rendering methods to GameEngine
    - Render current score using GameConfig font sizes and colors
    - Render menu screen with title, high score, and instructions
    - Render game over screen with final score, session best, all-time best
    - Render "NEW HIGH SCORE!" message when applicable
    - Render paused screen with current score and instructions
    - Apply pausedOpacity from GameConfig to paused game objects
    - _Requirements: 6.10, 6.11, 7.6, 7.7, 7.8, 7.9, 7.10, 7.11, 7.12, 8.11, 8.12, 9.2, 9.3, 9.4, 9.5, 9.22, 9.23, 9.24, 9.25, 9.26_

- [ ] 15. Checkpoint - Verify complete game flow
  - Test all state transitions (menu → playing → paused/game_over)
  - Verify audio plays correctly in all states
  - Verify UI displays correctly in all states
  - Ensure all tests pass, ask the user if questions arise

- [ ] 16. Integration and optimization
  - [ ] 16.1 Wire all components together in GameEngine
    - Initialize all subsystems with GameConfig
    - Connect input events to game actions
    - Integrate physics → collision → rendering pipeline
    - Connect score events to audio and particle systems
    - Connect collision events to visual effects and audio
    - Implement sprite batching for pipes and particles
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6_
  
  - [ ] 16.2 Implement error handling
    - Handle GameConfig loading failures with fallback to defaults
    - Handle asset loading failures gracefully
    - Handle localStorage unavailable scenario
    - Handle canvas context unavailable
    - Add configuration validation with value clamping
    - _Requirements: 10.1, 10.2, 10.3, 10.4_
  
  - [ ]* 16.3 Write integration tests
    - Test GameEngine initialization with GameConfig loading
    - Test physics → collision → rendering pipeline
    - Test input → physics → audio feedback loop
    - Test score system → localStorage persistence
    - Test configuration changes affecting multiple subsystems

- [ ] 17. Performance optimization and testing
  - [ ] 17.1 Verify performance targets
    - Test with 4 pipes and verify 60 FPS maintained
    - Test with 50 particles and verify 60 FPS maintained
    - Verify collision detection completes within 1ms
    - Verify audio latency below 50ms
    - Use PerformanceMonitor to track frame times
    - _Requirements: 11.1, 11.2, 11.3, 11.4, 11.5_
  
  - [ ]* 17.2 Write performance tests
    - Test frame rate with maximum pipes from GameConfig
    - Test frame rate with maximum particles from GameConfig
    - Test collision detection timing
    - Test audio latency
    - Test configuration loading time

- [ ] 18. Browser compatibility and final polish
  - [ ] 18.1 Test cross-browser compatibility
    - Test on Chrome 90+
    - Test on Firefox 88+
    - Test on Safari 14+
    - Test on Edge 90+
    - Verify canvas centering and responsiveness
    - _Requirements: 10.1, 10.2, 10.3, 10.4, 10.5_
  
  - [ ] 18.2 Add pixel-perfect rendering
    - Disable image smoothing for retro aesthetic
    - Round all positions to integers
    - _Requirements: 8.13_
  
  - [ ] 18.3 Create multiple test configuration files
    - Create test-config-easy.json
    - Create test-config-hard.json
    - Create test-config-extreme.json
    - Test game behavior with different configurations

- [ ] 19. Final checkpoint - Complete game verification
  - Play through complete game sessions
  - Verify all requirements are met
  - Verify high score persistence across page reloads
  - Verify all visual and audio effects work correctly
  - Ensure all tests pass, ask the user if questions arise

## Notes

- Tasks marked with `*` are optional testing tasks and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Object pooling (tasks 6.1, 8.1) is critical for 60 FPS performance
- GameConfig system (task 1) must be completed first as all components depend on it
- Checkpoints (tasks 7, 15, 19) ensure incremental validation
- Property tests validate universal correctness properties from the design document
- Integration tests verify component interactions
- Performance tests ensure 60 FPS target is maintained
