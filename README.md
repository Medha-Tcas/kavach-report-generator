# Kavach Report Generator

A single-page web tool that turns raw Kavach log files (Excel / CSV) into ready-to-use reports. Everything runs in the browser: files are processed on your own computer and are not uploaded to any server.

**Live tool:** https://medha-tcas.github.io/kavach-report-generator/

> For internal team use only. Please do not share the link outside the team.

---

## Quick start

1. Open the live link.
2. Select the report type.
3. Upload the input file(s) (`.xlsx`, `.xls` or `.csv`). All worksheets are scanned automatically.
4. Click **Generate Report**.
5. Download the output with **Excel**, **CSV** or **Print**. Use **Save** to keep the report in the session history.

If the page looks outdated after an update, refresh with `Ctrl + F5`.

---

## Reports and required columns

| Report | Required input columns | Notes |
|---|---|---|
| **RFID Tag Miss** | Transma file: Date, Station, Loco Id, Direction, RFID, Tag Link Info, TIN, Abs Loc | Optional `Tag_Linking_data` file (Nxt RFID sequence) adds the *Actual Tag* column for BothTagMissing. Without it the column is hidden. |
| **Station Health** | Date, Time, Station, Event Name, Event Info | Power, temperature and PA current per radio. Missing hourly values are forward-filled. |
| **Station RX** | Date, Station, Event Name, Event Info | Compares Radio-1 vs Radio-2 and flags defective radios by packet loss. |
| **Loco RX** | Date, Traffic Dir, Cur Station ID, Loco ID, RadRxPktCnt, RadID | SR1LR1 to SR2LR2 loss. |
| **GPRS Faults** | Date, Time, Station, Module, Fault Type, Fault Msg | Active unresolved faults, plus a Fault Msg Count view. |
| **OP Summary** | Date, Time, Station, Loco Id, Direction, Mode, Brake, Abs Loc, Speed, RFID, TIN | Mission availability, mode transitions and failures. |

---

## Station Health

Three views:

- **All Hours**: every hour in the file.
- **Latest Hour**: one row per station for the latest hour. Missing values are backfilled from an earlier hour (up to 3 hours back). **Current Running Key** and **Current Running Key Date** are backfilled from whenever they were last logged, even if that was days earlier.
- **Day Report**: day summary with max / min Fwd/Rev per radio.

### Excel output (Station Health)

- Yellow merged title, for example `Station Health Report (09:00 AM) 2026-10-01`, and a dark blue header row.
- Latest Hour layout: Station, Radio1 / Radio2 Rev/Fwd Power, Temperature, PA Temperature, Tx PA Current, Current Running Key and Key Date.
- Cell colours:
  - **Yellow**: Rev/Fwd, Temperature or PA Temperature cell is blank (no data), or Rev is *Fair*.
  - **Pink**: Fwd above the High Fwd limit, Fwd below 10.0, Rev *Bad*, or Temperature above the limit.
- Rev power rating (limits are set in Settings):

| Rev power | Rating | Excel colour |
|---|---|---|
| up to 0.5 | Good | none |
| above 0.5 up to 0.8 | Fair | yellow |
| above 0.8 | Bad | pink |

---

## GPRS Faults

Two views:

- **Active Faults**: latest unresolved fault per Station, Module and Fault Msg (Date, Time, Station, Module, Fault Type, Fault Msg).
- **Fault Msg Count**: how many times each fault message was logged per Station and Module.
  - Columns: Date, Time, Station, Module, Fault Msg, Count, **Fault Code**. Date and Time are the last occurrence.
  - **Min count** box (default 2) hides faults that were logged fewer times.
  - **Count Col: ON/OFF** button shows or hides the Count column.
  - **Fault Code** values:

| Value | Meaning |
|---|---|
| Active | Still active, count within the limit |
| Recovered | Recovered, logged once |
| Recovered & continuously logged | Recovered, logged more than once, count within the limit |
| Repeatedly Logging | Recovered, count **more than** the limit (default 8) |
| Repeatedly Logging(Active) | Still active and count more than the limit |

  - Repeatedly Logging cells are coloured by count in the screen table and in Excel: light yellow above the limit, orange above 2x the limit, red above 4x the limit.

The Excel output uses the same title and header style as Station Health.

---

## Settings

Open the **Settings** tab to change the thresholds. Values are applied when you click **Save Settings**.

| Setting | Default |
|---|---|
| RFID "Tag Issue" threshold (unique locos) | 3 |
| RFID "Loco Reader Issue" threshold (tags per loco) | 5 |
| Station Health temperature threshold (C) | 60 |
| Station RX packet loss threshold (%) | 10 |
| GPRS "Repeatedly Logging" count limit | 8 |
| Station Health High Fwd power limit | 10.5 |
| Station Health Rev power limits (Good up to / Fair up to) | 0.5 / 0.8 |

Station profile code and engineer / inspector name can also be set there.

---

## Deploying updates (GitHub Pages)

The whole tool is one file, `index.html`.

1. Open the repository `kavach-report-generator` on GitHub.
2. Replace `index.html` (open the file and use the pencil icon, or use **Add file > Upload files**).
3. Commit the change.
4. Wait about a minute, then refresh the live page with `Ctrl + F5`.

---

## Technical notes

- Plain HTML, CSS and JavaScript in a single file, with no build step.
- Libraries loaded from cdnjs: **SheetJS** (reading Excel / CSV) and **ExcelJS** (styled Excel output). An internet connection is needed to load them.
- Styled Excel export is used for Station Health and GPRS Faults. Other reports use the standard export.
- Works best in current Chrome or Edge.

---

## Change log

- Station Health, Latest Hour: Current Running Key and Key Date now use the last updated value, however old.
- GPRS Faults: new Fault Msg Count view with Repeatedly Logging rules, count colours, Count column toggle and configurable limit.
- Excel output for Station Health and GPRS Faults redesigned (title, header, borders, colour rules).
- New Settings: High Fwd power limit, Rev power limits (Good / Fair / Bad), GPRS repeated-fault count limit.
- New Kavach logo in the header and browser tab.

---

## Support

Report any issue or mismatch to the tool owner.
