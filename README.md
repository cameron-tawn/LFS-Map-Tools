LFS Map Tools

Create track maps from Live for Speed replays and display live car positions on a custom desktop minimap.

## The tools

**LFS Path Generator** converts a saved single-player replay (`.spr`) into a path file (`.pth`) and an optional matching SVG map. It supports closed circuits and open routes, with adjustable node spacing, road width, and outer width. Manual OutSim capture and RAF import are also available.

**LFS SVG Overlay** displays that map over LFS and uses InSim to show live car positions and headings. Move and resize the map, customise car colours, enable driver names, and lock the overlay so mouse clicks pass through to LFS.

## Requirements

- Windows x64 for the standalone release.
- Live for Speed 0.7A or newer, with access to the track, layout, and cars used in the replay.
- Windowed or borderless LFS for the overlay. Exclusive fullscreen is not supported.

The standalone applications include Python and their required libraries. LFS itself is not included.

## Standalone release

1. Download the release ZIP when available from this repository's Releases section.
2. Extract the entire ZIP before launching either application.
3. Open `LFS Path Generator/LFS Path Generator.exe` or `LFS SVG Overlay/LFS SVG Overlay.exe`.

Keep each application's `_internal` folder beside its executable. Read `START HERE.md` in the release for the complete setup instructions.

## Create your PTH and SVG

1. In LFS, drive and save a single-player replay. Complete at least one full lap for a circuit, or drive the entire intended route for an open layout.
2. Close LFS and open **LFS Path Generator**.
3. Set your node spacing and road widths. You can leave the initial values for your first attempt.
4. Enable **Closed loop (one lap)** for a circuit. Leave it disabled to export the whole replay as an open route.
5. Enable **Save SVG track map when converting SPR**.
6. Click **Convert SPR to PTH…** and select the replay, normally found in LFS's `data/spr` folder.
7. Choose where to save the PTH. If prompted, locate the appropriate `LFS.exe`. Use **Choose LFS installation…** to change installations.
8. Leave LFS alone while the generator captures the replay. When finished, it saves the files and closes the LFS instance it launched.

You should now have a matching pair:

```text
MyTrack.pth
MyTrack.svg
```

Keep these files together. Automatic replay conversion uses local InSim and does not require OutSim configuration or an RAF export.

## Display your map in LFS

1. Start LFS normally and load the same track and layout used for the recording.
2. Switch LFS to windowed or borderless mode.
3. Enable InSim by entering `/insim 29999` in LFS chat.
4. Open **LFS SVG Overlay** and browse for your PTH. Its same-name SVG is selected automatically when available; otherwise browse for the SVG separately.
5. Click **Load map files** after manually editing either file path.
6. Set **LFS address** to `127.0.0.1` when running on the same computer. Set **InSim TCP port** to the number enabled in LFS.
7. Enter an admin password only if required, then click **Connect**.
8. Enter the track. Cars with current position data should appear on the map.
9. Drag the overlay into position and resize it from the bottom-right corner. Adjust marker size, colours, and optional driver names.
10. Tick **Lock overlay** so mouse clicks pass through to LFS.
11. Once the overlay works, disable **Show small map** in LFS.

You can choose another port; the LFS command and overlay setting must match. If another InSim application is already connected, use LFS's existing port. Multiple TCP clients can share that port. Separate LFS instances need separate ports. Changing an active port may disconnect existing clients.

Keep the settings window open while using the overlay. Return to it to unlock, hide, or adjust the map. Closing settings exits the overlay.

The PTH and SVG are read directly by the overlay; they do not need to be installed into LFS.

## Car colours

All seven colours can be changed using the colour buttons in settings.

| Category | Default RGB |
| --- | --- |
| Car 1 lap ahead | 255, 255, 192 |
| Car ahead | 224, 224, 32 |
| AI ahead | 32, 176, 240 |
| Own car | 32, 224, 32 |
| AI behind | 48, 48, 240 |
| Car behind | 255, 160, 0 |
| Car 1 lap behind | 208, 96, 0 |

Automatic reference selects your local human car, or the viewed car when no local human car is available. You can select a specific reference driver when spectating or using a dedicated-host connection.

## Alternative capture methods

### Manual OutSim capture

1. Open **OutSim setup** in Path Generator. With LFS closed, apply the displayed settings to its `cfg.txt`, then restart LFS.
2. Load the SPR, select an in-car or custom camera, and pause at the beginning of the route.
3. Click **Start capture**, then resume the replay. Keep the same car and camera and play the route continuously.
4. Pause at the end and click **Stop capture**. For a circuit, capture exactly one lap and enable **Closed loop (one lap)**.
5. Adjust spacing and widths, then click **Save PTH…**.

### RAF import

1. In LFS, open the SPR and select **Esc → Output lap data** during the lap before the one you want.
2. Let the export finish.
3. In Path Generator, click **Import RAF** and select the file from LFS's `data/raf` folder.
4. Enable **Closed loop (one lap)** for a circuit, then save the PTH.

## Dependencies

| Package | Purpose | Standalone release version |
| --- | --- | --- |
| PySide6 Essentials | Overlay interface, SVG rendering, and graphics | 6.10.1 |
| shiboken6 | Python bindings required by PySide6 | 6.10.1 |
| PyInstaller | Builds standalone Windows applications; build-time only | 6.22.3 |

Path Generator uses Python's built-in `tkinter` interface with Tcl/Tk. Other standard-library modules include `socket`, `struct`, `xml.etree.ElementTree`, `threading`, `queue`, `subprocess`, `pathlib`, `tempfile`, `shutil`, `json`, `math`, `time`, `bisect`, `re`, `os`, and `sys`.

InSim and OutSim are LFS interfaces, not separate Python packages. Bundled third-party components retain their own licences; see **Third-party licences** in the standalone release.

## Settings

Overlay file selections, colours, marker size, driver-name visibility, and window geometry are saved on exit to:

```text
%LOCALAPPDATA%\LFS SVG Overlay\settings.json
```

Admin passwords are not saved.

## Limitations

- Use an unmodified SVG produced by this project's Path Generator with its matching PTH. Arbitrary SVG maps do not contain the coordinate mapping required by the overlay.
- The path follows the recorded driving line. Road and outer widths are user-defined full widths centred on that line, rather than measured track boundaries.
- A single recording cannot supply measured road edges, pit routes, or the actual pole position. RAF split times provide approximate intermediate split nodes; replay capture provides positions only.
- The overlay stays north-up; car arrows show heading.
- Spectators, garage cars, and cars whose positions have not updated for three seconds are hidden.
- Lap-relative colours depend on meaningful LFS lap and native track-node data. Some open layouts may not provide suitable data.
- Standalone startup, local protocol, geometry, and rendering checks have been performed. Live operation against LFS still requires verification.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Cannot connect | Confirm LFS is running, InSim is enabled, and ports match. Check any required admin password. |
| Connected but no car markers | Enter the track. Spectators and garage cars are hidden. |
| Overlay disappears over LFS | Use windowed or borderless mode and enable **Show overlay**. |
| Markers do not align | Use the matching PTH/SVG pair and the same LFS track/layout. |
| Cannot move the overlay | Untick **Lock overlay** in settings. |
| Conversion cannot find a complete lap | Record a complete lap or disable **Closed loop** to export an open route. |
| Replay will not load | Select an LFS installation that can play the replay and has access to its track and car. |

## Tests

Run the geometry, colour classification, roster handling, and simulated InSim connection checks with:

```powershell
python -m unittest test_overlay -v
```

The connection test opens a local TCP socket. These checks do not replace testing with a running LFS session.

## References

- [LFS InSim and OutSim documentation](https://www.lfs.net/programmer/insim)
- [InSim packet reference](https://en.lfsmanual.net/wiki/InSim.txt)
- [LFS file formats](https://en.lfsmanual.net/wiki/File_Formats#PTH)
- [LFS lap export information](https://www.lfs.net/replay-analysers)
