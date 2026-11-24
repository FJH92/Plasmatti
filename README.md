# Plasmatti

**Current version:** v1.0.0
**Latest version file:** `src/plasmatti.py`

Plasmatti is an open-source Python desktop application for automated post-acquisition processing of inductively coupled plasma optical emission spectrometry (ICP-OES) data.

Plasmatti is the second application in a series of open-source tools for laboratory data processing. The first, [Elementti](https://github.com/FJH92/Elementti), processes and visualizes SEM-EDS elemental maps.

## Citation

The article describing Plasmatti is in preparation. Until it is published, please cite:

Jafarihonar, F., Bezerra, L., & Vainio, E. Plasmatti: An open-source desktop application for automated processing of ICP-OES data. Manuscript in preparation, 2026. https://github.com/FJH92/Plasmatti

## Overview

In many laboratories, ICP-OES results are produced afterwards in a spreadsheet: the blank is subtracted, standards measured before and after the samples are used to correct for drift, one emission line is chosen per element, deviating replicates are removed, and the concentration is converted to the reporting basis with the dilution and, for solids, the sample mass and final volume. With many samples and elements, this is time-consuming, error-prone, and difficult to trace.

Plasmatti carries out these steps through a guided wizard that needs no programming, records every decision, and explains each result in words and formulas. Routine steps are automatic, while information that cannot be determined from the data is not guessed but asked from the user as a question.

## Main features

* read PerkinElmer Optima replicate reports (CSV and text-based PDF) directly
* import data from other instruments in a documented CSV format (see below)
* find and recognize the autosampler file and the dissolution file of a session by their content
* compare calibration and sample concentrations so that no dilution or mass factor is applied twice
* never turn instrument errors, such as saturated readings, into numbers
* identify every measurement as blank, standard, sample, or rinse, and ask numbered questions in plain sentences when it cannot
* fill in dilutions, masses, and final volumes from sample names or the dissolution file, with their source
* correct for drift for each sample by interpolating the blank and the standards measured before and after the samples
* choose the emission line and view from the standards and a built-in limit table
* treat outlying replicates with Dixon's Q test (95 %) or other stated rules
* calculate results on another emission line when the chosen one has no value, if the two lines agree
* report liquids in mg/L and solids in mg/kg, optionally on a dry basis
* compare each result with its reporting limit (LOQ) and mark results above the highest standard or corrected with a deviating standard
* show the results as a colored grid, with a step-by-step explanation of every result
* export a review workbook, a report workbook, a Word report with every rule and calculation, a detailed calculations workbook, and a project file
* independently recalculate every reported result during export to check the calculations
* reopen and recalculate saved projects, for example when masses or dilutions are corrected
* use your own method table instead of the built-in limit table
* run from the command line without PySide6
* create a standalone Windows executable
* load two built-in synthetic demonstration sessions (liquid and solid)

## Repository contents

* `src/plasmatti.py` – the program, as one file (the built-in limit table, demonstration sessions, logo, and icon are included)
* `sample_data/` – the two demonstration sessions as files
* `requirements.txt` – required Python packages
* `CHANGELOG` – changes in each version
* `CITATION.md` – how to cite Plasmatti
* `LICENSE` – software license
* `README.md` – project description and usage instructions

## Requirements

* Python 3.10 or newer
* PySide6 ≥ 6.5 (only for the desktop application)
* openpyxl ≥ 3.1
* PyMuPDF ≥ 1.23
* xlrd ≥ 2.0.1 (optional, for some older `.xls` files)

Plasmatti runs on Windows and Linux.

## Installation

1. Install Python 3.10 or newer.
2. Clone or download this repository.
3. Install the required packages:

```bash
pip install -r requirements.txt
```

Alternatively, on Windows:

```bash
py -m pip install PySide6 openpyxl PyMuPDF
```

## How to run

Start the desktop application:

```bash
python src/plasmatti.py
```

On Windows, this may also be run with:

```bash
py src/plasmatti.py
```

To check the installation without opening a window, run the built-in demo check:

```bash
python src/plasmatti.py --check-demo
```

## Command line

The command-line interface uses the same calculation engine as the desktop application and does not need PySide6. To see its options:

```bash
python src/plasmatti.py analyze --help
```

For example, to process the built-in liquid demonstration session and save the outputs and the project file:

```bash
python src/plasmatti.py analyze --demo --output results --save-project
```

The other commands are `history` (list the run folders in a ZIP file or directory) and `methods` (list the bundled method tables).

## Windows executable

For users who do not want to run the Python source code directly, a Windows executable may be provided as a GitHub release asset. Use the GitHub Releases page to download it when available.

You can also build it yourself. Start Plasmatti from the `.py` file and click **Create Plasmatti.exe** on the welcome page, or run:

```bash
python src/plasmatti.py --make-exe
```

## Demo data

Plasmatti includes two synthetic demonstration sessions, one with liquid samples and one with digested solid samples. They can be opened from the first step of the wizard with **Liquid demo** and **Solid demo**, and they are also available as files in `sample_data/`.

The demonstration data are invented and do not come from real samples. Each session folder contains an `ABOUT_DEMO.txt` that explains what to look for.

## Typical workflow

In the desktop wizard, one session (one instrument run with its blanks, standards, and samples) is processed in six steps:

1. **Import:** choose the replicate report of the session; the autosampler and dissolution files in the same folder are found automatically.
2. **Questions:** check the identified role of every measurement and answer any numbered questions.
3. **Samples:** confirm liquid or solid samples, the dilutions, and, for solids, the masses and final volume.
4. **Calculation rules:** keep the recommended rules or choose the alternatives.
5. **Results:** check the results grid and the explanation of any result.
6. **Export:** save the workbooks, the Word report, and the project file to a new folder.

## Output files

Each export creates a new folder containing:

* `ICP results - review.xlsx` – a colored workbook with remarks for checking the results in the laboratory
* `ICP results - report.xlsx` – a black-and-white report for the researcher
* `Detailed explanations.docx` – a Word report that explains every rule with its formula and works out the results with the numbers of the session
* `Detailed calculations.xlsx` – every replicate, blank, standard, and decision
* `Project.plasmati.json` – the project file, which can be reopened and recalculated

## CSV format for other instruments

Data from instruments other than PerkinElmer Optima can be imported as a CSV file with one row per measurement and emission line. Column names are not case sensitive.

| Column | Required | Content |
|---|---|---|
| `sequence` | yes | Position of the measurement in the run (1, 2, 3, …) |
| `position` | yes | Autosampler position |
| `sample_id` | yes | Name of the solution |
| `channel` | yes | Element, wavelength in nm with three decimals, and view, for example `Ca 317.933 axial` (view: `axial`, `radial`, or `unknown`) |
| `rep1`, `rep2`, … `repN` | one of these two | Concentration of each replicate in mg/L |
| `mean_mg_l`, `sd_mg_l` | one of these two | Mean and standard deviation of the replicates in mg/L |
| `rsd_pct` | no | Relative standard deviation in % |
| `replicate_count` | no | Number of replicates, if not given by the `rep` columns |
| `instrument_error` | no | Text of an instrument error; the reading is then not used as a number |

Numbers use a dot as the decimal mark. Each combination of `sequence` and `channel` may appear only once. If `sample_id` does not contain the sample names, put the autosampler file (columns `position` and `sample_id`) in the same folder.

Example:

```
sequence,position,sample_id,channel,rep1,rep2,rep3
1,1,Standard blank 1,Ca 317.933 axial,0.01325,0.01076,0.01265
2,2,Ca+Cu+Fe+K+Mg+Na+Pb+Zn 1 ppm,Ca 317.933 axial,1.058,1.061,1.069
```

## Development

Plasmatti was developed in the High-Temperature Processes and Materials Group at Åbo Akademi University, Turku, Finland, using ten years (2016–2026) of ICP-OES sessions of the laboratory.

## Status

Version **v1.0.0** corresponds to the version described in the article (in preparation).

## Authors

Farzad Jafarihonar, Luis Bezerra, and Emil Vainio
Åbo Akademi University
Turku, Finland

Contact: farzad.jafarihonar@abo.fi

## License

Plasmatti is distributed under the GPL-3.0 license. See `LICENSE` for details.
