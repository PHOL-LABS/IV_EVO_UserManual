# IV-Evo User Manual

User manuals in English, German, Spanish, French, Hungarian and Russian for the PHOL-LABS IV-Evo display indicator, adapted from the supplied `IV-EVO User Manual.zip` HTML documentation. This repository originated as an IV-Nano manual fork; Nano configuration instructions and sensor tables are not specifications for IV-Evo.

## Build

Use TeX Live 2025 with latexmk and biber:

```sh
latexmk IV_Evo_User_Manual
```

This produces `IV_Evo_User_Manual.pdf`. Add `_de`, `_es`, `_fr`, `_hu` or `_ru` to the target for a localized edition (for example, `latexmk IV_Evo_User_Manual_hu`). The original `IV_Indicators_User_Manual` entry points, with the same language suffixes, build the corresponding Evo editions and preserve existing CI/release artifact names. Build sequentially: aliases share chapter auxiliary files. All CI and release builds pin the exact historical TeX Live 2025 image digest. `latex-action@v3` maps `texlive_version: 2025` to `latest`, so the workflows use `docker_image` instead. Raster-only manuals need no additional Alpine packages in the Debian image.

In the prepared cloud environment, use the retained TeX Live container:

```sh
docker start iv-manual-tex
docker exec -e TEXMFVAR=/work/.aux/texmf-var \
  -e TEXMFCONFIG=/work/.aux/texmf-config \
  -e XDG_CACHE_HOME=/work/.aux/cache \
  iv-manual-tex latexmk IV_Evo_User_Manual
```

Host TeX lacks required packages. The full TeX Live 2026 image tested during setup also failed on the template's proof environment; the environment pins TeX Live 2025. Current illustrations are raster images, so SVG tools are not needed for this build. PDF/A conformance requires a separate VeraPDF check; producing a PDF alone does not establish conformance.

## Structure and sources

- `chapters_evo/`: English safety, overview, package, sensor options, Wi-Fi/wiring, applications, and Lua API appendix.
- `chapters_evo_{de,es,fr,hu,ru}/`: localized safety, overview/package, Wi-Fi, connections, applications, and Lua reference. Translations consolidate repetitive source prose while retaining the sensor requirements, calibration limits, every original figure, all API signatures and both code examples. App IDs and Lua identifiers remain unchanged; original diagram labels remain in English.
- `figures/ivevo/`: figures from the supplied Evo documentation.
- `iv_evo_reference/IVEVOUserManual.html` and its `images/`: unchanged source documentation for traceability. Open the HTML locally with its images beside it.
- `preamble/` and `frontmatter/`: shared layout and title page; Evo branding is enabled by the English entry point.

The English manual replaces the Nano Android/IV-Conf workflow with Evo Wi-Fi setup, covers BLE gear telemetry and Digifiz Next 1.0.14+, and includes the Lua runtime and module API. The Lua examples use the firmware’s 80×160 portrait coordinates, inclusive rectangle corners, an included 16-pixel font and clamped bar width.

The supplied text leaves RPM calibration units and general electrical limits unspecified. The firmware scripts contain oil/boost calibration assumptions, which are documented as script-specific rather than universal sensor specifications. The manual identifies these gaps instead of importing Nano values. The illustrated pinout is transcribed from the supplied board photograph. Package contents follow the explicit package list (indicator and wired KF2510 socket), rather than an inconsistent marketing reference to an included programmer.

## Archived Nano material

The old `chapters/`, `chapters_de/`, `chapters_es/`, `chapters_fr/`, `chapters_hu/`, `chapters_ru/`, `iv_nano_reference/` and `SENSORS.md` remain archived source material. No active manual entry point includes those chapters. All six CI and release artifacts now contain Evo documentation. Hungarian uses Hungarian Babel settings and `lang=hu` PDF metadata.

## License

Documentation is released under CC0 1.0; see `LICENSE`. Product and vehicle trademarks remain the property of their respective owners.

## Firmware integration

All six editions include a firmware controls/update chapter reviewed against [`Sgw32/iv-evo-fw` revision 3260783532b3](https://github.com/Sgw32/iv-evo-fw/tree/3260783532b3d4ad8529b23f4e907c0a6ab03262). It covers setup-button behavior, Wi-Fi/BLE mode selection, app and sensor paths, reloads, NTC settings, file backup and tar updates, along with source-specific calibration and API caveats. It takes precedence over the retained historical HTML recipes for that revision. See [the source audit](docs/FIRMWARE_SOURCES.md) for evidence and limitations. Firmware source files are not copied or modified; this is a manual update, not a claim of hardware testing.
