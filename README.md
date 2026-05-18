# grid direct instruments

Single-file browser instruments for a 16 x 8 monome grid. The page talks directly to the grid with Web Serial and makes sound with WebAudio. It does not use serialosc, OSC, Node.js, npm packages, or a local bridge process.

## Run locally on macOS

1. Stop serialosc if it owns the grid port:

   ```sh
   brew services stop serialosc
   ```

2. Start a local static server from this folder:

   ```sh
   python3 -m http.server 8000
   ```

3. Open Chrome or Edge to:

   ```text
   http://localhost:8000/
   ```

4. Click `Connect grid`, select the monome serial device, and click `Enable audio` if the browser has not already enabled it.

Restart serialosc later with:

```sh
brew services start serialosc
```

## Ports and studies

The launcher now starts with the sample instruments, then includes the new performance instruments, five WebAudio ports/inspired reductions, the original studies sequencer, and the existing Game of Life mode:

- `strata`: a six-layer performance grid. Rows 1-6 toggle drums, bass, notes, chords, dust, and noise. Row 7 mutes layers and picks chords. Row 8 jumps scenes, creates composed variations, holds fills on keys 9-12, and holds dub/stutter/crush/wash effects on keys 13-16.
- `lumen`: a playable wave field. Press any cell to cycle empty, spark, anchor, bloom. Sparks emit waves, anchors produce bass, and blooms make chords when waves hit them.
- `splice`: the default app. Load or record one sound, then it starts as a 16-slice straight 1x forward scan before pushing into reverse/forward rates. Row 7 toggles slices into a scanner, and row 8 controls recording, scan, normal-pattern restore, patterns, and tape speed.
- `undertow`: a sample weather system. One mic recording becomes ripples, reverse pulls, slow grains, and pitched fragments. Place nodes in rows 1-7, then use row 8 for recording, freeze, reseed, clear, and density.
- `studies`: the monome grid studies step sequencer pattern: top rows are toggles, the second-to-last row shows trigger activity, and the bottom row is a playhead/loop strip.
- `counts`: a small WebAudio instrument inspired by Meadowphysics, with independent right-to-left row counters, count ranges, clock divisions, reset routing, and rule sends.
- `drift`: a small WebAudio instrument inspired by tehn's `awake`, using two phase-shifting loops where one plays notes and the other transposes them.
- `threads`: a compact instrument inspired by markeats' `loom`, with moving vertical note threads and horizontal trigger threads that make sound at intersections.
- `pins`: an instrument inspired by `buoys`, where tides move across the grid, buoys sing when lifted, and pilings interrupt the field.
- `signals`: a small signal/cell environment inspired by `arcologies`, with emitters, note cells, reflectors, and moving signals.
- `life`: Conway's Game of Life with audio from births and population pulses.

Each app exposes its own little readme in the right panel.

The fixed `synth` panel is shared by the non-sample instruments: wave, attack, decay, lowpass, Q, drive, and level stay in the same place while the grid apps act as sequencers, note fields, or trigger surfaces. In non-sample apps, pressed grid position can also push filter and decay so the sequencer can reach into the common voice without each app needing its own scattered sound controls. The master `bpm` slider retimes the active app clock, and the `led curve` slider adjusts how strongly mid-level varibright values are compressed on the hardware grid.

Hardware LED output uses an adjustable brightness curve. App frames still use the full 0-15 varibright vocabulary internally; the `led curve` control blends between linear output and the more compressed Loom-like bright-events-over-dim-field response.

## Protocol notes

The main page and the small hello-world example share `grid-serial.js`, a tiny browser ES module for the raw monome serial protocol:

- Query grid size: `[0x05]`, expecting `[0x03, width, height]`
- Key down/up from grid: `[0x21, x, y]` / `[0x20, x, y]`
- Clear LEDs: `[0x12]`
- Varibright 8 x 8 LED frame chunks: `[0x1A, x, y, packed_nibbles...]`, packed the same way as libmonome: first cell in the high nibble, second in the low nibble.

The browser defaults to a 16 x 8 layout and switches if the grid reports a different size.

## Sample capture

`splice` and `undertow` can use either the bundled default loop, a local audio file, or the browser microphone. Loaded and recorded samples are normalized/compressed/loudened before playback.

The bundled default is `assets/default-sample.ogg`, Wikimedia Commons `Djembe accompaniment.ogg` by Michi Henning, released as CC0: <https://commons.wikimedia.org/wiki/File:Djembe_accompaniment.ogg>.

To load a sample, click `Load sample` and choose any browser-supported audio file.

To record a sample:

1. Click `Enable audio`.
2. Click `Enable mic` and allow microphone access.
3. In either sample app, press row 8 key 1 once to start recording. It is a latched record button, not press-and-hold.
4. Press row 8 key 1 again to stop and save the buffer. While recording, the browser mic button turns into a red `REC recording` light and the grid bottom row flashes.

If no mic recording exists yet, both apps use a small synthetic fallback buffer so the grid remains playable.

## References

- monome grid studies: <https://monome.org/docs/grid/studies/>
- monome grid computer docs: <https://monome.org/docs/grid/grid-computer/>
- norns softcut studies: <https://monome.org/docs/norns/softcut/>
- Meadowphysics docs: <https://monome.org/docs/meadowphysics/> and <https://monome.org/docs/ansible/meadowphysics/>
- cheat codes 2: <https://norns.community/cheat_codes_2/>
- monome serial protocol: <https://monome.org/docs/serialosc/serial.txt>
- serialosc setup: <https://monome.org/docs/serialosc/setup/>
- awake: <https://github.com/tehn/awake>
- loom: <https://github.com/markwheeler/loom>
- buoys: <https://github.com/lylepmills/buoys>
- arcologies: <https://github.com/northern-information/arcologies>
