# V175 Bin Finder

Scan a material number or serial number and the page shows, in very large type, which bin of van V175 it goes in.
Parts are grouped by equipment model into **bins 1-30** (for example bin 1 = Lexmark MS725 / MS823). Serialized units keep
their own spots (locked drawers, green totes, cargo floor).
Nothing prints and nothing is installed.

No inventory is stored in this repository. Each person loads their own SAP exports into the page,
and the files are read in the browser only. They are never uploaded. The page remembers them on that computer until
you load new ones or click **Forget files on this computer**.

## What's in the repo

| Path | What it is |
|---|---|
| `index.html` | The bin finder page |
| `layout/v175_bins.csv` | Which bin each material number goes in (1-30, or a spot code for serialized units), plus maker / model / equipment group |
| `layout/v175_locations.csv` | What each bin holds (bins 1-30 and the serialized-unit spots) |

The page reads the two layout files from the same site, so open it from a web address (for example GitHub Pages),
not by double-clicking `index.html`.

## Files you load in the page (same columns as the SAP exports)

| File | Required columns |
|---|---|
| Van inventory (e.g. `VanInventory.xlsx`) | `Material`, `Material Description`, `Storage Location`, `Unrestricted` |
| Serialized items (e.g. `7510.xlsx`) | `Serial Number`, `Material`, `Location` (`Description` is used if present) |

`.xlsx` and `.csv` both work. Only rows whose storage location / location matches the van box (default `V175`) are used.
The page tells you which file is which by its columns, so you can pick both at once or drag them onto the page.

## Using it

1. Open the page and load the two files once.
2. Click in the scan box (it stays selected) and scan, or type and press Enter.
3. The bin number fills the screen (blue). Serialized units show their spot instead: green for a tote, amber for
   locked storage, blue for the cargo floor. Red means the code isn't found or the part has no bin yet. **Full screen** hides the browser's bars.

| Scan or type | Shows |
|---|---|
| `18000376` (material #) | Its bin, bin name and where it is, the part, quantity on hand, and what else is in that bin |
| `CNC41931WD` (serial #) | The same, with that unit's serial |
| `18000376\|1` | Reads the material # out of an older printed label |
| Unknown code | **NOT FOUND**, a beep, and the code is listed under **Unknown codes**. Type the material # next to it and that code works from then on (saved in this browser) |

**Recent** under the display lists the last 10 parts scanned; click one to show it again.

## Moving a part or adding a new one

Edit `layout/v175_bins.csv` (`Material,Bin,Maker,Model,Group`). Parts in the inventory that aren't in this file
show as **NO BIN**, and the page counts them after you load your files.
