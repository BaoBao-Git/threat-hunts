# Threat Hunts

Hands-on threat hunting writeups from the SancLogic Cyber Range. Each hunt reconstructs an intrusion from raw telemetry with KQL in Microsoft Sentinel, and ends with detections and recommendations.

| Hunt | Scenario | Stack | Writeup |
|---|---|---|---|
| [Harborlight](harborlight/) | Akira-style double-extortion ransomware at a small dental and insurance practice, phishing to encryption across three hosts | Sysmon, Windows Security, Sentinel / KQL | [Interactive writeup](harborlight/index.html) · [PDF](harborlight/report/Harborlight_Threat_Hunt_Report.pdf) · [Queries](harborlight/queries/harborlight.kql) · [Detections](harborlight/detections/) |

<!-- Add JadePuffer, Meridian and Tideglass here as rows when they are moved into this repo. -->

## About

I'm Ritchy, working toward a US cybersecurity role. These writeups show how I read telemetry, separate attacker activity from background noise, and turn findings into detections. Each one keeps its corrections: where my first answer was wrong or only partly right, I say so and show the fix.

## Conventions

- All times are UTC. Hosts, accounts and credentials in these labs are fictional training-range values.
- Every hunt follows the same layout: `README.md`, `index.html`, `report/`, `queries/`, `detections/`, `evidence/`.
- Only my own analysis, queries and screenshots of my own query results are published. Platform briefs and answer text from the range are not reproduced.
- AI assistance is disclosed per hunt. I use it for query formulation, CSV triage and wording; reading the telemetry and the noise-versus-attack judgments are mine.

## Publishing with GitHub Pages

Settings → Pages → Deploy from branch → `main` / `(root)`. The Harborlight writeup then lives at `/harborlight/`.

## License

MIT, see [LICENSE](LICENSE).
