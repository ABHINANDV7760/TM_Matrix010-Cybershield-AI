# CyberShield AI: 3-minute video script (target 2:45, hard limit 3:00)

**Before recording:** run `CyberShield_Dashboard.ipynb` in Jupyter on the LinuxONE server (the result footer must say "Model: trained model"). Hide the Jupyter password/token and the browser bookmarks. Use only the fictional sample buttons. Record once as a rehearsal and time it.

| Time | Screen | Narration  |
|---|---|---|
| 0:00-0:25 **Problem** | Title slide: "CyberShield AI: <TM_Matrix010-cybershield-AI>" | "Small organizations get phishing messages every day, but the tools that catch them often want you to upload emails and logs full of personal data. Names, phone numbers, IPs, account IDs. Protecting people shouldn't mean exposing them. That's our theme: innovation without exposure." |
| 0:25-0:45 **Data** | README "Data and provenance" section | "We trained on a public labeled phishing-URL dataset, about 507,000 unique URLs, roughly 77% safe and 23% phishing. We tested our privacy layer on synthetic records with fictional identifiers. No real personal data was used anywhere." |
| 0:45-1:00 **Solution** | Simple diagram: input, privacy layer, model, report | "CyberShield works in four steps. First it masks personal data. Then the model sees only the masked text. It returns a verdict with reasons. Finally it writes an incident report." |
| 1:00-2:10 **Live demo** | Dashboard: choose "Phishing email (synthetic)" in the dropdown, press Analyze | "Here's a fictional phishing email with a name, phone number, email, IP and user ID. On the left is the raw input. On the right is what the model actually sees: every personal detail is masked, but the link is kept so we can still judge it. The verdict: phishing, with <score>% confidence. And here is why: the link imitates a login page, uses a suspicious domain pattern, and asks the user to verify. Below, the incident report: risk level, what was masked, and what to do next. Now a safe message..." *(choose "Safe message", Analyze, 10 seconds)* "...marked safe, personal data still masked." |
| 2:10-2:30 **IBM Z / LinuxONE** | Jupyter terminal showing `uname -m` (s390x), then the dashboard notebook, then the portal with ACTIVE server, then Jupyter | "All of this runs on an IBM LinuxONE instance: Ubuntu on s390x, the training happened in Jupyter on the same server, and the dashboard notebook runs on it. Here is the portal showing the server active." |
| 2:30-2:45 **Results + future** | Metrics slide | "On held-out data the model reaches 0.92 precision and 0.94 recall on phishing, trained right here on the LinuxONE server. We optimize recall, because a missed phishing link costs more than a false alarm. Our privacy layer catches 57 of 58 sensitive items on our synthetic test set. Next: a network-log analyzer and generative incident narratives with Granite. Thank you." |

## Evidence shots to capture (for the submission screenshots too)
1. Portal page showing the server **ACTIVE** (hide any password).
2. Jupyter terminal: `uname -m` (shows `s390x`), then the dashboard notebook running.
3. Jupyter with the training notebook and the classification report visible.
4. The dashboard result page (verdict, side by side, report).

## Honest points to be ready for if a judge asks
- The privacy layer is regex-based. It misses free-text names without a greeting or title (e.g. "signed by Rohit Verma").
- The model uses URL text features only. It does no live reputation or domain-age lookups.
- The demo runs over plain http or an SSH tunnel, so only fictional data is used.
- Our privacy test set is small (52 cases) and was written by us, so the 98% catch rate is optimistic.

## Fill-ins before recording
`<TM_Matrix010-cybershield-AI>` and `<score>` (read it live from the dashboard). The dataset and metric numbers above come from the LinuxONE server training run.
