# Router Log Analyzer

## Purpose
A single-file, client-side web application for parsing and analyzing AT command diagnostic logs from LTE routers and modems. Produces structured summaries, detects command failures, and enables navigation to specific failure lines in the original log.

## Deployment
- Type: static HTML, no backend, no dependencies
- File: `index.html`
- Hosting: any static host (GitHub Pages, Netlify, local file system)
- URL pattern: `https://<username>.github.io/<repo-name>/`

---

## Input

### Accepted formats
- File upload: `.txt`, `.log`, any plain text file
- Direct paste: raw text in the browser textarea

### Log format specification
Each line follows this pattern:
```
<YYYY-MM-DD HH:MM:SS.mmm>: <CHANNEL>_Q: <AT_COMMAND>
<YYYY-MM-DD HH:MM:SS.mmm>: <CHANNEL>_A: <RESPONSE>
```

- `_Q` = query (command sent to modem)
- `_A` = answer (modem response)
- `CHANNEL` = any string, e.g. `4G_MAIN`

### Example input
```
2026-04-16 02:09:36.381: 4G_MAIN_Q: AT+CEREG?
2026-04-16 02:09:36.605: 4G_MAIN_A: +CEREG: 1,1

2026-04-16 02:09:36.605: 4G_MAIN_Q: AT+CSQ
2026-04-16 02:09:36.830: 4G_MAIN_A: +CSQ: 20,99
```

---

## Output

### Session overview cards
| Field | Source command | Good condition |
|---|---|---|
| Overall status | derived | zero failures across all commands |
| LTE registration | `AT+CEREG?` | stat = 1 or 5 |
| PDP context | `AT+CGACT?` | context 1 active |
| Network type | `AT+QNWINFO` | present |
| Band | `AT+QNWINFO` | present |
| Cell state | `AT+QENG="servingcell"` | CONNECT or NOCONN (idle) |

### Signal quality cards
| Metric | Unit | Source |
|---|---|---|
| Avg RSRP | dBm | `AT+QENG="servingcell"` |
| Min RSRP | dBm | `AT+QENG="servingcell"` |
| Avg RSRQ | dB | `AT+QENG="servingcell"` |
| Avg SINR | dB | `AT+QENG="servingcell"` |
| CSQ avg | 0–31 | `AT+CSQ` |

### Event timeline
One row per AT command type. Columns: status dot, command string, last good response, total run count, failure count.
- Green dot: all executions passed
- Red dot: one or more executions failed
- Clicking a red-dot row activates failure navigation for that command

### Failure navigation
Activated by clicking a red-dot row in the event timeline.
- Displays: command name, current failure index (e.g. `fail 2 / 5`)
- ↑ button: jump to previous failure occurrence
- ↓ button: jump to next failure occurrence
- ✕ button: clear selection
- Each navigation scrolls the raw log viewer to the matching line and flashes it

### Raw log viewer
- Collapsible panel at bottom of page
- Color coding: query lines = bright, response lines = dim blue, other = muted
- Clicking any timeline row scrolls to the corresponding timestamp in this panel

---

## Command evaluation rules

| Command | Label | Pass condition | Skipped |
|---|---|---|---|
| `AT+CEREG?` | LTE registration | `+CEREG: n,1` or `+CEREG: n,5` | No |
| `AT+CSQ` | Signal strength | CSQ value ≠ 0 and ≠ 99 | No |
| `AT+CGACT?` | PDP context | `+CGACT: 1,1` present | No |
| `AT+CGPADDR=1` | IP address | address string present in response | No |
| `AT+QENG="servingcell"` | Serving cell | response matches expected field pattern | No |
| `AT+QENG="neighbourcell"` | Neighbour cells | at least one `+QENG: "neighbourcell"` line | No |
| `AT+QNWINFO` | Network info | response matches operator/band/ARFCN pattern | No |
| `AT+QNETDEVSTATUS?` | Net device status | field 2 of response = 1 | No |
| `AT+CREG?` | 2G registration | — | Yes — stat=0 expected on LTE-only devices |
| `AT+C5GREG?` | 5G registration | — | Yes — stat=0 expected when no NR coverage |

A command execution also fails if the modem responds with `ERROR` or returns no parseable response.

---

## Architecture

| Property | Value |
|---|---|
| File count | 1 (`index.html`) |
| Languages | HTML, CSS, vanilla JavaScript |
| Dependencies | none |
| Build step | none |
| Data handling | all processing in-browser, no network requests, no data stored |
| Browser support | any modern browser (Chrome, Firefox, Safari, Edge) |

---

## Tested with
- Quectel EC21 / EC25 AT command logs
- Any modem log using `_Q:` / `_A:` timestamp pattern
