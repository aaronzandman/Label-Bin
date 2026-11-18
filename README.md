# V175 Scan Station

Scan a material number or serial number, see which bin it lives in, and print its label on the Zebra GK420d.

**Live page:** https://aaronzandman.github.io/Label-Bin/

No inventory is stored in this repository. Each person loads their own SAP exports into the page,
and the files are read in the browser only. They are never uploaded. The page remembers them on that computer until
you load new ones or click **Forget files on this computer**.

## What's in the repo

| Path | What it is |
|---|---|
| `index.html` | The scan station page (works on GitHub Pages) |
| `layout/v175_bins.csv` | Which bin each material number goes in, plus maker / model / part type for the label |
| `layout/v175_locations.csv` | Bin names and where each bin is in the van |
| `v175_TEST_label.zpl` | One test label, to check the printer setup from Notepad |

## Files you load in the page (same columns as the SAP exports)

| File | Required columns |
|---|---|
| Van inventory (e.g. `VanInventory.xlsx`) | `Material`, `Material Description`, `Storage Location`, `Unrestricted` |
| Serialized items (e.g. `7510.xlsx`) | `Serial Number`, `Material`, `Location` (`Description` is used if present) |

`.xlsx` and `.csv` both work. Only rows whose storage location / location matches the van box (default `V175`) are used.
The page tells you which file is which by its columns, so you can pick both at once or drag them onto the page.

## Set up the Zebra GK420d (once)

The GK420d is a **direct thermal** printer: use direct-thermal 35 x 20 mm labels with a gap between them, and no ribbon.

### 1. Load the labels
1. Open the lid (press the two release tabs on the sides) and slide the roll holders apart.
2. Put the roll in with labels coming off the **top** of the roll, printed side up, and let the holders close on it.
3. Pull the labels over the platen roller at the front, under the guides. Slide the guides in until they just touch the label edges.
4. Close the lid until it clicks. Press **Feed** once: one label should come out and stop.

### 2. Install the Zebra driver
1. Plug the printer into the PC by USB and turn it on.
2. Download **ZDesigner** from zebra.com (support page for the GK420d > Drivers) and run it. Choose **Install Printer** > the **GK420d (203 dpi)** model > the USB port it found.
3. Windows now lists *ZDesigner GK420d*. Open its **Printer properties** > **Ports** tab and note the ticked port (usually `USB001`).

### 3. Print through a Generic / Text Only printer
The page sends labels as plain ZPL text. A *Generic / Text Only* printer passes that text to the Zebra unchanged, so nothing is
rendered into images and no extra software or scripts are involved. On a company PC, adding a printer may need IT.

1. Windows Settings > **Bluetooth & devices** > **Printers & scanners** > **Add device**, then **Add manually**
   (or *"The printer that I want isn't listed"*).
2. Choose **Add a local printer or network printer with manual settings** > **Next**.
3. **Use an existing port**: pick the same port as the ZDesigner printer (for example `USB001`) > **Next**.
4. Manufacturer **Generic**, printer **Generic / Text Only** > **Next**. If asked, keep the driver that's already installed.
5. Name it **Zebra ZPL** > **Next**. Don't share it. Don't print a Windows test page (it would print as garbage).
6. Open *Zebra ZPL* > **Printing preferences** > **Advanced**, and set Paper Size to **Continuous - No Page Break** if it's listed.

Check it: download [`v175_TEST_label.zpl`](v175_TEST_label.zpl), open it in Notepad, and print it to **Zebra ZPL**. One label should print.

### 4. Set the label size and calibrate
On the page, click **Add printer setup**, then **Print batch** to **Zebra ZPL**. The setup step:
- sets gap-sensing media, direct thermal, 280 x 160 dots (35 x 20 mm), print speed 3 in/s
- saves those settings in the printer
- runs a media calibration (`~JC`): a few blank labels feed out, then it stops at the start of a label

Or by hand: with the printer on, **hold Feed until the light flashes twice**, then let go. Holding until **one** flash prints a configuration report.

Redo the calibration whenever you load a different label roll, or if a blank label comes out between printed ones.

### Troubleshooting
| Problem | Fix |
|---|---|
| Blank label between real ones, or printing runs across the gap | Calibrate (**Add printer setup** + print, or hold Feed for two flashes) |
| Labels print the ZPL code as text, or print a date/URL | In the browser print window: printer **Zebra ZPL**, **Headers and footers** off, **Margins** None |
| Garbled or missing labels from the browser | Use **Download .zpl**, open the file in Notepad, and print it to **Zebra ZPL** |
| Print is too light or patchy | Wrong label type (needs direct thermal), or raise darkness in ZDesigner: Printing Preferences > Options > Darkness |
| Text or barcode cut off on one side | The roll guides are too loose. Slide them against the labels and calibrate |
| Status light red or flashing | Lid not fully closed, or out of labels |

## Printing a batch

1. Scan parts. Each scan adds its label to the **Batch** list. Change copies or remove rows there. The batch is kept if you reload the page.
2. Click **Print batch** (or type `P`). The browser's print window opens with the batch as ZPL text.
3. Choose **Zebra ZPL**. Under **More settings**, turn off **Headers and footers** and set **Margins** to None (the browser remembers this).
4. Click **Print**. When it's done, the page asks whether every label printed. Click **Yes** to clear the batch.

**Download .zpl** saves the same batch as a file, as a backup way to print it (Notepad > Print > Zebra ZPL).

### First time: test label
Click **Add test label** (or type `T`), print the batch, then scan the label. The page should show bin **D-A1-1**.

## Scanning

| Scan or type | Result |
|---|---|
| `18000376` | Shows the bin, adds 1 label to the batch |
| `CNC41931WD` (a serial) | Shows the bin, adds that unit's label with its serial |
| `18000376\|1` (one of these labels) | Reads the material number back out |
| `5*18000376` | Adds 5 labels |
| A serialized material with several units | Lists the serials. Tap one, or scan the serial |
| Unknown code | Beep, a CHECK ME label goes in the batch, and the code is listed under **Unknown codes**. Type the material # next to it and that code works from then on (saved in this browser) |

## Label (35 x 20 mm, 280 x 160 dots)

Data Matrix ECC 200, forced 18x18, 7 dots per module at `^FO12,17`, payload `<material>|1`.
Text to the right at x=146: bin code, maker, model (serial labels: `SN ...`, or the serial alone if it's too long to fit with the prefix; serials are never shortened), part type, description (wraps to 2 lines), material number.

## Moving a part or adding a new one

Edit `layout/v175_bins.csv` on GitHub (`Material,Bin,Maker,Model,Group`). Parts in the inventory that aren't in this file
show as **NO BIN**, and the page counts them after you load your files.
