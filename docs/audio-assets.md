# Audio Assets Specification

## Overview

This document defines the audio design specifications for Flappy Kiro. All sounds follow a retro 8-bit/chiptune aesthetic to match the game's visual style while remaining pleasant and non-intrusive during extended gameplay sessions.

## Audio Format Requirements

### Technical Specifications
- **Format:** WAV (uncompressed) for sound effects, MP3 for background music
- **Sample Rate:** 44.1 kHz (CD quality)
- **Bit Depth:** 16-bit
- **Channels:** Mono for sound effects, Stereo for background music
- **File Size Target:** < 50 KB per sound effect, < 500 KB for background music

### Browser Compatibility
- Primary format: WAV (supported by all modern browsers)
- Fallback format: MP3 (for older browsers)
- Preload all audio assets on game initialization

## Sound Effects

### 1. Flap Sound (jump.wav)

**Purpose:** Played when the player presses jump input (spacebar, click, or tap)

**Duration:** 0.1 seconds (100ms)

**Audio Characteristics:**
- **Type:** Short upward whoosh
- **Frequency Range:** 800 Hz - 2000 Hz (rising pitch)
- **Envelope:** 
  - Attack: 0ms (instant)
  - Decay: 20ms
  - Sustain: 40ms
  - Release: 40ms
- **Waveform:** Square wave or sawtooth wave (retro chiptune style)
- **Pitch Bend:** Start at 800 Hz, rise to 2000 Hz over 60ms
- **Volume:** 0.6 (60% of max volume, configurable in game-config.json)

**Sound Design Notes:**
- Should feel light and airy, like a wing flap or air puff
- Quick and punchy to provide immediate feedback
- Not harsh or jarring (player will hear this hundreds of times)
- Slight reverb (10-15%) for depth

**Synthesis Parameters (for chiptune tools):**
```
Oscillator: Square wave (50% duty cycle)
Frequency: 800 Hz → 2000 Hz (linear sweep)
Duration: 100ms
ADSR: 0/20/40/40 ms
Filter: Low-pass at 3000 Hz
Effects: Reverb (15%, room size: small)
```

**Reference Sound:** Similar to a soft "fwip" or "poof" sound

---

### 2. Score Sound (score.wav)

**Purpose:** Played when the player successfully passes through a pipe gap

**Duration:** 0.2 seconds (200ms)

**Audio Characteristics:**
- **Type:** Pleasant chime/bell tone
- **Frequency Range:** 1000 Hz - 1500 Hz (harmonious interval)
- **Envelope:**
  - Attack: 5ms (soft onset)
  - Decay: 50ms
  - Sustain: 100ms
  - Release: 45ms
- **Waveform:** Sine wave or triangle wave (smooth, bell-like)
- **Harmony:** Two-note chord (perfect fifth interval: C5 + G5)
- **Volume:** 0.5 (50% of max volume, configurable in game-config.json)

**Sound Design Notes:**
- Should feel rewarding and positive
- Bright and clear without being piercing
- Musical quality (fits in a major key)
- Slight sparkle/shimmer effect

**Synthesis Parameters (for chiptune tools):**
```
Oscillator 1: Sine wave at 1046 Hz (C6)
Oscillator 2: Sine wave at 1568 Hz (G6)
Duration: 200ms
ADSR: 5/50/100/45 ms
Filter: None (pure tones)
Effects: Chorus (subtle), Reverb (20%, room size: medium)
```

**Reference Sound:** Similar to a music box chime or xylophone note

---

### 3. Collision Sound (game_over.wav)

**Purpose:** Played when the player collides with a pipe or boundary

**Duration:** 0.3 seconds (300ms)

**Audio Characteristics:**
- **Type:** Soft thud with descending tone
- **Frequency Range:** 400 Hz - 100 Hz (falling pitch)
- **Envelope:**
  - Attack: 0ms (instant impact)
  - Decay: 100ms
  - Sustain: 100ms
  - Release: 100ms
- **Waveform:** Noise burst + sine wave (impact + tone)
- **Pitch Bend:** Start at 400 Hz, fall to 100 Hz over 250ms
- **Volume:** 0.7 (70% of max volume, configurable in game-config.json)

**Sound Design Notes:**
- Should convey failure without being harsh or punishing
- Soft and muted, not aggressive
- Descending pitch creates "deflating" feeling
- Brief noise burst at start for impact sensation

**Synthesis Parameters (for chiptune tools):**
```
Layer 1 (Impact):
  - White noise burst
  - Duration: 30ms
  - ADSR: 0/30/0/0 ms
  - Filter: Band-pass at 800 Hz

Layer 2 (Tone):
  - Sine wave
  - Frequency: 400 Hz → 100 Hz (exponential decay)
  - Duration: 300ms
  - ADSR: 0/100/100/100 ms
  - Filter: Low-pass at 600 Hz

Mix: 30% noise, 70% tone
Effects: Reverb (25%, room size: medium)
```

**Reference Sound:** Similar to a soft "bonk" or "thump" with a sad trombone quality

---

## Background Music

### 4. Background Music (background_music.mp3)

**Purpose:** Looping music track during gameplay

**Duration:** 30-60 seconds (seamless loop)

**Audio Characteristics:**
- **Style:** Upbeat retro chiptune/8-bit
- **Tempo:** 120-140 BPM (energetic but not frantic)
- **Key:** C Major or G Major (positive, uplifting)
- **Instrumentation:** 
  - Lead melody: Square wave
  - Bass line: Triangle wave
  - Percussion: Noise channel (hi-hat, kick, snare)
  - Harmony: Pulse wave
- **Volume:** 0.3 (30% of max volume, configurable in game-config.json)
- **Loop Point:** Seamless (no audible gap when looping)

**Musical Structure:**
```
Intro: 4 bars (establish melody)
Verse A: 8 bars (main theme)
Verse B: 8 bars (variation)
Bridge: 4 bars (build energy)
Outro: 4 bars (return to loop point)

Total: 28 bars at 140 BPM ≈ 48 seconds
```

**Composition Guidelines:**
- Memorable melody that doesn't become annoying
- Moderate complexity (not too busy)
- Positive and encouraging mood
- Retro game aesthetic (NES/Game Boy era)
- No jarring transitions or sudden volume changes

**Reference Style:** Similar to classic arcade games (Pac-Man, Galaga) or early Nintendo titles

---

## Audio Implementation

### Volume Configuration

All volumes are configurable in `config/game-config.json`:

```json
{
  "audio": {
    "jumpVolume": 0.6,
    "scoreVolume": 0.5,
    "gameOverVolume": 0.7,
    "musicVolume": 0.3,
    "musicFadeOutDuration": 500,
    "maxSoundLatency": 50
  }
}
```

### Audio System Requirements

**Preloading:**
```javascript
const audioAssets = {
  jump: new Audio('assets/jump.wav'),
  score: new Audio('assets/score.wav'),
  gameOver: new Audio('assets/game_over.wav'),
  music: new Audio('assets/background_music.mp3')
};

// Preload all audio
Object.values(audioAssets).forEach(audio => {
  audio.load();
});
```

**Playback:**
```javascript
// Sound effect playback
function playSound(soundName, volume) {
  const audio = audioAssets[soundName].cloneNode();
  audio.volume = volume;
  audio.play();
}

// Background music with looping
audioAssets.music.loop = true;
audioAssets.music.volume = 0.3;
audioAssets.music.play();
```

**Fade Out:**
```javascript
function fadeOutMusic(duration) {
  const startVolume = audioAssets.music.volume;
  const fadeStep = startVolume / (duration / 16.67); // 60 FPS
  
  const fadeInterval = setInterval(() => {
    if (audioAssets.music.volume > 0.01) {
      audioAssets.music.volume -= fadeStep;
    } else {
      audioAssets.music.pause();
      audioAssets.music.volume = startVolume;
      clearInterval(fadeInterval);
    }
  }, 16.67);
}
```

### Latency Requirements

- **Maximum Latency:** 50ms (from trigger to audible sound)
- **Target Latency:** < 20ms for optimal responsiveness
- **Implementation:** Use Web Audio API for lower latency if needed

```javascript
// Web Audio API implementation (lower latency)
const audioContext = new (window.AudioContext || window.webkitAudioContext)();

function playBufferedSound(buffer, volume) {
  const source = audioContext.createBufferSource();
  const gainNode = audioContext.createGain();
  
  source.buffer = buffer;
  gainNode.gain.value = volume;
  
  source.connect(gainNode);
  gainNode.connect(audioContext.destination);
  
  source.start(0);
}
```

## Audio Creation Tools

### Recommended Software

**Free/Open Source:**
- **Bfxr** (https://www.bfxr.net/) - Browser-based retro sound effect generator
- **ChipTone** (https://sfbgames.itch.io/chiptone) - Chiptune sound effect tool
- **Audacity** - Audio editing and mixing
- **BeepBox** (https://www.beepbox.co/) - Chiptune music composition

**Commercial:**
- **FL Studio** - Full DAW with chiptune plugins
- **FamiTracker** - NES-style music tracker
- **Magical 8bit Plug** - VST plugin for chiptune sounds

### Sound Generation Workflow

1. **Generate base sound** using Bfxr or ChipTone
2. **Export as WAV** at 44.1 kHz, 16-bit
3. **Edit in Audacity:**
   - Trim silence
   - Normalize volume
   - Add effects (reverb, EQ)
   - Ensure exact duration
4. **Export final file** with proper naming
5. **Test in-game** at target volume levels

## Asset File Structure

```
assets/
├── jump.wav              (flap sound, ~10-20 KB)
├── score.wav             (score chime, ~15-25 KB)
├── game_over.wav         (collision sound, ~20-30 KB)
└── background_music.mp3  (looping music, ~300-500 KB)
```

## Testing Checklist

### Sound Effects
- [ ] Flap sound plays instantly on jump input (< 50ms latency)
- [ ] Flap sound is not harsh when heard repeatedly
- [ ] Score sound is pleasant and rewarding
- [ ] Score sound doesn't overlap/clip when scoring rapidly
- [ ] Collision sound conveys failure without being punishing
- [ ] All sound effects are audible but not overwhelming
- [ ] Volumes are balanced relative to each other

### Background Music
- [ ] Music loops seamlessly without gaps or clicks
- [ ] Music doesn't become annoying after 5+ minutes
- [ ] Music volume doesn't overpower sound effects
- [ ] Music fades out smoothly on game over (500ms)
- [ ] Music pauses correctly when game is paused
- [ ] Music resumes correctly when game is unpaused

### Technical
- [ ] All audio files load successfully
- [ ] Audio works in Chrome, Firefox, Safari, Edge
- [ ] Audio works with sound disabled (graceful fallback)
- [ ] No audio glitches or pops during playback
- [ ] Memory usage is reasonable (no audio leaks)
- [ ] Audio latency meets < 50ms requirement

## Accessibility Considerations

- Provide option to disable sound effects separately from music
- Provide option to disable all audio
- Ensure game is fully playable without audio (visual feedback only)
- Consider adding visual indicators that sync with audio cues
- Test with screen readers (audio shouldn't interfere)

## Performance Notes

- Preload all audio assets during game initialization
- Use `cloneNode()` for sound effects that may overlap
- Limit simultaneous audio playback to prevent performance issues
- Consider using Web Audio API for better performance on mobile
- Monitor memory usage (audio buffers can be large)

## Future Enhancements (Optional)

- [ ] Add subtle wind/ambient sound during gameplay
- [ ] Add menu navigation sounds (hover, click)
- [ ] Add pause/unpause sound effects
- [ ] Add high score celebration sound
- [ ] Add difficulty increase audio cue
- [ ] Dynamic music that changes with score/difficulty
- [ ] Spatial audio (panning based on ghost position)

## References

- Requirements: See `.kiro/specs/flappy-kiro/requirements.md` sections 1.3, 2.5, 2.6, 5.17, 5.18, 6.3, 6.4, 7.2, 7.4, 9.8-9.10
- Design: See `.kiro/specs/flappy-kiro/design.md` AudioSystem component
- Configuration: See `config/game-config.json` audio section
