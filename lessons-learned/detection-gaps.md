# Detection Gaps & Lessons Learned

> Honest documentation of what worked, what didn't, and what I'd do differently.
> This is arguably the most valuable document in the lab — it shows real analytical thinking.

---

## Gap 1 — Brute Force Threshold Tuning

**Problem:** Initial SPL query used a threshold of 3 failed logons in 60 seconds. This fired constantly during legitimate admin activity (service account lockouts, password expiry).

**Root Cause:** No baseline of normal failed logon frequency was established before writing the rule.

**Fix Applied:** Raised threshold to 5 failures + added condition requiring a subsequent successful logon from the same IP. Significantly reduced false positives.

**Lesson:** Always baseline before you threshold. Run `stats count by Source_Network_Address` over a normal 24-hour period before setting alert thresholds.

---

## Gap 2 — PsExec Detection Needs Multiple Signals

**Problem:** Alerting on EventID 7045 (new service install) alone produced false positives — legitimate software installers also create temporary services.

**Fix Applied:** Required correlation of 7045 + EventID 4648 (explicit credential use) from the same source within 2 minutes. Confidence significantly higher.

**Lesson:** Single-event detections rarely work well in practice. Chain events for higher fidelity — accept lower sensitivity in exchange for lower false positive rate at L1.

---

## Gap 3 — Token Impersonation is Hard to Detect from Windows Logs Alone

**Problem:** Metasploit's `getsystem` via token impersonation left minimal footprint in standard Windows Event Logs. EventID 4688 showed process creation but the chain wasn't obviously suspicious without Sysmon.

**Fix Applied:** Deployed Sysmon with SwiftOnSecurity config. Sysmon EventID 1 (process creation with full command line) and EventID 10 (process access) dramatically improved visibility.

**Lesson:** Default Windows logging is insufficient for modern attack detection. Sysmon is non-negotiable for a real SOC endpoint. This is likely why enterprise SOCs deploy EDR on top of native logging.

---

## Gap 4 — C2 Beacon Detection Requires Traffic Baseline

**Problem:** Attempted to detect simulated C2 beaconing (Netcat reverse shell) via Wazuh network events. The alert threshold was arbitrary — no idea what "normal" outbound connection frequency looked like.

**Status:** Partially resolved. Added Wireshark manual analysis step to validate Wazuh alerts during simulation, but automated detection still needs baselining work.

**Planned Fix:** Capture 24 hours of normal traffic first, use `timechart` in Splunk to establish baseline, then set anomaly threshold at 2 standard deviations above mean.

---

## Gap 5 — No Coverage of Initial Access Phase

**Current lab scope starts at post-access simulation** (Metasploit handler already has a shell). No detection coverage for:
- Phishing simulation (email-based initial access)
- Exploit delivery
- Drive-by compromise

**Planned:** Add GoPhish for phishing simulation in next lab iteration.

---

## What I'd Build Differently Next Time

1. **Set up Sysmon on Day 1** — not as an afterthought
2. **Baseline before alerting** — run the environment clean for 24 hours, capture normal patterns
3. **Build a ticketing workflow** — even a simple spreadsheet to simulate L1 triage handoff to L2
4. **Add a firewall log source** — pfSense or iptables logs would have helped with C2 detection
5. **Document every false positive** — I started doing this halfway through; should have been from day 1
