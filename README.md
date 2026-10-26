# PluginPlaya
Lightweight Virtual Instrument Plugin Player/Test Environment for MacOS

PLUGIN//PLAYA brings a plugin host, virtual MIDI controller, stereo audio input mixer, audio-file player, OSC/MIDI control router, and performance-preset library together in one desktop application. Load an instrument and play it from your computer keyboard, connect external MIDI controllers, audition effects with live audio, or run a WAV backing track alongside a plugin during a performance—without needing to open a full DAW.

## At a glance

- **Host VST3, Audio Unit (AU), and compatible 64-bit VST2 plugins** in one standalone macOS app.
- **Embedded or floating plugin GUIs**, plus generic parameter controls when needed.
- **Play plugins with KBD Midi**, an on-screen piano keyboard, 16 configurable drum pads, or external MIDI controllers.
- **MIDI 1.0 / MIDI 2.0 input-mode switch**, with UMP hardware input and legacy plugin compatibility.
- **MIDI CC learn and routing matrix** for mapping controller messages to plugin parameters.
- **OSC control matrix** with address learning and an optional UDP listener.
- **Stereo live input** with independent channel gain, pan, and LED meters.
- **Audio-file playback** with waveform display, transport, loop, volume, and plugin/direct-output routing.
- **XML performance presets** that restore plugin state and host control assignments.
- **Ableton Link**, BPM/transport information, master metering, diagnostic exports, and optional GitHub update checks.

## Plugin hosting and GUI control

PLUGIN//PLAYA can scan installed plugin folders, open a plugin manually, or load a supported plugin bundle by drag and drop. It supports **VST3**, **Audio Unit**, and **VST2** when VST2 hosting is included in the distributed binary. VST2 support is for compatible **64-bit** plugins; this app does not emulate 32-bit VSTs.

Plugin management features include:

- Installed-plugin selector, folder scanning, and plugin load/unload.
- **REFRESH** to reload a plugin instance, with **Keep patch on refresh** to attempt to restore its previous state.
- Native plugin GUI embedding, optional floating editors, and a host-provided generic parameter editor.
- GUI Options for compatibility fallbacks and additional troubleshooting options.
- Plugin information showing identity, format, audio/MIDI capabilities, parameters, channel counts, and diagnostic details.
- Host-window resizing and cross-monitor geometry handling designed to preserve window dimensions when moving between displays.

**Compatibility note:** Plugin GUIs and audio engines run inside the host process. A defective third-party plugin can therefore crash PLUGIN//PLAYA. Known problematic editors may open with a host-provided compatibility interface instead of their original skin. For example, Cineol-X 224 has a dedicated parameter-based fallback because its native editor has caused crashes on the development system. Some plugin interfaces and sample-library plugins may also depend on their own macOS/version compatibility.

## Audio playback and backing tracks

The **Audio Playback** page lets you use an audio file to test effects or accompany a live instrument performance.

- Open or drag in an audio file; supported extensions include **WAV, AIFF, FLAC, MP3, and OGG**, subject to the available decoders.
- View a waveform, navigate/scrub through the file, play or stop, and enable looping.
- Adjust the dedicated horizontal **WAV LEVEL** slider independently of master gain.
- Choose the playback destination with **TO VST / DIRECT OUT**:
  - **TO VST:** Sends the file into the hosted plugin for processing—ideal for auditioning compressors, reverbs, distortion, and other effects.
  - **DIRECT OUT:** Keeps the file out of the plugin's processing path and mixes it with the plugin's output afterward—ideal for backing tracks while playing a MIDI instrument.

**DIRECT OUT uses the same stereo hardware outputs as the plugin**; it is not a separate physical output bus. The combined mix still passes through the master output gain. Audio-file playback is associated with the Audio Playback page. XML presets store the WAV gain and routing choice, but **do not embed or automatically reload the audio file**.

## Two-channel live audio input

The **Live Input** page supports two physical input channels with independent processing before they enter the hosted plugin:

| Control | Channel 1 | Channel 2 |
| --- | --- | --- |
| Input level | Independent gain | Independent gain |
| Peak meter | 22-segment LED | 22-segment LED |
| Stereo pan | Left–right | Left–right |
| Default pan | Hard left | Hard right |

Use **ARM LIVE IN** to pass the selected inputs into the plugin. The meters can still display incoming signal while monitoring is disarmed. **AUDIO I/O** provides device and input/output configuration, including buffer settings. This makes it useful for quickly processing a microphone, instrument, stereo source, or external hardware without setting up a DAW session.

## MIDI keyboard, pads, and external controllers

Use the integrated performance controls to trigger instrument plugins:

- **KBD Midi** maps the Mac keyboard to musical notes and starts **enabled by default**.
- Piano-style computer key layout; **Z/X** changes octave and **C/V** changes velocity.
- App-level key capture can keep the keyboard playable while many native plugin GUIs have focus. Unmapped printable keys are suppressed to prevent the macOS alert beep while KBD Midi is on, except when typing in text fields or using ordinary shortcuts.
- Resizable on-screen MIDI piano keyboard or **16 assignable MIDI drum pads**.
- MIDI channel selection, a vertical **MOD** (CC1) slider, and an auto-centering vertical **BEND** slider.
- Physical MIDI device selection, including enabling multiple controllers through the MIDI CC panel.

### MIDI CC mapping

Open **MIDI CC** for a dynamic routing matrix that maps incoming controller messages to the currently loaded plugin's parameters. Routes can specify a device, MIDI channel, CC number, and target parameter. Add or remove mappings, learn from a physical controller, or clear the matrix in one action.

For plugins using the host-provided generic or compatibility editor, **right-click a parameter → MIDI CC Learn**, then move a control on an enabled MIDI device. Mappings are retained in XML performance presets.

### MIDI 1.0 and MIDI 2.0

The **MIDI 1.0 / MIDI 2.0** button beside MIDI CC switches the hardware input mode:

- **MIDI 1.0:** Traditional MIDI input and compatibility with legacy plugins.
- **MIDI 2.0:** JUCE UMP endpoint input, with higher-resolution controller values available for plugin-parameter mapping and translation of supported channel messages for existing plugins.

MIDI 2.0 mode supports high-resolution CC input, but **does not mean every hosted VST2/VST3/AU plugin receives raw MIDI 2.0 UMP**. Plugin note/event delivery is currently translated to the existing MIDI 1.0-compatible host path. Full MIDI-CI, per-note MIDI 2.0 expression delivery, and other advanced MIDI 2.0 features are not yet implemented.

## OSC (Open Sound Control)

The **OSC** button opens a separate, scrollable OSC routing matrix for control from OSC-capable hardware, software, tablets, or other network devices.

- Optional UDP receiver, **off by default**, with configurable port (**9000** by default).
- OSC address learning: transmit a message and associate its address with a plugin parameter.
- Add and remove mappings, enable/disable routes, or clear all routes.
- Configure incoming value minimum/maximum and invert the mapping.
- Numeric OSC float and integer values; nested OSC bundles are supported.
- OSC routing remains independent of MIDI 1.0/2.0 mode and is saved with performance presets.

OSC uses UDP and has no built-in authentication in this implementation. Enable listening only on networks you trust. Loading a preset **does not automatically activate** the OSC listener.

## XML performance presets

PLUGIN//PLAYA includes a shared, app-level performance-preset browser with a dropdown, previous/next arrows, and **SAVE**. Unlike per-plugin preset menus, this library can contain setups for **different installed plugins in the same list**. Selecting a preset for another plugin attempts to load that plugin and restore its saved state.

Each preset can include:

- Plugin identity and its saved state data, with parameter-value fallback.
- Host BPM and master output gain.
- Live input levels and stereo pan settings.
- WAV playback volume and **TO VST / DIRECT OUT** choice.
- MIDI CC parameter mappings.
- OSC mappings and receiver port.

All Plugin Playa XML presets are kept in one location:

```text
/Library/Audio/Presets/PluginPlaya/
```

Because this is a **system-level macOS folder**, write permission may need to be granted before presets can be saved. Presets reference installed plugins; they **do not bundle the plugin itself or the backing-track audio file**. Preset switching that loads another plugin is not guaranteed to be seamless or gapless.

## Tempo synchronization and Ableton Link

- Adjustable host tempo from **20–300 BPM**.
- Transport information supplied to compatible hosted plugins, including BPM, 4/4 time signature, play state, and timeline position.
- **Ableton Link** to synchronize tempo over a local network, with a peer counter and optional Link start/stop synchronization when supported by the binary.

## Master output and interface

- Master output level slider controlling the final mix.
- LED master peak meter and continuously updated **dBFS** readout at the top of the window.
- Five selectable accent themes—**cyan, red, gold, violet, and blue**—cycled by right-clicking the main title.
- Context-sensitive hover help on host-owned controls.
- Resizable layout, floating/plugin-embedded GUI choices, and monitor-transition size protection.
- Compact live status information for loaded plugins, routing, and device activity.

The interface intentionally keeps the plugin editor at the center, with audio, MIDI, and performance controls close at hand.

## Diagnostics and updates

PLUGIN//PLAYA includes diagnostic tools intended to help troubleshoot unusual plugin behavior:

- **PLUGIN INFO**: loaded plugin metadata, parameters, GUI state, and host compatibility details.
- **Application Diagnostics**: audio/MIDI/OSC state, connected devices, counters, plugin host status, and recent events.
- View, copy, and **export Markdown (`.md`) diagnostic reports**.
- Session log files for investigating plugin loading and GUI problems.

The **About** window links to the official GitHub repository. **Check for updates on startup** is enabled by default and can be disabled; it checks the latest published GitHub release in the background. When a newer version is available, the app offers to open the Releases page. When no newer published release is available, it displays **“You are up to date!”**; network failures are reported separately. No automatic installation occurs.

## Getting started (binary release)

1. Download the macOS app archive from [GitHub Releases](https://github.com/TEAMBANZAI/PluginPlaya/releases) when a release is published.
2. Extract the archive and move **Plugin Playa.app** to **Applications** (or another preferred location).
3. Open Plugin Playa, select an installed compatible plugin, or drag a plugin bundle into the window.
4. Choose your audio device in **AUDIO I/O**, then play with **KBD Midi**, a connected controller, live audio, or an audio file.
5. Save reusable setups with the **performance preset** controls.

If macOS asks you to confirm opening an app downloaded from the internet, use Finder's normal **Open** workflow and review the macOS security prompt. Consult each release's notes for its supported macOS versions and CPU architectures.

## Good uses for Plugin Playa

- **Quick plugin auditioning:** Test a VST or AU without launching a full DAW.
- **Live MIDI performance:** Play an instrument with computer keys, pads, MIDI controllers, or OSC.
- **Backing-track sets:** Route a WAV directly to the stereo output while a hosted instrument continues to receive MIDI.
- **Effect testing:** Send an audio file or live stereo input through a loaded effect.
- **Performance recall:** Step through XML setups that include plugins, parameter states, controllers, and routing.
- **Troubleshooting:** Collect application and plugin diagnostics when a plugin behaves unexpectedly.

## Compatibility and current limitations

- **Platform:** Standalone macOS app. Development has focused on Intel/x86_64 and macOS Monterey 12.6.8; exact supported systems depend on the published binary and the plugins being hosted.
- **Plugin formats:** VST3 and AU; VST2 when included in the build. Plugins must match the architecture supported by the host. **32-bit VST emulation is not provided.**
- **Native GUI stability:** Some third-party plugin editors are incompatible or unstable; the generic/compatibility editor is available as a fallback. Plugins are currently hosted in-process, not sandboxed in separate crash-isolated processes.
- **MIDI 2.0:** UMP hardware input and high-resolution controller mapping are supported; full native MIDI 2.0 plugin-event delivery and complete MIDI-CI support are not.
- **OSC:** Incoming UDP parameter control only; it should not be exposed to untrusted networks.
- **Presets:** XML files depend on installed plugin binaries and access to the system preset folder. Referenced audio files are not bundled into presets.
- **Output routing:** DIRECT OUT bypasses the plugin but shares the main stereo hardware outputs; there is no separate backing-track hardware bus or automatic mix limiter.
