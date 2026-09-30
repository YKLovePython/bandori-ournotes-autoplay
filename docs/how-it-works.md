# How it works

This is the long version of the four ideas behind the project. No source code here —
just the mechanism, the measurements, and the dead ends.

## 1. Timing: the player's finger is the clock

### Why guessing does not work

A rhythm game needs one number before anything else: **what real time does chart time
`t = 0` correspond to?** For this game that number is not available from inside the chart
file, and the loading time is not constant.

Measured on the same device and the same song, with the same code, on one day:

| Attempt | "seconds between tapping START and chart time 0" |
| --- | --- |
| 1 | 2.12 s |
| 2 | 9.53 s |
| 3 | 10.05 s |
| 4 | 11.52 s |

The spread is nine seconds. Any fixed constant is therefore wrong most of the time,
and a wrong constant does not produce "slightly worse" play — it produces a song full
of misses.

The screen can be used to detect *when the live scene appears*, but the video path
carries 25–40 ms of latency and occasionally mis-fires, so using it as the clock puts a
constant, unknown offset on every note. We tried that too, including a closed loop that
kept re-estimating the offset from the notes visible on screen. It drifted by hundreds of
milliseconds the moment the detector had a false positive.

### What replaced it

The phone's own touchscreen driver reports every touch with a **kernel timestamp**.
When the player taps the first note and the game judges it, that timestamp *is* chart
time `t1`, as seen by the game.

So the script:

1. arms a reader on the real digitizer (`getevent`) before the live starts;
2. takes the kernel timestamp `F` of the first touch inside the playfield;
3. schedules the rest of the song at real time `F + (t_n − t_1)`.

Two consequences matter:

* the read path can be slow — it only delays *when we start*, because the timestamp is
  taken in kernel time, not arrival time;
* the constant part of the injection latency cancels out, because the player's finger and
  the injected touches travel through the same input pipeline.

What remains is the player's own timing error on that one note. On fast sliding bars that
residual is still visible (50 ms × 4000 px/s ≈ 200 px), which is what pushed the
implementation towards the continuous following described below.

### Measuring the path

The script still needs to know `L + λ` (read latency + inject latency) to convert the
timestamp into a wall-clock instant. It measures it: during the loading screen it taps the
virtual touchscreen a few times and watches how long the event takes to show up on the
device side. Measured value: **≈ 1 ms**, consistent with a persistent adb shell measuring
0.7–1.5 ms round-trip on this device.

## 2. Injection: a virtual HID touchscreen, no root

On a modern stock Android device:

| Method | Result |
| --- | --- |
| writing `/dev/input/eventN` directly | denied by SELinux (`u:object_r:input_device:s0`) |
| `adb shell input tap` / `motionevent` | works, but single finger and 60–80 ms per call |
| **scrcpy-server UHID** | works: the system registers a genuine `TOUCH | TOUCH_MT` digitizer |

UHID lets a normal (non-root) process ask the kernel to create a HID device from a report
descriptor it supplies. Give it a multi-touch digitizer descriptor and the system treats it
exactly like panel hardware — the game has no way to tell.

Two details that cost real time to find:

* the descriptor's logical maximum must be the **natural (portrait) panel size**, otherwise
  the coordinates get scaled;
* the landscape coordinates the game uses map to HID coordinates as
  `(panel_width − y, x)`.

## 3. Notes come from chart data, not from the screen

The screen is used for exactly one coarse decision: *is this the band-confirm page?*
(template match on the LIVE START button, which also yields a more reliable button position
than a hard-coded coordinate).

Everything else — which note, which lane, which time, which gesture — comes from chart data.
Compared to detecting notes in a video stream this is:

* exact (no detection error),
* cheap (no decoding),
* and stable (no drift).

## 4. Gestures: hold bars move, and their tails flick

Parsing all 340 charts gives the following picture:

| Fact | Count |
| --- | --- |
| notes total | 95190 |
| hold bars | 23073 |
| hold bars whose head and tail positions differ | 11986 |
| hold bars that move somewhere in the middle (including those that return) | 15311 |
| hold bars whose **tail** is a flick | 2512 (up 1536 / left 492 / right 484) |
| hold bars whose **head** is a flick | 200 |
| standalone flick notes | 6698 (up / left / right only — no "down") |

Hold bars slide at a measured **median 1258 px/s, 90th percentile 4346 px/s**. Holding the
finger still, or moving it once per chart node (≈157 ms ⇒ 197 px per step), leaves the finger
off the bar for a good fraction of the time — which shows up as *intermittent* misses that
look random. Resampling the bar's node path **by distance** (one move per ~50 px of travel,
at most 40 Hz per finger) brings the median step to 72 px.

A bar whose tail is a flick needs three things in order: be *on* the bar's tail position,
swipe, then release. Missing any of the three loses the note.

## 5. What we tried and abandoned

Hoping the write-up saves someone the same weeks:

* **Visual closed-loop timing** (re-estimating the offset from notes on screen): false
  positives pushed the schedule by ±400 ms. The observer is still available in the code
  but is disabled.
* **Audio clock** (reading the game's own playback position): the only counter available
  updates every 5 s and lands after the first note. Also, the audio stream's log line
  turned out to belong to the home-screen BGM, not the song.
* **`sendevent` / `minitouch`**: blocked or unusable on this device.
* **Guessing the lead-in from the loading screen**: 9 seconds of variance, see above.

## 6. Verification

Everything above is checked without a phone where possible:

* all **340 charts** are parsed and scheduled in a regression pass (0 failures);
* schedules are checked for finger reuse while a finger is still down (0 cases) and for
  same-instant ordering (presses before releases);
* gesture shapes are dumped and eyeballed (a hold that ends in a right flick becomes
  *pre-position → +60 px → +120 px → release*);
* on the device, the log records the anchor, the handover point, the gesture mix and any
  late actions.
