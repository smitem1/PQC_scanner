# PQC_scanner
This is a Python-based CLI tool that inventories the TLS encryption posture of specified domains and services to support post-quantum cryptography (PQC) readiness and crypto-agility planning.

The tool performs standard TLS handshakes to collect certificate and cryptographic metadata (e.g., TLS version, public-key algorithm, certificate validity) and applies a proxy-based risk model to help prioritize which endpoints may require attention as organizations plan for a future PQC transition.

To use the tool, edit the targets file  CSV file and contain authorized target domains or IP addresses under the host column and run the scan from the command line. The output includes structured results (CSV/JSON) and a human-readable summary for analysis and reporting.

====> HOW TO RUN..
download pqcmvp as name: pqcscan1.0.py file  and targets file as name:  targets.csv file into one folder > name folder pqcscan_mvp > enter your target hosts under the hosts column in the targets.csv > save csv>   right click on folder open in CLI > enter  python -m pip install cryptography / if that does work try py -m pip install cryptography> then run  python pqcscan1.0.py scan --input targets.csv --out output in your CLI> check your pqcscan_mvp folder your inventory is now available =) 




## Risk Scoring Methodology (PQC Prioritization)

This tool does **not** label assets as “quantum vulnerable” in a CVE sense. Instead, it prioritizes cryptographic assets for **post-quantum cryptography (PQC) migration planning** using an explainable model aligned with common industry guidance around **Harvest-Now, Decrypt-Later (HNDL)** risk and migration complexity.

### Why “PQC Risk” is different from traditional vulnerability scoring
Most public-key cryptography used today (e.g., RSA and ECC) is believed to be breakable by sufficiently capable quantum computers (via Shor’s algorithm). Therefore, the practical question is not “is it breakable?” but:

**If encrypted traffic or signed artifacts were collected today, which assets would produce the highest impact if decrypted/forged in the future, and which assets will be hardest to migrate?**

### Scoring model
The scanner computes a 0–100 priority score based on four explainable subscores:

- **Data Lifetime Risk:** proxy for long-term sensitivity using business criticality and certificate role/usages.
- **Exposure / Harvestability:** proxy for HNDL exposure (internet-facing, TLS posture, long-lived certificates).
- **Crypto Breakability Signal:** recognizes RSA/ECC-family keys and legacy signature hygiene (visibility/legacy indicator).
- **Migration Complexity:** proxies operational difficulty (e.g., CA certificates, legacy TLS stacks, long validity).

Final priority blends these components:

`Priority = (DataLifetime × Exposure) + (Breakability × MigrationComplexity) + ExpiryUrgency`

The output includes subscores and human-readable reasons for each endpoint to support auditability and remediation planning.

### Important note
Weights and thresholds are intentionally conservative and intended to be tuned per environment, data sensitivity, and regulatory requirements.
## Allowed Targets & Scanning Policy

This tool performs **passive cryptographic posture inspection** by initiating a standard TLS handshake and analyzing the cryptographic metadata (e.g., certificates, protocol versions) that a server voluntarily presents.

### What this tool does
- Establishes a TCP connection to a specified host and port
- Performs a standard TLS handshake (no authentication)
- Collects publicly visible TLS and certificate metadata
- Does **not** send application payloads
- Does **not** decrypt traffic
- Does **not** bypass access controls

### Permitted targets
You may use this tool **only** against systems that meet one or more of the following criteria:

- Systems you own or operate
- Systems you have explicit authorization to test
- Public test infrastructure designed for TLS/security testing (e.g., BadSSL)
- Reserved example domains (e.g., example.com, example.org)
- Public internet services where basic TLS handshakes are normal and expected

### Prohibited use
You must **not** use this tool against:
- Systems without authorization
- Government, financial, or healthcare systems without permission
- Targets requiring authentication
- Any system where scanning would violate acceptable use policies or local law

### Ethical use
This project is intended for:
- Cryptographic inventory and visibility
- Post-quantum cryptography (PQC) migration planning
- Harvest-now-decrypt-later (HNDL) exposure assessment
- Security research and defensive analysis

Misuse of this tool is the responsibility of the operator.

### Disclaimer
The author assume **no liability** for misuse of this software.  
Users are responsible for ensuring all scans are legal, authorized, and ethical.

The scanner only performs a TLS handshake to collect certificate and crypto metadata on..
Public websites & APIs (HTTPS on 443, 8443, 9443)

Internal web services (behind VPN or on private networks)

Cloud endpoints (AWS, Azure, GCP services)

Load balancers & reverse proxies

IoT devices & gateways that expose TLS

MQTT brokers over TLS (e.g., port 8883)

Network appliances (firewalls, routers, management interfaces)

Internal servers with TLS-enabled services

Test / staging environments


