First public write-up of a working autoplay for **BanG Dream! Our Notes** on a stock,
unrooted Android phone.

It is deliberately human-in-the-loop: **you tap the first note**, the tool takes over from
the second and plays the rest from the game's own chart data.

This release documents the result and the ideas — the implementation is not published.

## What is in it

* Why guessing the lead-in fails, with the numbers: the measured "seconds from START to
  chart time 0" swung across **2.12 / 9.53 / 10.05 / 11.52 s** on the same device and day.
* The replacement: the **kernel timestamp of the player's own first tap** as the clock, so
  a slow read path delays the start but cannot shift the notes.
* **Non-root touch injection** through a virtual HID touchscreen (scrcpy-server UHID); the
  phone sees a genuine `TOUCH | TOUCH_MT` digitizer.
* Chart-driven play — the screen is used for exactly one coarse gate.
* Hold bars **followed along their path**: 15311 of 23073 hold bars move (median
  1258 px/s, 90th percentile 4346 px/s); stepping once per chart node meant a median
  197 px jump, now 72 px.
* Hold bars that **end in a flick**: 2512 of them (up 1536 / left 492 / right 484), plus
  200 that start with one.
* How it compares to the closest existing projects, and the limits we chose on purpose.

## 中文提要

《BanG Dream! Our Notes》人机协同自动演奏的首次公开说明：**你按第 1 个音符**，
脚本从第 2 个起、按游戏谱面数据把整首打完。免 root，走虚拟 HID 触摸屏。

本版本公开的是**结果与思路**，不包含实现代码。中英双语见 `README.md` 与 `README.zh-CN.md`。

作者：B 站 **作业快没了**
