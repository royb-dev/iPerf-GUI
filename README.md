[README.md](https://github.com/user-attachments/files/32621146/README.md)
# SATCOM iPerf Test Console

SATCOM iPerf Test Console is a lightweight Windows desktop application for controlled SATCOM terminal and network-path testing. It provides a browser-based graphical interface for iPerf 2 and iPerf 3, live statistics, profile-aware charts, and locally stored reports.

This is the initial release of the application. It is intended for desktop installation and does not require licensing, activation, an account, or a separate iPerf installation.

The application will continue to mature as operational experience grows and additional capabilities are identified. Feedback, comments, improvement ideas, and field observations are welcomed and will help guide future development.

## Release

- Product: **SATCOM iPerf Test Console**
- Version: **1.0.0**
- Platform: **Windows 10/11 x64**
- Package type: **Windows installer build kit**
- Licensing: **Not required**
- Web interface: **Localhost only by default** (`127.0.0.1:8080`)

## Included components

- Bundled iPerf 2 executable for full-featured SATCOM traffic profiles
- Bundled iPerf 3.21 executable and required Windows runtime
- Standalone desktop application source and Nuitka build script
- Inno Setup installer definition
- Client, server, live-statistics, charts, reports, guide, and settings interfaces
- Windows firewall helper for the standard iPerf ports
- Local report storage with HTML, JSON, CSV, raw output, hashes, and ZIP export

Recipients of the finished Setup EXE do not need Python, iPerf, Winget, or administrator access for the normal per-user installation.

## Primary workflows

### Client testing

The Client tab supports three destination modes:

- **Public Server:** Uses a listed third-party iPerf 3 endpoint. This measures the complete end-to-end route, including the SATCOM segment, gateway, Internet path, and public-server conditions.
- **Ad Hoc:** Tests a manually entered hostname or IP address.
- **Planned:** Tests a saved peer associated with a configured test domain.

Select the engine, protocol, profile, direction, duration or transfer size, and applicable traffic parameters before selecting **Run client**.

### Server testing

Use the Server tab to start a managed local iPerf listener. The remote client must use the same iPerf generation:

- iPerf 2 normally uses port **5001**.
- iPerf 3 normally uses port **5201**.
- iPerf 2 and iPerf 3 are not interoperable.

### Live statistics and charts

During and after a run, the console displays throughput, transfer volume, packet loss, jitter, RTT/latency, packet or response rate, raw iPerf output, progress, and applicable charts.

**Clear live statistics** removes the displayed statistics, raw output, completed-run summary, and chart data from the interface. It does not delete the saved report.

### Reports

Completed runs are stored locally under the application `reports` folder. Available report artifacts can include:

- Standalone HTML report
- Summary JSON
- Interval metrics CSV
- Raw iPerf output
- Executed command and request data
- SHA-256 manifest
- ZIP report bundle

To permanently remove reports, select one or more report checkboxes and choose **Delete selected**. Confirming deletion removes the selected report folders and their saved files. Reports associated with an active test cannot be deleted.

## Network communications

| Direction | Purpose | Typical destination/port | Required condition |
| --- | --- | --- | --- |
| Local only | Browser GUI to local application | `127.0.0.1:8080` TCP | Available only on the local computer by default |
| Outbound | iPerf client test traffic | Remote test port; commonly TCP/UDP 5001 or 5201 | Remote endpoint must be reachable and running the matching engine |
| Inbound | Local iPerf server listener | Commonly TCP/UDP 5001 for iPerf 2 or 5201 for iPerf 3 | Windows firewall and intervening network devices must allow the selected port |
| Outbound | Public iPerf 3 test | Published server and port | Internet access and third-party endpoint availability |
| Outbound | Public-server directory refresh | HTTPS | Optional; the bundled cached directory remains available offline |

The application does not require licensing, activation, telemetry, or control-plane communications.

## Windows firewall

If this computer will operate as an iPerf server, allow the selected inbound test ports on the Windows Private network profile.

For the standard ports, right-click `satcom-iperf-web\allow-iperf-firewall.bat` and select **Run as administrator**.

For custom ports, run PowerShell as administrator:

```powershell
.\satcom-iperf-web\allow-iperf-firewall.ps1 -Ports 5001,5201,PORT
```

Coordinate any external firewall, router, VPN, or SATCOM terminal access-control changes separately.

## Building the Windows installer

### Build-machine requirements

1. Windows 10 or Windows 11 x64
2. Python 3.12 x64
3. Inno Setup 7
4. Internet access for first-time Nuitka/compiler dependency retrieval

The verified iPerf engines are already included in the build kit. Winget is not used.

### Build steps

1. Extract the entire ZIP into a new folder.
2. Open PowerShell in that extracted folder.
3. Run:

```powershell
Set-ExecutionPolicy -Scope Process Bypass
.\Build-SATCOM-iPerf-Light.ps1
```

The finished installer is written to:

```text
release\SATCOM-iPerf-Test-Console-Setup-1.0.0.exe
```

The builder validates Python, verifies the bundled engine hashes, creates the standalone GUI, restores the verified engine folders to the payload, performs a startup/shutdown health test, builds the installer, and prints its SHA-256 hash.

If Inno Setup is installed in a nonstandard location:

```powershell
.\Build-SATCOM-iPerf-Light.ps1 -InnoCompiler "C:\Path\To\ISCC.exe"
```

## Installing and distributing

Only the generated Setup EXE is required for recipient installation. Do not send the build folder, Python environment, Nuitka output, or separate iPerf executables.

The installer:

- Installs per user under `%LOCALAPPDATA%\Programs\SATCOM iPerf Test Console`
- Preserves writable configuration and report storage
- Creates Start Menu and optional desktop shortcuts
- Starts the GUI without a command window
- Keeps managed iPerf child processes hidden during tests

## Basic acceptance check

After installation:

1. Launch **SATCOM iPerf Test Console**.
2. Confirm the header reports both iPerf 2 and iPerf 3 as available.
3. Start and stop a local server listener.
4. Run a short client test against a compatible endpoint.
5. Confirm no command-prompt window appears during the run.
6. Confirm **Clear live statistics** resets the displayed run and charts.
7. Confirm report selection and **Delete selected** work after confirmation.
8. Select **Shutdown** and confirm the local console closes.

## Troubleshooting

### A command window appears during a test

Confirm the application was built from this complete package and that the generated Setup EXE was installed. If the issue continues, record the selected engine and test mode with a screenshot for troubleshooting.

### The peer cannot connect to this server

- Confirm both endpoints use the same iPerf generation.
- Confirm the server is listening on the expected port.
- Apply the Windows Private-profile firewall rule.
- Check SATCOM terminal, router, VPN, NAT, and upstream firewall policies.
- Verify that return traffic has a valid route.

### A public test fails or appears busy

Public servers are third-party resources and may be unavailable, rate-limited, protocol-restricted, or overloaded. Try another published endpoint or use controlled Ad Hoc/Planned endpoints for formal testing.

### The browser shows a stale interface

Select **Shutdown**, close the browser tab, reinstall the newly generated Setup EXE, and relaunch the application. A forced browser refresh (`Ctrl+F5`) may also clear cached static assets.

## Operational notes

- Test traffic can consume substantial bandwidth. Coordinate rates and test windows before using production SATCOM links.
- High-rate UDP testing can create packet loss or affect other services when the configured rate exceeds path capacity.
- Public-server results represent the complete route and do not isolate the satellite segment.
- Use controlled endpoints and documented link conditions for repeatable engineering or acceptance testing.
- The tool generates network measurements; it does not certify application performance by itself.

## Shutdown

Use the red **Shutdown** control in the application header. It stops managed test activity and closes the local backend cleanly.

## Additional documentation

- `WINDOWS-BUILD.md` — concise Windows packaging instructions
- `RELEASE-NOTES-v1.0.0.md` — release and maintenance changes
- `BINARY-PROVENANCE.txt` — bundled engine sources and hashes
- `satcom-iperf-web\VALIDATION.md` — validation record
- `satcom-iperf-web\DEPLOYMENT.md` — source-mode deployment guidance
