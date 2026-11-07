# Cross Hermit Save Editor

<div align="center">

[![Live Editor](https://img.shields.io/badge/Status-Online-success.svg)](https://freeze-man-1.github.io/CrossHermitSaveEditor/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Web%20Browser-orange.svg)]()

**The official, open-source web-based save file editor for *Cross Hermit*. Easily modify your save data, character stats, inventory items, and offsets directly in your browser with automatic checksum recalculation.**

[🚀 Launch the Live Editor](https://freeze-man-1.github.io/CrossHermitSaveEditor/)

</div>

---

## 🌟 Features

* **Web-Based & Instant:** No installation or downloads required—runs completely client-side in any modern web browser.
* **Automatic Checksum Recalculation:** Safely modifies save files without triggering game corruption errors by recalculating required binary checksums on-the-fly.
* **Deep Save Inspection:** View and edit hidden game variables, item inventories, character stats, and progression flags.
* **Open Source & Secure:** Your save data never leaves your device; all parsing and modifications happen locally using JavaScript.

---

## 🚀 How to Use

1. Head over to the live tool: **[Cross Hermit Save Editor](https://freeze-man-1.github.io/CrossHermitSaveEditor/)**
2. Click **Load Save File** and select your `Cross Hermit` save file from your local machine.
3. Modify your desired values (stats, inventory, items, currency) using the graphical interface.
4. Click **Download Patched Save** to export your updated file back into your game directory.

---

## 🛠️ Technical Details & Binary Structure

This project was built by reverse-engineering the internal binary format of *Cross Hermit* save files. 

* **Save Format:** Custom binary structure mapping specific offsets for player attributes and world progression.
* **Checksum Verification:** The editor parses the binary header, maps data offsets, and updates the validation checksum before export to ensure game compatibility.

If you encounter parsing errors with a specific game version, please open an [Issue](https://github.com/freeze-man-1/CrossHermitSaveEditor/issues) with a sample save file.

---

## 📦 Local Development

If you want to run or modify the source code locally:

```bash
# Clone the repository
git clone [https://github.com/freeze-man-1/CrossHermitSaveEditor.git](https://github.com/freeze-man-1/CrossHermitSaveEditor.git)

# Navigate to the project directory
cd CrossHermitSaveEditor

# Open index.html in your preferred browser or serve via a local static server
