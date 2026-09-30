# FAQ

**Q: Why is the source code not public?**
It is kept in a private repository on purpose. This repository documents the result and
the ideas; the implementation is not distributed.

**Q: Does it work on my phone?**
It was developed and measured on a Xiaomi 2510DRK44C (Android 16, unrooted). In principle
any device that (a) accepts `scrcpy` UHID and (b) lets `getevent` read its touchscreen
works; the panel size, judge-line geometry and the LIVE START button are calibrated per
device.

**Q: Why not just use `adb shell input tap`?**
It is single-finger and takes 60–80 ms per call because it spawns a process each time.
Chords and holds are impossible that way.

**Q: Why not use a computer-vision loop, like other rhythm-game bots?**
Vision tells you *where a note is*, which you already know exactly from the chart. It also
adds 25–40 ms of latency and occasional false positives. Here the screen is only used to
recognise the confirm page.

**Q: Why not read the game's memory, or use root?**
The phone in question is unrooted with a locked bootloader. That was the constraint that
made the project interesting.

**Q: Isn't this cheating / will it get me banned?**
Yes, automating a live-service game is against its terms of service and can get an account
punished. This is published as a research project, not as a tool to use. See the
disclaimer.

**Q: Why does the first note have to be played by hand?**
Because that tap *is* the timing reference. The chart does not contain a lead-in, and the
loading time varies by seconds. It is not a missing feature — it is the core idea.

**Q: What about the misses that remain?**
They cluster on the fastest sliding bars (the residual comes from the player's own first
tap being off by tens of milliseconds, which matters most when the bar moves 4000 px/s),
and occasionally on bars whose tail is a flick. Both are described in
[how-it-works](how-it-works.md) with the numbers behind them.
