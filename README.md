before we start, I want to give a special THANK YOU to https://github.com/lostumutz

# PresetConverter

Convert NVIDIA Ansel / Freestyle presets to ReShade, and ReShade presets back to
Ansel / Freestyle.

A single self-contained Windows executable. No Python, no installer, no runtime
to download.

## Download

Grab `PresetConverter.exe` from this repository and run it. Windows may show an
"unknown publisher" warning because the file is not code-signed; choose
**More info → Run anyway**.

## Usage

Double-click the executable for the interactive menu:

```
  ansel to reshade - 1
  reshade to ansel - 2
  quit             - Q
```

Choose a direction, then pick a file, a folder (every `.ini` inside is
converted) or a `.zip`.

Command line:

```
PresetConverter.exe preset.ini -1      Ansel / Freestyle -> ReShade
PresetConverter.exe preset.ini -2      ReShade -> Ansel / Freestyle
PresetConverter.exe preset.ini         detect the direction automatically
PresetConverter.exe folder -1 -n       preview a whole folder
```

### Options

| Option | Meaning |
| --- | --- |
| `-o FOLDER` | write output somewhere else |
| `-n`, `--dry-run` | preview changes, write nothing |
| `-f`, `--force` | overwrite existing output files |
| `-v`, `--verbose` | show every changed line |
| `-s FOLDER` | extra folder of `.fx` files to read filter names from (repeatable) |
| `-k`, `--keep-unknown` | direction 2: keep filters Freestyle has no shader for |
| `--keep-sorting` | direction 2: copy ReShade's full sorting list |
| `-p`, `--prune` | direction 2: also remove `[Filter.fx]` sections with no Freestyle shader |
| `-i`, `--install` | direction 2: copy the result into Freestyle's preset folder |

## What it does

Both runtimes share the same preset layout, so values, sections and
preprocessor definitions are carried over unchanged. Only the technique lines
differ:

```
Ansel / Freestyle                    ReShade
TechniquesSorting=...                TechniqueSorting=...
Techniques=Vibrance,...              Techniques=Vibrance@Vibrance.fx,...
```

- **Direction 1** turns bare Ansel names into `Technique@File.fx` entries and
  reports filters that do not exist in ReShade (NVIDIA-only filters such as
  BeautifyAO are removed).
- **Direction 2** turns `Technique@File.fx` entries back into the bare names
  Freestyle loads, drops shaders Freestyle does not ship, and flags filters it
  cannot resolve. `-k` keeps them instead.
- Output files are named `*_reshade.ini` and `*_ansel.ini`.
- Freestyle presets live in `C:\Program Files\NVIDIA Corporation\Ansel\Custom`.
  Copying there needs administrator rights; `-i` does it for you.
- Filter names are read from the local Ansel installation when present, with a
  built-in fallback list otherwise.
- Existing output files are never overwritten without `-f` (or a confirmation
  in interactive mode).
