# Find Your Own Lucky Wheel
* Interaction Method: Audio-Driven Wheel Discovery 
* Student：Ziyu Wang
* Unikey：zwan0450

---

## How to Interact

1. **Load the Page**: Open `index.html` in your browser (preferably via a local server).
2. **Intro Screens**: You’ll see floating text prompts.
   * Click anywhere to advance each step.
3. **Gameplay**: Once started, three themed wheels (Ocean, Desert, Oasis) appear.
4. **Mouse Movement**: Move your cursor—every wheel plays the same sound, but volume and stereo pan change based on your distance and position.
5. **Find the Lucky Wheel**: Only one wheel per theme is the “Lucky Wheel.”

   * As you hover closer, sound volume peaks.
6. **Reveal Result**: Click the true Lucky Wheel to display a congratulatory message.
7. **Restart**: Click again to begin with a new random theme.

---

## Individual Animation Approach

* **Driver Chosen:** **Audio** – I focused on sound cues as the primary guide.
* **Core Idea:** Use spatial audio (volume & pan) for “hot-or-cold” style discovery instead of visual-only clues.
* **Why Unique:** Enriched the Lucky Wheel background and introduced three distinct themes (Ocean, Desert, Oasis); layered in audio-driven engagement, added Perlin-noise text jitter for dynamic prompts, and designed for continuous, replayable interaction.


---

## Animated Properties & Uniqueness 

* **Volume Mapping:** Mouse proximity to the Lucky Wheel maps to volume (closer = louder).
* **Stereo Panning:** Horizontal mouse position pans left/right.
* **Thematic Soundscapes:** Each theme loads a unique ambient track (ocean waves, desert wind, oasis ambiance).

This contrasts with peers who animated rotation speed or color dynamically; here, sound is the main interactive element.

---

## References & Inspiration

* **Find the Invisible Cow** – inspired the auditory “hidden target” concept from [https://findtheinvisiblecow.com/]
* **p5.js `dist()`** – calculates the Euclidean distance between two points for collision and hover detection from [https://p5js.org/reference/p5/dist/](https://p5js.org/reference/p5/dist/)  
* **Collision Detection (CodeGuppy)** – step-by-step guide to detect overlap between two circles using p5.js `dist()` from [https://codeguppy.com/blog/how-to-implement-collision-detection-between-two-circles-using-p5.js/index.html](https://codeguppy.com/blog/how-to-implement-collision-detection-between-two-circles-using-p5.js/index.html)
* **p5.js Sound Library** – `loadSound()`, `setVolume()`, `pan()` from [https://p5js.org/reference/#/p5.SoundFile](https://p5js.org/reference/#/p5.SoundFile).

---

## Technical Explanation

1. **State Machine** (`state = 0…3`): manages intro, instructions, gameplay, and result screens.
2. **Sound Setup** (`preload` & `setup`):

   ```js
   oceanSound = loadSound('assets/ocean.wav');
   // ... similarly desertSound, oasisSound
   themeIndex = floor(random(3));
   song = [oceanSound, desertSound, oasisSound][themeIndex];
   song.loop();
   // Audio analyzers:
   analyser = new p5.Amplitude();      // tracks overall volume level
   analyser.setInput(song);
   fft = new p5.FFT(0.8, 128);        // analyzes frequency spectrum
   fft.setInput(song);
   ```
3. **Volume & Pan Mapping** (`mouseMoved`):

   ```js
   let d = dist(mouseX, mouseY, lw.x, lw.y);
   let vol = map(maxDist - d, 0, maxDist, 0.2, 1, true);
   song.setVolume(vol);
   song.pan(map(mouseX,0,width,-1,1));
   ```
4. **Audio-Driven Animation** (`draw` during gameplay):

   * **Amplitude for Rotation & Scale:**

     ```js
     let level = analyser.getLevel();
     w.rotationSpeed = w.baseRotationSpeed * map(level, 0, 1, 0.5, 2, true);
     w.targetRadius = w.baseRadius * (1 + level * 0.3);
     ```
   * **FFT to Pink Ring Alpha:**

     ```js
     let midEnergy = fft.analyze()[30];
     w.pinkRingColor.setAlpha(map(midEnergy, 0, 255, 50, 255, true));
     ```
5. **Lucky Wheel Selection** (`mouseClicked`): picks one wheel index per theme, only that index triggers the result state.

---

## Major Code Changes

* Added a four-phase state machine with Perlin-noise text jitter for intro, instructions, gameplay, and result screens.

* Integrated `p5.Amplitude()` and `p5.FFT()` to drive wheel rotation speed, size, and ring transparency directly from audio level and frequency content.

* Themed color ranges and unique ambient tracks (Ocean, Desert, Oasis) replace fully random palettes and static sound.

* Simplified user controls—audio now auto-drives visuals; mouse panning remains the only manual input.

---
