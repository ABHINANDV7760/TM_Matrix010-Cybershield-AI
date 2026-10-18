# CyberShield AI - <TM_Matrix010-cybershield-AI>
**IBM Z Datathon 2026 - "AI Secured: Innovation Without Exposure"**

Privacy-preserving phishing detection. Personal data is masked **before** the model sees it, every verdict comes with reasons, and a ready-to-share incident report is generated.

## How it works
`input -> privacy.redact -> model.predict -> report.make_report -> dashboard`

| Module | File | What it does |
|---|---|---|
| Privacy layer | `src/privacy.py` | Masks emails, phones, IPs, Aadhaar, PAN, cards, IDs, names; keeps URLs for the model |
| Theme | `src/theme.py`, `.streamlit/config.toml` | Shared dark neon styling (pure CSS, works offline) |
| Phishing model | `src/model.py` | TF-IDF char n-grams + logistic regression, with explanations |
| Incident report | `src/report.py` | Verdict, risk level, reasons, privacy actions, next steps |
| Dashboard | `src/app.py` (Streamlit), `src/dashboard_jupyter.py` + `CyberShield_Dashboard.ipynb` (Jupyter, used on LinuxONE) | Raw vs masked view, verdict, reasons, report |

## Repo layout
```
src/   privacy.py  model.py  train_model.py  report.py  app.py  (+ tests)
data/  urls.csv (not committed, see Data section)
docs/  portal / terminal / Jupyter screenshots
```

## Run (from the repo root)
**Laptop (Streamlit dashboard):**
```bash
pip install -r requirements.txt
python src/train_model.py          # needs data/urls.csv (columns: url,label); writes model.joblib + metrics.json
streamlit run src/app.py
```
**LinuxONE server (Jupyter dashboard).** Streamlit cannot be installed on the s390x lab container (its `libcst` dependency fails to build), so the server uses a Jupyter version with the same pipeline:
1. Upload `cybershield-repo.zip` in Jupyter, then in a Jupyter terminal: `unzip cybershield-repo.zip -d ~` and `cd ~/cybershield-repo`.
2. Put the dataset at `data/urls.csv` and run `python src/train_model.py`.
3. Open `CyberShield_Dashboard.ipynb`, run the cells top to bottom.

Quick tests: `python src/test_privacy.py` and `python src/eval_privacy.py` (run from `src/`).

## Data and provenance
- **Phishing Site URLs** (Kaggle dataset by Tarun Tiwari, https://www.kaggle.com/datasets/taruntiwarihp/phishing-site-urls).
  License: **Database Contents License (DbCL) v1.0** ([text](https://opendatacommons.org/licenses/dbcl/dbcl-10.txt)). It permits commercial use and requires compliance with the ODbL. We credit the source and do not redistribute the raw dataset in this repository (`data/` is git-ignored).
- **Synthetic** emails, logs and privacy test records with fictional identifiers (our own).
- We use public research data and synthetic data only. No real passwords or personal information were collected.

## Results
Trained on 200,000 sampled URLs (from 507,192 unique), tested on 37,331 held-out URLs. The split is grouped by website host, so no site appears in both train and test (this avoids inflated scores).

| Class | Precision | Recall | F1 |
|---|---|---|---|
| Phishing | 0.92 | 0.94 | 0.93 |
| Safe | 0.98 | 0.97 | 0.98 |

Overall accuracy 96%. Trained and evaluated on the IBM LinuxONE server. Confusion matrix (TN, FP, FN, TP): 27,138 / 757 / 586 / 8,850. About 6% of phishing links are missed (586 of 9,436) and about 3% of safe links are wrongly flagged (757 of 27,895). We favour recall on phishing, because a missed phishing link costs more than a false alarm.

Privacy layer: caught 57 of 58 sensitive items on our own 52-case synthetic test set (1.7% missed, no over-masking). That set is small and written by us, so treat it as indicative.

**Model details worth knowing:** dataset URLs have no `http(s)://`, so we strip the scheme before scoring. A small explicit trusted-site list (e.g. google.com, hdfcbank.com; shared-hosting hosts excluded) overrides the model for well-known domains to reduce false alarms. The dashboard says when this applies.

## Limitations
- URL/text lexical features only; no live lookups (domain age, reputation). The model can flag short links and unfamiliar legitimate sites.
- The trusted-site list is hand-written and small; a real deployment would use a maintained reputation feed.
- The dataset is a 2010s-era research set; new phishing styles may be missed.
- Regex privacy layer: misses names without a greeting/honorific and obfuscated values.
- Demo runs over plain http: fictional data only.
- Privacy layer is a prototype: it caught 98% of sensitive items on our standard test set (57 of 58), but on a harder set of 25 new cases (20 written adversarially with an AI assistant, 5 by a teammate) it missed 30.4% (7 of 23 items; 95% interval 15.6% to 50.9%). Misses: phone numbers with spaces or dots, UPI IDs, lowercase PAN, employee codes, dates of birth, and names without a greeting. It also wrongly masked a version number (v2.4.1.7) as an IP address.

## Future work
Network-log analyzer (e.g. CIC-IDS2017 sample), generative incident narratives with the Granite model.

## IBM Z / LinuxONE
Runs on a LinuxONE Community Cloud instance (Ubuntu 24.04, s390x). See `docs/` for portal, terminal and Jupyter screenshots.
