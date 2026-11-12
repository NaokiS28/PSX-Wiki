---
title: CD-ROM Guide
description: Guide to help build a disc image to boot on PS1
published: true
date: '2026-11-12T13:54:20.165Z'
tags: []
editor: markdown
dateCreated: '2026-11-12T13:47:26.467Z'
---

# CD-ROM Guide

# PlayStation  1 CD-ROM Guide

## Brief overview
Initially the PlayStation's disc format was developed for audio (and shares a lot of similarities), \
yet has some details in it's requirements that were specifically made for the PlayStation .

Like Audio CD's the contents of a disc are arranged in tracks, and are indexed by number (1-99). \
Each of these tracks can be audio (as CD-DA/Red Book) or data (as CD-XA).

> ℹ️ **NOTE**: CD-DA tracks can be also played in any CD player, though data tracks will usually be silent.

Data can either be in Mode1 (Yellow Book) or Mode2 (Green Book). The PlayStation can actually read both modes, but conventionally uses Mode2. \
If you want to include content using XA-ADPCM audio (for example "regular" music, or FMVs with interleaved XA-ADPCM), you _have_ to use Mode2.

> ✅ **TIP**: More technical details can be found over on [psx-spx cdrom sector encoding](https://psx-spx.consoledev.net/cdromformat/#cdrom-sector-encoding)

The easiest way to visualize a CD, is it being a very long string of Data all combined, \
and laid out in a circular pattern going from the inside of the disc outward. \
You don't access "files" directly, but specify which sector (based on time) they live in. \
The Playstation (and generally how every CD works) can seek directly to these, to retrieve the data for you. \
This concept is explained in more detail later on in the [cuesheet](#cuesheets) section of this document.

## Bootable disc details
In order to make a disc bootable by the PlayStation, several things are expected to be setup in a certain way. \
Track 1 **must** be an [ISO9660](https://en.wikipedia.org/wiki/ISO_9660) `data` track, and contains `license data` and your `executable`. \
Every track after that can be used in any combination you see fit, as per your games requirements.

### License data
This exists in the ISO9960 "system area", which are the first 16 sectors reserved for platform specific data.
- Consists of a [region-specific string](https://psx-spx.consoledev.net/cdromformat/#cdrom-protection-scex-strings) and the PlayStation logo in .tmd format

Some differences between regions, and how the console responds to this exists. \
The following table shows which region consoles will boot without the license.

| Region | SCPH-1XXX/7XXX | SCPH-1XX (Slim/PSone) | Note | 
| :---: | :---: | :---: | :--- |
| **NTSC-J** | ❌ no | ❌ no | _requires_ a modchip (even with bypass via Unirom/tonyhax) | 
| **NTSC-US** | ✅ yes | ✅ yes | Still _desirable_ to include since otherwise the logo won't show up | 
| **PAL** | ✅ yes | ❌ no | unless chipped with a region-patching chip, or bypassed via Unirom/tonyhax, PSone requires it | 

Generally it makes sense to include Japanese license data, or provide both Japanese and PAL images.

### Executable
There are 2 ways to have your executable start from disc:

###### auto-boot

The playstation can boot any `PSX.EXE` that is in the root of your disc.\
When you build your game with `ps1-bare-metal`, you can simply rename the *.psexe file.

###### config
The more common way is to have a file called `SYSTEM.CNF` in the root containing BOOT/TCB/EVENT/STACK entries.

> ⚠️**IMPORTANT**: SYSTEM.CNF must have CRLF line endings

contents of the file would contain the following definitions:

`BOOT` gives a location to the executable that needs to start. \
`TCB` defines the number of threads the kernel allocates (optional). \
`EVENT` is the amount of event slots are allocated in the pool (optional). \
`STACK` defines the memory address at which the Stack starts.

Example (default) configuration: 
```
BOOT = cdrom:\FILENAME.EXE;1
TCB = 4
EVENT = 10
STACK = 801FFFF0
```

All of these are hex values, so in the example given TCB is 4, and EVENT is 16. \
_TCB and EVENT are irrelevant when not using BIOS thread/event APIs._

> ℹ️ **NOTE**: Executable name can be anything, doesn't have to be SLUS_xxx.xx or similar. \
> Emulators and ODEs may display the executable name as game name.

#### Limitations
Certain limitations exist for your file and folder structures, as the PlayStation BIOS is _known to contain some bugs_ that may lead to crashes otherwise.

Your file and folder names must be a maximum of 16 characters long.
- This include file extension (and the period)
- In practice DOS-style 8.3 names are typically used
  
Root directory descriptor must be a maximum of 2KB. \
This means you should stick to having a maximum of 30 entries in the **root folder**.

Path table must be a maximum of 2KB. \
This allows for a maximum of up to 45 folders total, **including subfolders**.

Executable (and SYSTEM.CNF if used) must be within the first 1MB of the track. \
If track 1 is the only track, at least 1-2MB of padding must be added at the end (TODO: expand explanation!).

> ℹ️ **NOTE**: Some authoring tools may not provide a way to guarantee this

#### Layout
The order files appear physically on the disc is important, to minimize seek times. \
The PlayStation  uses [CLV](https://en.wikipedia.org/wiki/Constant_linear_velocity), so read speed is *not* affected by file location.

> ℹ️ **NOTE**: Not all authoring tools allow file order customization. 
> Common workaround: naming files alphabetically (e.g. ZPADDING.DAT)

In some cases where you share data between different sections of your game, it might be best to have duplicate shared objects closer together rather than having to jump back and forth between files and folders.

### Image formats
There are several image formats you can use to create your disc. \
If you plan to use it on real hardware the best option is `.bin/.cue`, followed by `.iso` (if you do _not_ require CD-XA use). \
For emulation there are other (more compressed) formats available.

The following lists will go over all the differences and details.

#### CUE <a name="cuesheets" id="cuesheets"></a>
A cuesheet informs (in a human readable format), where your data lives on a disc, and what it contains (_table of contents_). \
It's a definition of the `Tracks` we spoke about earlier, and what each of them contains.

A cuesheet can be added to either `.iso` ("MODE1/2048") or `.bin` ("MODE2/2352") files.

`.cue` cuesheets:
  - Required for discs with multiple tracks, superfluous for data-only discs
  - Table of contents
    - Contains paths to each track's image file (must be edited when renaming files)
    - CD-DA track entries point to separate .bin files (containing raw 16-bit PCM audio)
    - Redux and burning tools support WAV/FLAC/MP3/etc. files for CD-DA, but DuckStation does not.
  - Single .bin mode: all tracks can be concatenated into one large .bin file
    - The data track must be in Mode2 .bin format for this to work
    - The starting offset of each track within the .bin is stored in the cuesheet

###### Examples
Example of a basic cuesheet for a single binary: 
```
FILE "GAME.BIN" BINARY
  TRACK 01 MODE2/2352
    INDEX 01 00:00:00
```

If you want to add (for example) additional audio files to your game, this is simply done by defining extra tracks, \
and defining where the audio starts on the disc.

You define a unique `TRACK` number, and below that the `INDEX` at which it starts in MM:SS:FF format. \
M are minutes, S are second, and F is frames (defined as 75 frames per second). 

In order to calculate the offset of Data to Time, you can use the following equation:

```
N = file size (in bytes)
lba = N / sector size (2048 for Mode1, 2352 for Mode2)
minute = lba / (75 * 60)
second = (lba / 75) % 60
sector = lba % 75 (75 frames = 1 second)
```

You need to create a `buffer window` before each Track. \
You define this buffer (called the pregap), using `INDEX 00` and `INDEX 01` inside the Track definition body. \
This helps the laser pick-up seek, track, and lock onto the start of the audio (Index 01) without missing the transition.

> ℹ️ **NOTE**: Red Book Standard: The official Philips/Sony Red Book audio CD specification defines a 150-frame (2-second) region as a standard buffer area preceding the main audio index.

The following is an example of what a cuesheet would look like with 2 additional music files (and the pregap defined). \
The fictional "GAME.BIN" is exactly 2Mb, or `2,097,152` in bytes, which makes the lba (defined above) a total of `891`, \
meaning the following files starts at the next sector `892`. \
Following the above equation, that becomes `00:11:67` in MSF Format.

```
FILE "GAME.BIN" BINARY
  TRACK 01 MODE2/2352
    INDEX 01 00:00:00
  TRACK 02 AUDIO
    INDEX 00 00:11:67
    INDEX 01 00:13:67
  TRACK 03 AUDIO
    INDEX 00 01:43:67
    INDEX 01 01:45:67
```

The first audio track was exactly `01:30:00` long, and was added to the length of the start of the previous to define the position of the second audio track. \
You can keep counting the lengths up together, to add as many tracks as you need, up to a maximum of 99 Tracks. 

> ℹ️ **NOTE**: The official Red Book Standard for maximum length of Time used on a CD, is `74:30:00`.

You can expand your track data with additional information like `TITLE` and `PERFORMER`, but they are not required for the PS1. \
More information can [be found here](https://kodi.wiki/view/Cue_sheets#Cue_Sheet_Commands).

#### BIN
Supported by all emulators and ODEs, but requires third-party burning tools. 

> ℹ️ **NOTE**: make sure when creating a disc to load/burn the `.cue`, **not** the first track `.iso/.bin`

`.bin` using 2352-byte mode2 sectors:
  - Full mode2 and XA-ADPCM support
  - Requires conversion from .iso or specialized tools (mkpsxiso, Redux Lua API etc.)

#### ISO
Supported by Redux and recent versions of DuckStation, but not by any ODEs (yet).

`.iso` using 2048-byte mode1 sectors:
  - Standard format everywhere, except on the PlayStation 
  - Easiest to author using standard software (ImgBurn, genisoimage etc.)
  - No support for mode2
    - No XA-ADPCM including in FMVs (but CD-DA can still be used with a cuesheet)
    - Smaller than an equivalent .bin since no ECC data is stored in the image
  - Can be burned natively on any OS without additional software

#### CHD
Generally supported by emulators, as it was originally created for use with MAME, but not by ODEs nor burning tools.

`.chd` (Compressed Hunks of Data):
  - All-in-one compressed container format, must be generated from any of the formats listed above
  - Uses FLAC internally for audio tracks, good for compressing CD-DA-heavy images
