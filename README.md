# PQC_scanner
This is a python based CLI tool that enumerates the encryption standards on your site domains to enable visibility into where you are vulnerable to PQC Algorithims. Simply create a CSV file of your target domains and run this software via your CLI python pqcscan.py scan --input targets.csv --out ./results. Prior to running your scan ensure pip install cryptography is enabled and installed. Do NOT use this tool on aunthorized domains as you are subject to criminal offense.

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
