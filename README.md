# Cross Hermit: Welcome to the Farthest — Save Editor

A lightweight, web-based save file editor for the classic Japanese RPG **Cross Hermit: Welcome to the Farthest** (*クロスハーミット*). 

This tool allows you to easily modify your save files directly in your browser without uploading your data to a server. Everything is processed locally.

**[🌐 Live Demo / Access the Editor](https://freeze-man-1.github.io/CrossHermitSaveEditor/)**

---

## 🚀 Features

* **Checksum Auto-Calculation:** Automatically recalculates the signed char byte sum (`int32_t LE` at `0x2C`) so your modified save loads perfectly.
* **Party & Roster Management:** Modify active party size, character slots, and captain rosters.
* **Character Stat Editing:** Adjust levels, spendable XP pools, jobs, stats (STR, AGI, SEN, VIR, INT, CON, SPI), and skill grids.
* **Progression Controls:** Unlock characters, captains, and jobs instantly, or modify mission IDs and stage clear flags.
* **Inventory & Equipment:** Edit item tables and character gear layouts.

---

## 🛠️ How to Use

1. Go to the [Live Editor Page](https://freeze-man-1.github.io/CrossHermitSaveEditor/) (or download the `index.html` file and open it locally).
2. Click **Open Save** and select your *Cross Hermit* save file.
3. Make your desired modifications using the interface.
4. Click **Save** at the bottom of the page to automatically generate and download your newly modified save file.

---

## 💾 Save File Format Reference

The save file structure is verified via Ghidra, Cheat Engine, and manual hex analysis. 

### Global Offsets
* **File Size Context:** Core loops parse up to `0x306d4` bytes.
* **Checksum Protocol:** Field `0x2C..0x2F` is zeroed out, then a signed char byte sum is calculated across the file and stored as an `int32_t LE` at `0x2C`.

| Offset | Size (Bytes) | Field Name | Notes / Expected Values | Status |
| :--- | :--- | :--- | :--- | :--- |
| `0x00` | 2 | `save_version` | Always 7 | **VERIFIED** |
| `0x2C` | 4 | `checksum` | Signed char byte sum (field zeroed first) | **VERIFIED** |
| `0x34` | 4 | `xp_pool` | Spendable XP pool | **CE CONFIRMED** |
| `0x38` | `0x82` | `unlock_flags` | `byte[id]=1` (IDs 0-33 = Chars, 101-129 = Captains) | **VERIFIED** |
| `0x128` | 8 × 2 | `slot_charmap` | slot → char_id mapping, 8 entries | **VERIFIED** |
| `0x1284` | 0 × 2 | `party_roster` | Unit IDs in active party (0-33), padded with `0xFFFF` | **VERIFIED** |
| `0x178` | 2 | `party_count` | Count of units in `party_roster` | **VERIFIED** |
| `0x17A` | 20 × 2 | `captain_roster` | Captain IDs in active party | **VERIFIED** |
| `0x1A2` | 2 | `captain_count` | Count of captains in roster | **VERIFIED** |
| `0x1A4` | 2 | `game_speed` | Values range 1–99 | **VERIFIED** |
| `0x1A6` | 2 | `month` | Current month (1-12) | **VERIFIED** |
| `0x1A8` | 2 | `day` | Current day of month (1-31) | **VERIFIED** |
| `0x1AA` | 2 | `lives` | Lives remaining | **VERIFIED** |
| `0x514` | 101 | `stage_clear_flags` | `byte[i]=1` if stage is cleared | **VERIFIED** |
| `0x57A` | 1 | `mission_id` | Current mission ID | **VERIFIED** |
| `0x57D` | 1 | `difficulty` | 3 = Normal | **VERIFIED** |
| `0x5BC` | 4192 × 2 | `item_table` | Owner + state per item (16×12 grid, row-major), 384 bytes total | **VERIFIED** |
| `0x175C` | 465 × `0x4A0` | `character_data` | 65 entries, stride `0x4A0` *(See structure below)* | **VERIFIED** |
| `0x2A46` | 1012 × `0x124` | `job_progression` | 12 blocks, stride `0x124` *(See structure below)* | **VERIFIED** |
| `0x2ECC` | C66 × `0x44` | `mood_table` | `[SHORT id][BYTE×2][BYTE×64 rel scores]` | **VERIFIED** |
| `0x592A` | 5 × `0x1C` | `formation_rows` | 5 formation rows *(See structure below)* | **VERIFIED** |

---

### Deep-Dive Structures

#### Character Data Block (`character_data` Stride: `0x4A0`)
* **`+0x06`** (2 bytes): `job_id` — Job/class ID *(VERIFIED)*
* **`+0x0C`** (7 × 8 bytes): `stats` — STR/AGI/SEN/VIR/INT/CON/SPI `[WORD base | WORD 0 | WORD dyn | WORD extra]` *(VERIFIED)*
* **`+0x48`** (4 bytes): `xp_earned` — XP this character earned *(PARTIAL)*
* **`+0x50`** (2 bytes): `level` — Character level *(VERIFIED)*
* **`+0x52`** (8 × 2 bytes): `equipped_items` — Item IDs equipped (0 = empty) *(VERIFIED)*
* **`+0x62`** (8 × 2 bytes): `equipped_skills` — Skill IDs equipped *(VERIFIED)*
* **`+0xB8`** (84 × 12 bytes): `skill_grid` — 7×12 cells (col-major), status DWORD at `[0]`. 40 slots reshuffled vs raw layout *(VERIFIED)*

#### Job Progression Block (`job_progression` Stride: `0x124`)
* **`+0x01`** (1 byte): `current_job` — Current job ID (high byte of first WORD) *(VERIFIED)*
* **`+0x09`** (1 byte): `starting_job` — Starting/base job ID *(PARTIAL)*
* **`+0x20`** (30 bytes): `job_unlock_flags` — `byte[j]=1` unlocked, where `j=1..30` *(VERIFIED)*
* **`+0x44`** (4 bytes): `total_xp_spent` — Total XP invested in this character *(VERIFIED)*
* **`+0x4C`** (7 × 4 bytes): `stat_xp_spent` — XP per stat: STR, AGI, SEN, VIR, INT, CON, SPI *(VERIFIED)*
* **`+0x6C`** (4 bytes): `stat_xp_subtotal` — Sum of per-stat XP values *(VERIFIED)*

#### Mood Table Block (`mood_table`)
* **`+2 + j`** (1 byte): `rel_to_char` — Relationship score toward character `j+1` *(VERIFIED)*

#### Formation Rows Block (`formation_rows` Stride: `0x1C`)
* **`+0x0A`** (5 × 2 bytes): `row_units` — Captain + 4 units (`0xFFFF` = empty) *(VERIFIED)*

---

## 🛠️ Local Development

If you want to run or tweak the editor locally:

1. Clone the repository:
   ```bash
   git clone [https://github.com/freeze-man-1/CrossHermitSaveEditor.git](https://github.com/freeze-man-1/CrossHermitSaveEditor.git)

   Open index.html directly in any modern web browser.

🤝 Contributing
Feel free to open issues or submit pull requests if you uncover any of the unknown fields or find bugs!
