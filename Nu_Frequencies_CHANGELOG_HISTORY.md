# Nu Frequencies - Changelog & Intelligent Logic History

## Date: February 5, 2026

### Today's Main Modifications

#### 1. Default State & Visual Consistency
- **Default Selection**: Forced the "Relaxation" mode to be active upon launch.
- **Visual Fix**: Implemented **Inline Styles** on the Relaxation card in the HTML to bypass any CSS/JS timing issues, ensuring the green border is visible immediately.
- **Info Box Alignment**: Updated the `.mode-info-box` border color to match the primary selection (Green for relaxation) to maintain visual continuity.
- **Mono Selection**: Set "Naturel (432 Hz)" as the default selected option in the Mono Modal.

#### 2. UI/UX Refinements
- **Text Updates**: Shortened descriptions for Mono frequencies. Replaced "pures" with "naturelles" in the French description and updated the English equivalent.
- **Button Styling**: Ensured the "Commencer la session" button in the Mono modal precisely matches the main interface button.
- **Font Size**: Reduced `.mode-info-box` font size for better information density.
- **Screen Management**: Added and then **Removed** a screen toggle. The app now **Always** requests a Wake Lock (keeps screen on) during a session to prevent interruptions, as iOS often kills audio when the screen goes black.

#### 3. Audio Engine Evolution (Internal Logic)
- **Mono vs Binaural Detection**: Implemented logic `isMonoMode = (config.freqL === config.freqR)`.
- **Intelligent Panning**:
  - **Binaural**: Uses 100% stereo separation (-1 and 1) for maximum hemispheric synchronization.
  - **Mono**: Bypasses the stereo panner (centered) and uses a single oscillator to prevent phase cancellation on mono phone speakers (especially iPhone).
- **iOS Audio Unlock**: Implemented a multi-stage unlock:
  - First, play an actual HTML5 Audio element (Base64 MP3/WAV) on user gesture.
  - Second, `audioCtx.resume()` to activate the Web Audio Context.
- **Volume Curve**: Implemented a linear ramp (fade-in/fade-out) to avoid "clicks" when starting or stopping frequencies.

---

### Intelligent Connections & Critical Dependencies (Knowledge Retention)

#### A. The "User Gesture" Chain
**Why it matters**: Browsers (especially iOS Safari) block all audio until a user interacting with the page "unlocks" it.
**Connection**: The `toggleSession()` function acts as the gatekeeper. It must be called by an `onclick` event. Any code trying to start audio outside this chain will fail silenty.

#### B. WakeLock & Audio Survival
**Knowledge**: iOS Safari puts the browser to sleep when the screen turns off. 
**Connection**: `requestWakeLock` is intrinsically tied to `startAudio`. If the screen turns off, the `AudioContext` is suspended. Current architecture forces the screen to stay on to guarantee audio playback.

#### C. Mono Compatibility
**Connection**: `MODES` object contains frequencies for L and R. If `L == R`, the code intelligently switches from a dual-panner setup to a single-oscillator centered setup. This is critical for users listening without headphones on devices with a single speaker.

#### D. Translation & UI Sync
**Connection**: `updateInterfaceLanguage()` doesn't just change text; it triggers `selectMode(currentMode)` to refresh the info box content and posture hints. Never call one without ensuring the other is synced.

---

### Unresolved Issues (For Future Sessions)
- **iOS Speaker Routing**: Some iOS units still route audio to the earpiece instead of the speaker despite the HTML5 unlock hack. Further investigation into audio session categories or using a silent long-running loop might be needed.
