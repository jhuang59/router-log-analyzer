# Router Log Analyzer — Free AT Command Log Parser for LTE Modems

A free, open-source, browser-based tool for parsing and analyzing AT command diagnostic logs from LTE routers and modems. No installation required. Works with Quectel EC21, EC25, and any modem producing standard timestamped AT command logs.

**Live demo:** `https://jhuang59.github.io/router-log-analyzer/`

---

## What problem does it solve

Engineers debugging LTE modem connectivity issues spend significant time manually scanning AT command log files to identify dropped registrations, failed PDP contexts, weak signal events, and abnormal command responses. This tool automates that process — paste or upload a log file and get a structured analysis in seconds.

**Keywords:** AT command log parser, LTE modem log analyzer, Quectel log analyzer, EC21 log parser, EC25 diagnostic tool, AT command debugger, modem diagnostics, LTE registration failure detection, PDP context monitor

---

## Features

- **Session overview** — LTE registration status, PDP context, network type, band, serving cell state
- **Signal quality summary** — average and minimum RSRP, RSRQ, SINR, CSQ across all poll cycles
- **Event timeline** — one row per AT command type, last good response, total run count, failure count
- **Failure navigation** — click any failing command to step through every bad response with ↑ ↓ buttons; jumps and highlights the exact line in the raw log
- **Raw log viewer** — collapsible, color-coded, scrollable original log with line-level jump targeting
- **Zero setup** — single HTML file, runs entirely in the browser, no data sent anywhere

---

## Quick start

1. Download `index.html`
2. Open it in any modern browser (Chrome, Firefox, Safari, Edge)
3. Upload a `.txt` / `.log` file or paste log text directly
4. Review the session overview, signal cards, and event timeline
5. Click any red-dot row to browse failures for that command

---

## Supported log format

Compatible with Quectel AT command logs and any modem log following this structure:

```
2026-04-16 02:09:36.381: 4G_MAIN_Q: AT+CEREG?
2026-04-16 02:09:36.605: 4G_MAIN_A: +CEREG: 1,1

2026-04-16 02:09:36.605: 4G_MAIN_Q: AT+CSQ
2026-04-16 02:09:36.830: 4G_MAIN_A: +CSQ: 20,99
```

Pattern: `<timestamp>: <CHANNEL>_Q: <command>` and `<CHANNEL>_A: <response>`

---

## Recognized AT commands

| Command | What is checked | Skipped |
|---|---|---|
| `AT+CEREG?` | LTE EPS registration — expects stat 1 (home) or 5 (roaming) | No |
| `AT+CSQ` | Signal strength — flags CSQ=0 or CSQ=99 (no signal/unknown) | No |
| `AT+CGACT?` | PDP context — expects context 1 active | No |
| `AT+CGPADDR=1` | IP address — expects a valid address assigned | No |
| `AT+QENG="servingcell"` | Serving cell info — RSRP, RSRQ, SINR, PCI, EARFCN | No |
| `AT+QENG="neighbourcell"` | Neighbour cells — expects at least one visible cell | No |
| `AT+QNWINFO` | Network info — operator, band, ARFCN | No |
| `AT+QNETDEVSTATUS?` | Device status — expects connection field = 1 | No |
| `AT+CREG?` | 2G circuit-switched registration | Yes — stat=0 normal on LTE-only |
| `AT+C5GREG?` | 5G NR registration | Yes — stat=0 normal without NR coverage |

---

## Tech stack

- Single HTML file — HTML, CSS, vanilla JavaScript
- No dependencies, no build step, no backend
- All processing runs client-side; no log data is transmitted

---

## Tested with

- Quectel EC21 series
- Quectel EC25 series
- Any modem log using `_Q:` / `_A:` timestamped AT command format

---

## Contributing

Pull requests welcome. Most useful contributions:
- Support for additional AT commands
- Support for other modem log timestamp formats
- Signal threshold tuning

---

## License

MIT License

Copyright (c) 2026 <your-name>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
