# GitHub 仓库设置（公开版）

## 仓库名（建议）

```
bandori-ournotes-autoplay
```

候选（想更"技术"一点的话）：`ournotes-human-anchor-autoplay`、`ournotes-autoplay-showcase`

## 描述（About 那一栏，英文优先）

```
Human-in-the-loop autoplay for BanG Dream! Our Notes: you tap the first note, it plays the rest. No root — virtual HID touchscreen over scrcpy. Chart-driven, with hold-bar following and flick notes. (no source code)
```

中文（可放在 README 第一行或 Release 说明里）：

```
《BanG Dream! Our Notes》人机协同自动演奏：你按第 1 个音符，脚本从第 2 个起打完。免 root，走虚拟 HID 触摸屏；谱面驱动，支持长条横移与轻扫。
```

## 网站（Website 那一栏）

填你的 B 站主页，例如：

```
https://space.bilibili.com/<你的UID>
```

## Topics（标签，直接全填，最多 20 个）

```
bandori
bang-dream
our-notes
rhythm-game
autoplay
android
no-root
uhid
hid
scrcpy
adb
touch-injection
getevent
reverse-engineering
automation
opencv
python
human-in-the-loop
音游
自动演奏
```

（GitHub topics 只允许小写字母、数字和连字符，中文那两个可能被拒，能过就留着。）

## Release（发一个，方便别人搜到）

* Tag：`v0.1.0`
* 标题：`First public write-up — human-anchor timing + non-root UHID injection`
* 说明：把 README 里的"它不一样在哪"五条贴进去，末尾加一句
  `Source code is not included; this release documents the result.`

## 建议的仓库结构

```
README.md              英文（GitHub 默认展示这个）
README.zh-CN.md        中文
docs/how-it-works.md + how-it-works.zh-CN.md
docs/faq.md + faq.zh-CN.md
docs/disclaimer.md + disclaimer.zh-CN.md
media/*.png
LICENSE
```

## 容易被搜到的几个词（README 和 Release 里都出现了）

`BanG Dream Our Notes autoplay`、`バンドリ 自動演奏`、`音游 自动演奏 免root`、
`UHID 虚拟触摸屏`、`scrcpy UHID touch`、`getevent 内核时间戳`、
`rhythm game autoplay without root`
