> Inspired by existing plot digitizing tools; this is an independent open-source implementation.

**English** | [한국어](README.ko.md)

# PlotDigitizer1234

A desktop digitizer that extracts data points from graph images
(especially **Arrhenius plots**) with simple clicks and saves them as `csv` / `json`.

- Recover numeric data from graph images in papers and textbooks
- Handles **linear / log axes**; calibrate with just 2 points per axis
- Clicks **snap to the curve automatically** (edge detection); manual mode toggle available
- Automatic **linear regression** (slope, R²) after extraction — useful for activation-energy
  analysis from Arrhenius slopes
- Korean label/font support

> ℹ️ **About the name:** `PlotDigitizer1234` is a placeholder name. To avoid confusion with
> existing services of the same name (`plotdigitizer.com`, the PyPI `plotdigitizer` package),
> consider renaming it before publishing.

---

## Requirements

- Python 3.10+
- Dependencies: `numpy`, `matplotlib`, `opencv-python-headless`, `pillow`
- `tkinter` for the GUI (bundled in official Windows/macOS installers; on Linux install `python3-tk`)

## Run from source

With `uv`, no separate install is needed:

```bash
uv run --with numpy,matplotlib,opencv-python-headless,pillow plotdigitizer1234.py
```

You can also pass an image path directly:

```bash
uv run --with numpy,matplotlib,opencv-python-headless,pillow plotdigitizer1234.py /path/to/graph.png
```

## Usage

1. On launch it asks for an image.
   - **Drag & drop** an image file onto the terminal (Windows `C:\`/`D:\` paths auto-converted)
   - `v` or empty Enter → use the **clipboard image** (e.g. `Win+Shift+S` screenshot)
   - Or type the path directly
2. When the window opens, **calibrate the axes**: click 2 known points on the X axis and 2 on
   the Y axis, entering their real values.
3. **Click the data points** → they snap to the curve. Right-click undoes the last point.
   - If snapping is off, switch to manual click mode with the `Manual` button.
4. Close the window to save the results.

## Output

Saved under a `digitized/` folder next to the executable (or script):

```
digitized/<image_name>.csv    # x,y point data (opens directly in Excel)
digitized/<image_name>.json   # axis types, slope, R², and other metadata
```

(Clipboard images are first saved as `clipboard_<time>.png` in the current folder.)

## Building / distributing the Windows `.exe`

A single-file executable that runs by double-click without Python or setup is built from
`windows_build/`. See [`windows_build/README.md`](windows_build/README.md) for the build steps
and notes.

The latest prebuilt `.exe` is available on the **[Releases](../../releases)** tab.

---

## License

Released under the [MIT License](LICENSE). Free to use, modify, and redistribute, provided the
copyright notice is retained.

## Acknowledgments

Inspired by existing plot digitizing tools (WebPlotDigitizer, Engauge Digitizer, etc.).
This is an independent, from-scratch open-source implementation and is not affiliated with any
of them.
