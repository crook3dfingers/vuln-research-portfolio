# Vulnerability research portfolio

Index of vulnerability-research campaigns by Lane Crawford / Tarmo
Technologies. Each project is its own repo (sortable, scannable,
selectively cloneable). All project repos carry the GitHub topic
`vuln-research` for portfolio-wide discovery:
**[github.com/topics/vuln-research](https://github.com/topics/vuln-research)**.

## Active / recent campaigns

| Campaign | Repo | Status | Headline finding(s) |
|---|---|---|---|
| **DDS middleware** (Fast-DDS, CycloneDDS, OpenDDS, RTI Connext) | [`vuln-research-dds`](https://github.com/crook3dfingers/vuln-research-dds) | 25+ findings, 2 demonstrated pre-auth wire→post-exploit chains, 1 wire→RIP-control proof on hardened builds | **Pre-auth wire→attacker-chosen code execution** on OpenDDS rtpsrelay (10/10 stock release-hardened, 2026-05-11); pre-auth network heap-OOB-write with wire-delivered RIP-control on CycloneDDS; pre-auth network OOB-READ on Fast-DDS; auth-plugin race UAFs on Fast-DDS + CycloneDDS |
| **GPSD + receiver protocols** (NMEA, AIS, u-blox, RTCM) | [`vuln-research-gpsd`](https://github.com/crook3dfingers/vuln-research-gpsd) | F-0002 multi-vector; ASLR-bypass + alt-vector branches | Pre-auth memory corruption with single-packet RF / USB / network delivery vectors |
| **TAK ecosystem** (TAK Server, ATAK CIV, WinTAK, iTAK) | [`vuln-research-tak`](https://github.com/crook3dfingers/vuln-research-tak) | F-0034 dual-axis chain runtime-confirmed end-to-end on both client and server; vendor disclosure pending | **Client axis:** pre-auth remote ASLR-bypass RCE in ATAK CIV, lab-confirmed end-to-end on x86 Android emulator. **Server axis:** federation mission-package XXE composes into root-CA private-key exfiltration, forged-admin authentication, CoreConfig disclosure, and arbitrary code execution on the deployment's database backend; the JVM-side plugin-loader primitive then yields in-process code execution on the takserver plugin-manager. Public-tag history walk shows the chain components have been continuously present in publicly-released TAK code over a ~5-year window. Runtime re-confirmation against the current public release (`TAK-Product-Center/atak-civ` 5.5.1.x) and ARM64 verification pending hardware. |
| **Samsung Shannon LTE baseband + Pixel 10 GSA Trusty TEE** | [`vuln-research-baseband-shannon`](https://github.com/crook3dfingers/vuln-research-baseband-shannon) | FirmWire emulation campaign + TEE audit | Pre-auth wireless RCE research |
| **dump1090 / readsb** (ADS-B receivers) | [`vuln-research-dump1090`](https://github.com/crook3dfingers/vuln-research-dump1090) | H-0046 + H-0047 RCE chain demos | RCE-class chain with ASan-confirmed primitives |
| **BlueZ** (Linux Bluetooth stack) | [`vuln-research-bluez`](https://github.com/crook3dfingers/vuln-research-bluez) | Scoped, scaffold-only | Pre-auth attack-surface audit pending |
| **MAVLink ground-control stations** (Mission Planner, QGroundControl, MAVLink C library) | [`vuln-research-mavlink-gcs`](https://github.com/crook3dfingers/vuln-research-mavlink-gcs) | Scoped, scaffold-only | Drone-link / GCS protocol surface audit pending |
| **NTRIP + RTKLib** (precise GNSS / RTK) | [`vuln-research-ntrip-rtklib`](https://github.com/crook3dfingers/vuln-research-ntrip-rtklib) | Scoped, scaffold-only | NTRIP server / caster + RTK receiver audit pending |
| **Civil avionics** (Honeywell Primus, Collins Pro Line, Garmin GTN/G1000, Thales TopFlight, ARINC 424 / AIXM / ADS-B 1090ES / CPDLC parsers) | _local-only `projects/civil-avionics/`_ | **Planning** — `NEXT-TARGET.md` scoping doc | FAA/EASA DO-178C-cert avionics firmware as parser-bug-class target. Static + emulated + Faraday-shielded research path. Authorization framing TBD before any dynamic work begins. |
| **Files.com** (HackerOne — managed file transfer) | _local-only `projects/files-com/`_ | **Scoped** — H1 bug-bounty, validation target for bsec + pentest-storm + pentest-scribe + govfuzz stack integration | On-Premise Agent + Desktop v4/v6 + CLI + REST API as unauth-RCE attack surface. MFT-category adjacent CVE history (MOVEit, GoAnywhere, Cleo). |
| **GitLab** (HackerOne — DevOps platform) | private `vuln-research-gitlab` | **Scoped** — H1 bug-bounty, open-scope CVSS-based rewards | Rails monorepo + Gitaly / gitlab-shell / gitlab-pages / gitlab-runner Go services + container registry + customers.gitlab.com. CVSS-Critical target classes: Banzai markdown / asset pipeline, Git RPC surface, import / export pipelines, CI/CD interpolation, registry manifest handling. |

## Conventions

Every project repo follows the same structure:

```
<repo>/
├── AUTHORIZATION.md            # scope, target inventory, threat model, disclosure timeline
├── README.md                   # quick-orient: targets, status, headline findings
├── evidence/                   # per-hypothesis writeups (one dir per H-NNNN)
├── submissions/                # disclosure-ready advisories + ELI5 docs
├── notes/                      # methodology notes, prior-art mapping, IDL audits
├── harnesses/                  # fuzz / instrumentation harness sources
├── scratch/                    # exploit-primitive repros + chain demos (sources only — binaries excluded by .gitignore)
└── corpus/                     # small fixture / test data
```

Hypothesis IDs (`H-NNNN`) and finding IDs (`F-NNNN`) are managed by
the `bsec` CLI from the bitwize-security plugin.

For the full campaign creation, scaffold, and portfolio update checklist, see
[`TRACKING.md`](TRACKING.md).

## Discovery

- **All projects:** [`https://github.com/topics/vuln-research`](https://github.com/topics/vuln-research)
- **By owner:** [`https://github.com/crook3dfingers?tab=repositories&q=vuln-research`](https://github.com/crook3dfingers?tab=repositories&q=vuln-research)

## Why repo-per-campaign rather than monorepo

Each campaign carries:

- Its own `AUTHORIZATION.md` and disclosure timeline
- Multi-GB of scratch / build artifacts (excluded from git but tracked locally)
- Distinct vendor relationships, target inventory, threat model

Repo-per-campaign lets each disclosure flip public independently,
keeps `git clone` bounded, and makes the portfolio scannable. The
shared `vuln-research` topic + this index repo provide the
"easily tied together" affordance.

## Methodology

Each campaign uses the bitwize-security plugin's hypothesis-driven
workflow:

1. `bsec project new <slug>` — scope + authorization
2. `bsec hyp new <slug> "<title>" --class <class> --priority P0..P4`
3. Drive each hypothesis through gates: HYPOTHESIS → SINK_CONFIRMED →
   SOURCE_CONFIRMED → PATH_VALIDATED → RUNTIME_CONFIRMED
4. `bsec finding promote H-NNNN` once RUNTIME_CONFIRMED → finding gets
   an F-NNNN identifier
5. Write advisory + ELI5; track in `submissions/`
