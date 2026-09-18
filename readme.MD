# CineDen for Plex

A native Meta Quest home theatre for your own Plex server. Films, television and live
channels, on a screen you put wherever you want it.

> **Not affiliated with, endorsed by, or sponsored by Plex.** "CineDen for Plex" is used
> with Plex's written permission. Plex is a trademark of Plex Inc.

Runs on Quest 2 and Quest 3 from one build. Quest 3 gets colour passthrough; Quest 2 gets
monochrome.

---

## What this repository is for

**Reporting problems.** This is where beta testers file what they run into, and where the
known issues are listed so you can tell a bug from something already on the pile.

---

## You will need

- A Quest 2 or Quest 3
- A Plex account, and a Plex Media Server you can reach
- For live channels: a tuner and a guide set up on that server, which is Plex Pass territory

**A server on the same network as the headset gives the best results by a wide margin.**
Over the internet you are limited by that server's upload rather than by your own download,
and it is the single biggest cause of stuttering.

---

## Reporting a problem or feature suggestion

[Open an issue](../../issues).

Some ideas of stuff to include:
- **What you did, in the order you did it.** Nearly every fault found so far depended on
  what came before it - a film's clock was wrong only after a channel had played, and a
  channel would not start only while another stream was still open. "It broke" cannot be
  chased; "I watched a channel, stopped it, then resumed a film" can.
- **Quest 2 or Quest 3**, and the version from **Settings → About**.
- **Whether your server is on the same network** as the headset or reached over the
  internet. Most playback reports come down to this.
- **What you were watching**, roughly - a film, an episode, or a channel - and whether the
  transport bar said it was converting.
- **A log**, if you want to include one or have a log, it helps.
- **Any suggestion**, anything you want to see added.

### Getting a log, if you can

This needs a computer with `adb` and developer mode on the headset. Skip it if that is not
you; a clear description is worth more than a log you had to fight for.

```bash
adb logcat -c && adb logcat -v threadtime | grep -a --line-buffered -E "QuestPlexPlayer|CineDen\]"
```

Start it, reproduce the problem, then paste the whole thing. Filter on those two patterns
and nothing narrower: a filter chosen around the symptom reliably drops the line that
explains it, which has already cost this project real time.

### Is a log safe to paste?

**One from that command is.** Every Plex URL carries your token as a query parameter, so
this app never logs a whole URL - failures are recorded with the request path only, and
anything a server sends back is masked first.

**Other sources are not.** Your token *is* in your browser's address bar and network tab
while you use Plex on the web, and in your Plex server's own logs. If you paste from
either, remove anything that looks like `X-Plex-Token=...` first.

If you think a token of yours has been exposed, revoke it at <https://plex.tv/devices> -
remove the device, then sign in again.

---

## Known issues

Please do not file these; they are known and being worked on.

- **Remote playback is bandwidth-bound**, and a live channel especially so. A film can
  buffer minutes ahead. A channel cannot buffer ahead of the broadcast at all, so a rate
  your link carries on average but not continuously stalls rather than catching up. Auto
  quality caps a remote film at 8 Mbps and a remote channel at 4 Mbps and 720p for exactly
  this reason.
- **A live channel has no timeline.** No scrub bar, no length, no way back to something
  already broadcast. Plex keeps a capture buffer that would make that possible; the app does
  not read it yet.
- **One narrow way to lose a tuner.** The ids needed to hand one back survive a restart, so
  closing the app mid-channel recovers on the next run. Clearing the app's data or
  reinstalling while a channel plays loses them, and that tuner stays held until something
  else stops the stream.
- **Dragging the scrub bar on a converted film is slow to respond.** Seeking a conversion is
  a new conversion every time.
- **Plex Home profile switching** has only been tested against one account. If your profile
  list comes up empty on an account that definitely has Home users, that is worth an issue.

---

## Signing in

Typing a password on a floating keyboard is miserable, so CineDen uses Plex's link flow: the
app shows a short code and you enter it at **plex.tv/link** on a phone or laptop. You stay
signed in afterwards.

If your account has more than one server you pick one, and that choice is remembered. It is
never overwritten automatically, so a server that was merely asleep when you last opened the
app is not forgotten.

### Profiles

An account can carry several people, and which profile is active decides whose watch history
and Continue Watching you see. Being on the wrong one means resuming someone else's films
and marking them watched on their behalf.

If your account has more than one profile you are asked once at sign-in, and can change it
later in **Settings → Account**. PIN-protected profiles work; the PIN is asked for only when
the server says one is needed.

---

## Watching

### The screen and the room

Two ways to place it:

- **Theatre** (default) - a big screen pinned in the room. Grab it to move it, grab with two
  hands to resize it, walk around it. Behaves like a television that happens to be four
  metres wide.
- **Locked** - the screen follows your gaze so it stays centred wherever you look. The
  follow lag and look-around slack are adjustable, because a screen bolted rigidly to your
  face is the fastest route to motion sickness.

Behind it you can have **passthrough** (your real room), a **solid colour** (black by
default, eight presets), or the built-in **dark cinema room**. Walking and turning are
available in the cinema room only - in passthrough you already have a real room to walk
around.

### Controls

| Gesture | What it does |
|---|---|
| Point + **trigger** or **grip** | Everything: buttons, handles, dragging |
| **A** or **X** | Play / pause |
| **B** or **Y** | Recentre the screen and the menu in front of you |
| **Menu** (left controller) | Show or hide the on-controller guide to all of this |
| Left thumbstick | Walk, in the cinema room |
| Right thumbstick | Turn. Snap by default; smooth is in Settings → Controls |
| Violet corner cubes | Drag to resize the screen |
| Bar above the screen | Drag to move the screen |
| Grab the picture itself | Also moves it; two hands resizes it |

Every button does the same thing on both controllers. The thumbsticks are the exception, one
job each.

None of that is discoverable on its own, so the app explains itself: pressing **menu** on
the left controller labels the buttons on the controller you are holding, each label on a
leader line to the thing it describes. It appears on the first launch and waits for you to
raise a controller. **Settings → Controls** brings it back for anyone who has forgotten
which button that was.

### Your library

The root lists your libraries with Continue Watching beneath them - that way round because
the libraries are the way into everything, and a few part-watched films would otherwise push
every folder below the fold.

Inside a library there is search, a sort (A–Z, Z–A, date added, year, rating), a filter
(all, unwatched, in progress), and a grid/list toggle. Grid leans on the artwork and is the
default; list spells out runtime, rating, content rating and how far through you are.

All of it is done by the server rather than in the headset, so it orders your whole library
and not just the page on screen. **Load more** pages further in.

Selecting a film that is already playing folds the menu away rather than starting it again.

### Live TV

If your server has a tuner and a guide, a **Live TV** tab appears next to your libraries.
Each channel shows its number, its call sign and what is on it right now, so you can tell
what you are about to watch before you start it.

Tuning takes a few seconds - the server has to claim a tuner and start converting the
broadcast - and the spinner names the channel while you wait. Over-the-air television is
MPEG-2 with AC-3 sound, neither of which the headset can play as-is, so a channel is always
converted by the server. That is normal and not a fault.

### What plays, and what gets converted

For each item the app either plays your file untouched or asks the server to convert it, and
the transport bar says which and why.

It is rarely about the picture. Quest hardware decodes H.264, HEVC including 10-bit, VP9,
MPEG-2 and MPEG-4 comfortably at 4K. **It is almost always the sound.** The headset has no
AC-3, E-AC-3, DTS or TrueHD decoder at all.

A conversion is also forced by an **HDR** picture, since nothing here can display HDR and
the server is the only thing in the chain that can tone map it properly rather than turn it
green, and by **burned-in subtitles**, which have to be drawn into the picture because the
player has no way to draw text over it.

**Quality** and **captions** are in the transport bar rather than in Settings, because both
are things people reach for in reaction to what they are watching. Auto reads the
connection.

## Your data

CineDen talks to your Plex account and your Plex server, and to nothing else. There is no
analytics, no crash reporting and no server of ours.

What is stored on the headset: your Plex token, which server and profile you chose, and your
settings. Uninstalling the app removes all of it.

The full policy the store listing points at:
<https://echo-james.github.io/cineden-legal/privacy.html>
