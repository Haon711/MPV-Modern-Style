# MPV Player - Modern Style


A clean, high-quality **mpv configuration for Windows**, focused on:

* SDR-only playback
* high-quality video rendering
* clean audio output
* modern OSC interface
* timeline thumbnails with `thumbfast`
* automatic folder playlist
* subtitle synchronization with `autosubsync-mpv`
* useful internal mpv menus through `input.conf`

This setup is designed to stay faithful to the source: no forced HDR tone mapping, no forced full-range conversion, no forced audio upsampling, no always-on debanding, no always-on interpolation, and no unnecessary audio filters.

---

## Features

### Video

* `gpu-next` video output
* D3D11 backend for Windows
* SDR-only configuration
* No HDR / tone mapping
* No forced full-range output
* High-quality scalers:

  * `ewa_lanczossharp` for luma upscale
  * `mitchell` for downscale
  * `ewa_lanczossharp` for chroma
* Deband disabled by default
* Interpolation disabled by default
* Deinterlace disabled by default
* Optional manual profiles for:

  * hardware decoding
  * smoother video sync
  * interpolation
  * deband
  * strong deband
  * deinterlace
  * low GPU mode
  * Vulkan test mode

### Audio

* WASAPI output on Windows
* Exclusive audio mode
* No forced 96 kHz resampling
* No forced 32-bit conversion
* No loudness normalization
* No dynamic compression
* Optional HDMI surround profile
* Optional Dolby/DTS bitstream passthrough profile

### Interface

* `mpv-osc-modern` as the OSC replacement
* `thumbfast` support for timeline preview thumbnails
* Internal mpv menus mapped to convenient shortcuts
* Automatic playlist creation from the current folder

### Subtitles

* External subtitle auto-loading
* ASS subtitle styles preserved
* Embedded fonts enabled
* Optional subtitle synchronization through `autosubsync-mpv`
* Safe subtitle retiming: original subtitles are not overwritten

---

## Requirements

### Required

* Windows 10 / Windows 11
* mpv
* `mpv-osc-modern` with thumbfast support
* `thumbfast`
* `Material-Design-Iconic-Font.ttf`

### Required for subtitle synchronization

* FFmpeg
* Python
* `ffsubsync`

Install `ffsubsync` with:

```bat
py -m pip install -U ffsubsync
```

Check that FFmpeg and ffsubsync work:

```bat
ffmpeg -version
ffsubsync --version
```

---

## Folder Structure

For a normal Windows installation, use:

```text
%APPDATA%\mpv\
```

Final structure:

```text
%APPDATA%\mpv\
│
├─ mpv.conf
├─ input.conf
│
├─ scripts\
│  ├─ modern.lua
│  ├─ thumbfast.lua
│  └─ autosubsync\
│     ├─ main.lua
│     ├─ autosubsync.lua
│     ├─ helpers.lua
│     ├─ menu.lua
│     └─ subtitle.lua
│
├─ fonts\
│  └─ Material-Design-Iconic-Font.ttf
│
└─ script-opts\
   ├─ osc.conf
   ├─ thumbfast.conf
   └─ autosubsync.conf
```

For portable mpv, use:

```text
portable_config\
```

Example:

```text
mpv\
│
├─ mpv.exe
│
└─ portable_config\
   ├─ mpv.conf
   ├─ input.conf
   │
   ├─ scripts\
   ├─ fonts\
   └─ script-opts\
```

---

## Main Configuration

### `mpv.conf`

This is the main configuration file.

Location:

```text
%APPDATA%\mpv\mpv.conf
```

Portable location:

```text
portable_config\mpv.conf
```

Core settings:

```conf
profile=high-quality

vo=gpu-next
gpu-api=d3d11
gpu-context=auto

priority=high
fullscreen=yes
force-seekable=yes
keep-open=always
save-position-on-quit=yes
watch-later-dir="~~/cache/watch_later"

autocreate-playlist=same

hwdec=no

target-prim=auto
target-trc=auto
video-output-levels=auto

icc-profile-auto=yes

dither-depth=auto
dither=fruit
temporal-dither=no

correct-downscaling=yes
linear-downscaling=yes
sigmoid-upscaling=yes
scaler-resizes-only=yes

scale=ewa_lanczossharp
dscale=mitchell
cscale=ewa_lanczossharp

scale-antiring=0.6
dscale-antiring=0.6
cscale-antiring=0.6

deband=no
deband-iterations=2
deband-threshold=48
deband-range=16
deband-grain=8

video-sync=audio
interpolation=no
deinterlace=no

ao=wasapi
audio-exclusive=yes
wasapi-exclusive-buffer=default

volume=100
volume-max=100

audio-device=auto
audio-file-auto=fuzzy
audio-pitch-correction=yes
audio-channels=auto-safe

alang=ja,jp,jpn,it,ita,en,eng
slang=it,ita,en,eng

sub-auto=fuzzy
sub-file-paths=sub;subs;subtitles
demuxer-mkv-subtitle-preroll=yes
embeddedfonts=yes

sub-ass-override=no
sub-fix-timing=no
blend-subtitles=no

sub-font="Segoe UI Semibold"
sub-font-size=46
sub-color="#FFFFFFFF"
sub-border-color="#FF000000"
sub-border-size=2.4
sub-shadow-color="#80000000"
sub-shadow-offset=0.7
sub-margin-y=50

osc=no

osd-bar=no
border=no
cursor-autohide-fs-only=yes
cursor-autohide=500

osd-level=1
osd-duration=1200
osd-font="Segoe UI"
osd-font-size=22
osd-color="#FFFFFFFF"
osd-border-color="#FF000000"
osd-border-size=0.8

screenshot-format=png
screenshot-high-bit-depth=yes
screenshot-png-compression=1
screenshot-directory="~/Pictures/mpv-screenshots"
screenshot-template="%f-%wH.%wM.%wS.%wT-#%#00n"

cache=yes
demuxer-max-bytes=512MiB
demuxer-max-back-bytes=128MiB
demuxer-readahead-secs=60
cache-pause=no
```

---

## Manual Profiles

Add these profiles at the end of `mpv.conf`.

```conf
[Idle]
profile-cond=p["idle-active"]
profile-restore=copy-equal
title=' '
keepaspect=no
background=color
background-color="#000000"


[hwdec-safe]
profile-desc=Hardware decoding sicuro per video pesanti o drop frame
hwdec=auto-safe


[smooth-video]
profile-desc=Frame pacing più fluido, meno purista per audio
video-sync=display-resample
video-sync-max-video-change=1
interpolation=no


[interpolation]
profile-desc=Interpolazione: movimento più fluido, meno cinematografico
video-sync=display-resample
video-sync-max-video-change=1
interpolation=yes
tscale=oversample
tscale-antiring=0.6


[deband]
profile-desc=Deband leggero per video con banding visibile
deband=yes
deband-iterations=3
deband-threshold=48
deband-range=16
deband-grain=8


[strong-deband]
profile-desc=Deband forte per streaming/WEB-DL brutti
deband=yes
deband-iterations=4
deband-threshold=64
deband-range=20
deband-grain=12


[deinterlace]
profile-desc=Deinterlace manuale per contenuti realmente interlacciati
deinterlace=yes


[deinterlace-auto]
profile-desc=Deinterlace automatico, da usare solo se serve
deinterlace=auto


[hdmi-surround]
profile-desc=PCM multicanale via HDMI verso AVR/soundbar
audio-channels=7.1,5.1,stereo


[bitstream]
profile-desc=Passthrough Dolby/DTS via HDMI verso AVR/soundbar compatibile
audio-spdif=ac3,eac3,dts,dts-hd,truehd


[vulkan]
profile-desc=Prova Vulkan se D3D11 dà problemi o vuoi testare prestazioni
gpu-api=vulkan
gpu-context=auto


[low-gpu]
profile-desc=Più leggero per GPU integrata o video molto pesanti
hwdec=auto-safe
scale=ewa_lanczos
dscale=mitchell
cscale=mitchell
scale-antiring=0.6
dscale-antiring=0.6
cscale-antiring=0.6


[no-icc]
profile-desc=Disattiva ICC se i colori sembrano strani
icc-profile-auto=no


[temporal-dither]
profile-desc=Temporal dithering opzionale
temporal-dither=yes
```

---

## Keybindings

### `input.conf`

Location:

```text
%APPDATA%\mpv\input.conf
```

Portable location:

```text
portable_config\input.conf
```

```conf
# ============================================================
# MPV INPUT.CONF
# mpv-osc-modern with.thumbfast + thumbfast + autosubsync
# ============================================================


# ============================================================
# INTERNAL MPV MENUS
# ============================================================

Alt+p script-binding select/select-playlist
Alt+a script-binding select/select-aid
Alt+s script-binding select/select-sid
Alt+d script-binding select/select-audio-device
Alt+c script-binding select/select-chapter
Alt+b script-binding select/select-binding
Alt+r script-binding select/show-properties


# ============================================================
# AUTOSUBSYNC
# ============================================================

# autosubsync-mpv already registers N by default.
# Do not use N for other commands.
# n script-binding autosubsync-menu


# ============================================================
# PROFILES
# ============================================================

Alt+h apply-profile hwdec-safe
Alt+g apply-profile low-gpu
Alt+v apply-profile smooth-video
Alt+i apply-profile interpolation
Alt+e apply-profile deband
Alt+Shift+e apply-profile strong-deband
Alt+x apply-profile deinterlace
Alt+Shift+x apply-profile deinterlace-auto
Alt+m apply-profile hdmi-surround
Alt+Shift+m apply-profile bitstream
Alt+Shift+i apply-profile no-icc
Alt+t apply-profile temporal-dither

Alt+Shift+r set deband no ; set interpolation no ; set video-sync audio ; set deinterlace no ; set hwdec no ; set temporal-dither no ; set icc-profile-auto yes ; show-text "Base profile restored"


# ============================================================
# VIDEO
# ============================================================

i script-binding stats/display-stats-toggle

s screenshot
S screenshot subtitles
Ctrl+s screenshot window

. frame-step
, frame-back-step

a cycle-values video-aspect-override "no" "16:9" "4:3" "2.35:1" "1.85:1"

Alt+0 set video-zoom 0 ; set video-pan-x 0 ; set video-pan-y 0 ; set video-aspect-override no


# ============================================================
# SUBTITLES
# ============================================================

v cycle sub-visibility

j cycle sub
J cycle sub down

z add sub-delay -0.1
x add sub-delay +0.1
Z add sub-delay -1
X add sub-delay +1

Ctrl+UP add sub-pos -1
Ctrl+DOWN add sub-pos +1

Ctrl++ add sub-scale +0.05
Ctrl+- add sub-scale -0.05
Ctrl+0 set sub-scale 1


# ============================================================
# AUDIO
# ============================================================

k cycle audio
K cycle audio down

Ctrl+LEFT add audio-delay -0.1
Ctrl+RIGHT add audio-delay +0.1

m cycle mute

UP add volume 5
DOWN add volume -5


# ============================================================
# PLAYBACK / SEEK
# ============================================================

SPACE cycle pause

LEFT seek -5 exact
RIGHT seek 5 exact

Shift+LEFT seek -30 exact
Shift+RIGHT seek 30 exact

Ctrl+LEFT seek -60 exact
Ctrl+RIGHT seek 60 exact

HOME seek 0 absolute-percent
END seek 100 absolute-percent

[ multiply speed 0.9091
] multiply speed 1.1
BACKSPACE set speed 1.0


# ============================================================
# PLAYLIST
# ============================================================

PGUP playlist-prev
PGDWN playlist-next

Alt+Shift+s cycle shuffle

l cycle-values loop-file "inf" "no"
L cycle-values loop-playlist "inf" "no"


# ============================================================
# WINDOW
# ============================================================

f cycle fullscreen
T cycle ontop

q quit-watch-later
Q quit
```

---

## mpv-osc-modern

This setup uses `mpv-osc-modern` as a replacement for mpv's built-in OSC.

Important:

```conf
osc=no
```

must be set in `mpv.conf`.

The built-in OSC must be disabled because `mpv-osc-modern` replaces it.

### Font

Place:

```text
Material-Design-Iconic-Font.ttf
```

in:

```text
%APPDATA%\mpv\fonts\
```

Do not remove this font. It is required for the modern OSC icons.

---

## `osc.conf`

Location:

```text
%APPDATA%\mpv\script-opts\osc.conf
```

```conf
showwindowed=yes
showfullscreen=yes

scalewindowed=1
scalefullscreen=1
scaleforcedwindow=2

vidscale=no

hidetimeout=1000
fadeduration=500
minmousemove=3

iamaprogrammer=no

font='mpv-osd-symbols'

seekrange=yes
seekrangealpha=128
seekbarkeyframes=yes

title='${media-title}'
showtitle=yes
timetotal=yes
timems=no

visibility=auto
windowcontrols=auto
volumecontrol=yes
processvolume=yes

language=eng
```

---

## Thumbfast

`thumbfast` provides high-performance timeline preview thumbnails.

Important: `thumbfast.lua` does not display thumbnails by itself. It must be used with a compatible UI.

This setup requires:

```text
mpv-osc-modern with.thumbfast
```

Place:

```text
thumbfast.lua
```

in:

```text
%APPDATA%\mpv\scripts\
```

Place:

```text
thumbfast.conf
```

in:

```text
%APPDATA%\mpv\script-opts\
```

### `thumbfast.conf`

```conf
socket=
thumbnail=

max_width=320
max_height=180

scale_factor=1

tone_mapping=no

overlay_id=42

spawn_first=no
quit_after_inactivity=20

network=no
audio=no
hwdec=no

direct_io=no

mpv_path=mpv
```

### Troubleshooting Thumbfast

If thumbnails do not appear:

1. Make sure `modern.lua` is the `with.thumbfast` version.
2. Make sure `thumbfast.lua` is in the `scripts` folder.
3. Make sure `thumbfast.conf` is in `script-opts`.
4. Move the mouse over the timeline.
5. If mpv cannot create the thumbnail subprocess, set the full mpv path:

```conf
mpv_path=C:/mpv/mpv.exe
```

Use the real path to your `mpv.exe`.

---

## autosubsync-mpv

`autosubsync-mpv` synchronizes subtitles automatically using `ffsubsync` or `alass`.

This setup uses `ffsubsync`.

Place the autosubsync folder here:

```text
%APPDATA%\mpv\scripts\autosubsync\
```

Required files:

```text
main.lua
autosubsync.lua
helpers.lua
menu.lua
subtitle.lua
```

### `autosubsync.conf`

Location:

```text
%APPDATA%\mpv\script-opts\autosubsync.conf
```

```conf
ffmpeg_path=
ffsubsync_path=
alass_path=

audio_subsync_tool=ffsubsync
altsub_subsync_tool=ffsubsync

unload_old_sub=no
overwrite_old_sub=no

new_sub_directory=
```

### Why this configuration is safe

* It does not overwrite the original subtitle.
* It keeps the old subtitle loaded.
* It creates a new `_retimed` subtitle file.
* It uses `ffsubsync` by default.
* Empty executable paths work if `ffmpeg` and `ffsubsync` are available in PATH.

### Usage

Open a video with an out-of-sync subtitle and press:

```text
n
```

The script will start subtitle synchronization.

### Troubleshooting autosubsync

Check dependencies:

```bat
ffmpeg -version
ffsubsync --version
```

If they are not detected, use full paths:

```conf
ffmpeg_path=C:/Program Files/ffmpeg/bin/ffmpeg.exe
ffsubsync_path=C:/Users/YourName/AppData/Roaming/Python/Python312/Scripts/ffsubsync.exe
```

Use forward slashes `/` or double backslashes `\\` on Windows.

---

## Optional fonts.conf

A custom `fonts.conf` is usually not required on Windows.

Recommended default:

```text
Do not use fonts.conf unless you have real font fallback problems.
```

For this setup, it is usually enough to have:

```text
%APPDATA%\mpv\fonts\Material-Design-Iconic-Font.ttf
```

and in `mpv.conf`:

```conf
embeddedfonts=yes
sub-font="Segoe UI Semibold"
osd-font="Segoe UI"
```

Do not force:

```conf
sub-font-provider=fontconfig
```

unless you know you need it.

---

## Recommended Usage

Normal playback:

```bat
mpv "video.mkv"
```

Use hardware decoding for heavy files:

```bat
mpv --profile=hwdec-safe "video.mkv"
```

Use low GPU mode:

```bat
mpv --profile=low-gpu "video.mkv"
```

Use smoother frame pacing:

```bat
mpv --profile=smooth-video "video.mkv"
```

Use HDMI surround PCM:

```bat
mpv --profile=hdmi-surround "video.mkv"
```

Use Dolby/DTS bitstream passthrough:

```bat
mpv --profile=bitstream "video.mkv"
```

---

## Design Philosophy

This configuration is intentionally conservative.

It avoids:

* forced HDR to SDR conversion
* forced full-range output
* forced audio resampling
* audio normalization
* dynamic range compression
* always-on debanding
* always-on interpolation
* automatic deinterlacing
* unnecessary shaders
* multiple OSC scripts at the same time

The goal is simple:

```text
faithful playback first, optional processing only when needed.
```

---

## Useful Shortcuts

| Shortcut      | Action                    |
| ------------- | ------------------------- |
| `Alt+p`       | Playlist menu             |
| `Alt+a`       | Audio track menu          |
| `Alt+s`       | Subtitle track menu       |
| `Alt+d`       | Audio device menu         |
| `Alt+c`       | Chapter menu              |
| `Alt+b`       | Keybinding menu           |
| `Alt+r`       | Properties menu           |
| `n`           | autosubsync menu          |
| `i`           | stats                     |
| `s`           | screenshot                |
| `S`           | screenshot with subtitles |
| `Ctrl+s`      | screenshot window         |
| `Alt+h`       | hardware decoding profile |
| `Alt+g`       | low GPU profile           |
| `Alt+v`       | smooth video profile      |
| `Alt+e`       | deband profile            |
| `Alt+Shift+e` | strong deband profile     |
| `Alt+m`       | HDMI surround profile     |
| `Alt+Shift+m` | bitstream profile         |
| `Alt+Shift+r` | restore base profile      |

---

## Third-Party Components

This setup uses third-party mpv scripts and components:

* mpv
* mpv-osc-modern
* thumbfast
* autosubsync-mpv
* ffsubsync
* FFmpeg
* Material Design Iconic Font

Respect the original licenses of each project.

Do not redistribute third-party font files unless their license allows it.

---

## Notes

This setup is made for Windows and SDR playback.

HDR playback, tone mapping, advanced shaders, Anime4K, CAS, FSR, color correction, dynamic cropping, equalizers, compressors, loudness normalizers, and other processing tools are intentionally not included by default.

They can be useful in specific cases, but they are not part of this clean videophile/audio-focused configuration.


# Screenshot




# Resources:
   GUI build: 
   https://github.com/maoiscat/mpv-osc-modern
   https://github.com/joaquintorres/autosubsync-mpv
   https://github.com/po5/thumbfast
   
   Collection of user scripts: https://github.com/mpv-player/mpv/wiki/User-Scripts  
   MPV properties page: https://mpv.io/manual/master/#synopsis

