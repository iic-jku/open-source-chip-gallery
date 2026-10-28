# Contributing

The gallery is open to every open-source chip that was designed at the Institute for Integrated Circuits and Quantum Computing (IICQC) at JKU Linz, also when the design was done together with partners. A chip is added as soon as it has been submitted to a shuttle. Its layout has to be made with an open-source PDK and its design data has to be public, ideally in a repository of the [iic-jku](https://github.com/iic-jku) organization.

All steps that need tools are run inside the [IIC-OSIC-TOOLS](https://github.com/iic-jku/IIC-OSIC-TOOLS) container with tag `2026.08` or later.

## Adding a Chip

1. Create a directory named `<pdk>-<design>-<shuttle>`:
   - `<pdk>` is the PDK the chip was designed in, for example `ihp-sg13g2`, `ihp-sg13cmos`, `ihp-sg13cmos5l`, `gf180mcuD` or `sky130A`.
   - `<design>` is a short lowercase name of the design, for example `sparx`, `tinywhisper` or `mpc` for a multi-project chip.
   - `<shuttle>` names the exact run, so a design that is taped out again gets a new directory. It is `mpw-<YYYY>-<MM>` with the year and month of the tapeout for an IHP MPW run, `ws-run<N>` for wafer.space run `<N>` and the shuttle name for Tiny Tapeout, for example `ttsky25b`.
   - Examples: `ihp-sg13cmos-sparx-mpw-2026-03`, `gf180mcuD-mpc-ws-run1` and `sky130A-iqmod-ttsky25b`.

2. Copy the final layout into the directory as `<directory>_top.gds`. Do not rename the top cell inside the layout, so it can be traced back to the design repository. Large layouts are stored gzipped (`<directory>_top.gds.gz`) or as OASIS (`<directory>_top.oas`), which is usually much smaller. GitHub warns about files above 50 MB and rejects files above 100 MB. A layout is converted to OASIS with:

   ```sh
   sak-layconv.sh <layout>.gds <directory>_top.oas
   ```

3. Render the layout images in the chip directory:

   ```sh
   cd <directory>
   sak-render.py -t <tech> -w 2048 -s 4 -o <directory>.png <directory>_top.<gds|gds.gz|oas>
   ```

   This writes `<directory>_white.png` and `<directory>_black.png`. The `-o` option is needed because the image names are otherwise taken from the layout file name. `<tech>` is one of `ihp-sg13g2`, `ihp-sg13cmos5l`, `gf180mcuD` or `sky130A`. SG13CMOS layouts are rendered with `ihp-sg13g2`, which uses the same layer stack. Fill and dummy shapes are hidden by default. Do not edit the images by hand, so they can always be reproduced from the layout.

4. Further images are welcome, for example a pinout or photos of the fabricated die. Take them from the design repository where possible and name them `<directory>_<description>.png` with a short lowercase description, for example `<directory>_pinout.png` or `<directory>_cob.png`. Keep each image below a few MB. A labeled pinout can replace the white render as the main image of the section, as in the SPARX section. The renders stay in the directory either way.

5. Add a section for the chip to [README.md](README.md) with the [template](#section-template) below. Place it by tapeout date, with the most recent tapeout first.

6. Add a row for the chip to the overview table at the top of [README.md](README.md), in the same order as the sections. The link in the first column points to the anchor of the section heading. GitHub builds the anchor from the heading in lowercase, with spaces replaced by `-` and other punctuation removed, so `# SPARX (IHP MPW Tapeout)` becomes `#sparx-ihp-mpw-tapeout`.

7. Open a pull request.

## Updating a Chip

- When the status of a chip changes, for example when the dies are delivered or the first measurements are done, update the status sentence in its section. Once the dies are delivered, add photos of the fabricated chip as described in step 4.
- When the layout of a chip is replaced, render the images again so the layout and the images always match.
- A new tapeout of an existing design gets its own directory and section, so earlier tapeouts stay in the gallery.

## Section Template

Replace everything in angle brackets and remove the bullets that do not apply. The shuttle in the heading is written like in the existing sections, for example `IHP MPW Tapeout`, `wafer.space` or `Tiny Tapeout`. Further bullets for the main features and building blocks of the chip are welcome. The main image is the white render or a labeled pinout (see step 4). Additional images, such as chip photos, go below the first paragraph in the same `<div>` format, each with its own caption. Two photos with the same aspect ratio can be placed side by side with `width="45%"` and one caption, as in the multi-project chip section.

```markdown
# <Chip Name> (<Shuttle>)

<One sentence that says what the chip is.>

<div align="center">
  <img src="<directory>/<directory>_white.png" alt="<Chip Name>" width="600">
  <p><em>Render of the <pdk> <Chip Name> <chip type> (<width> x <height>).</em></p>
</div>

<A short paragraph about the chip. What does it do, how was it designed, when was it taped out and what is its current status?>

- Designers: <Name>, <Name> (<affiliation>) and <Name>
- Technology: <foundry> <process> (<node>)
- Die size: <width> x <height>
- Package: <package>
- Supply voltage: <voltage>
- <Main features and building blocks>

Repository: <link to the design repository>
```

## Writing Conventions

- List the designers in the order of the author list of the design repository. Designers are from JKU unless an affiliation is given in brackets.
- Name the technology with foundry, process and node, for example `IHP SG13CMOS (130 nm)`.
- Write dimensions as `1mm x 1.4mm` or `334.88µm x 225.76µm`, with a plain `x` and without area units.
- Give dates as month and year, for example `March 2026`.
- Keep the text short and factual. Details belong in the design repository.

## License

The text and the rendered images in this repository are licensed under the [Apache License 2.0](LICENSE). The chip layouts and the images taken from the design repositories keep the licenses of those repositories.
