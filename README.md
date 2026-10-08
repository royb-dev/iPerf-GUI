[README.md](https://github.com/user-attachments/files/33216318/README.md)
# Link Performance Test Console

Link Performance Test Console is a standalone Windows application for repeatable TCP and UDP measurement across terrestrial, wireless, cloud, tactical, and SATCOM paths. It includes iPerf 2 and iPerf 3 workflows, live monitoring, public-server recovery, saved reports, interface discovery, media profiles, and editable engineering thresholds.

The installed application runs locally and requires no account, device registration, or online access service. Internet access is used only when the operator refreshes the public iPerf 3 server directory or runs a test against a public destination.

## Highlights

- Bundled iPerf 2 and iPerf 3 engines in the Windows installer
- Client and server workflows for TCP and UDP
- Public-server filters, ISO 3166-1 country preferences, and duplicate-safe country lists
- Explicit public-server recovery actions: **Try Next Server** and **Choose Another Server**
- Startup and run watchdogs that distinguish busy, unavailable, and timed-out endpoints
- Live raw output, KPIs, charts, progress states, and run review
- JSON, CSV, raw, HTML, manifest, and ZIP reports
- Local configuration, discovery, and single-instance lifecycle management

## Windows build

Requirements:

- Windows x64
- Python 3.12 available as `py -3.12`
- Inno Setup 7

Run:

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
.\build-windows.ps1
```

The builder validates product hygiene, fetches the pinned Windows iPerf 3 payload, verifies its SHA-256, builds the console-free executable with Nuitka, and creates:

```text
release\Link-Performance-Test-Console-Setup-1.0.0.exe
```

## Source start

On Windows, double-click `Start Link Performance Test Console.cmd`. On Linux or macOS, run `./start.sh`. The local interface opens at `http://127.0.0.1:8080`.

## Validation

```bash
python3 PRODUCT-HYGIENE.py
python3 -m unittest discover -s tests -v
```

## Security

The local web interface has no built-in login or TLS. Keep it on localhost or a trusted management network. Only test networks and endpoints you are authorized to use.
