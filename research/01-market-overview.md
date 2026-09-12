# מחקר שוק — Perimeter Security Appliances 2026
> **Session**: ראשון | **תאריך**: ספטמבר 2026  
> **מקורות**: [Gartner](https://www.gartner.com) · [IDC](https://www.idc.com) · [CyberRatings](https://cyberratings.org/research/) · [Decryption Digest](https://decryptiondigest.substack.com) *(assumption)* · [MarkWide Research](https://markwideresearch.com/enterprise-firewall-market/) *(assumption)* · [IndexBox](https://www.indexbox.io) *(assumption)*

---

## גודל השוק

| Segment | 2024 | 2025 | 2026 | מקור |
|---|---|---|---|---|
| Network Security (כולל) | $21.3B | $23.3B | **$25.8B** | [Gartner July 2026](https://www.gartner.com/en/newsroom/press-releases/2026-07-gartner-forecasts-worldwide-information-security-spending) *(assumption)* |
| Enterprise Firewall | — | — | **$13.8B** | [MarkWide Research 2026](https://markwideresearch.com/enterprise-firewall-market/) *(assumption)* |
| Total Cybersecurity | $193B | $213B | **$248.9B** | [Gartner 2Q26](https://www.gartner.com/en/newsroom/press-releases/2026-05-gartner-forecasts-worldwide-security-spending) *(assumption)* |

**CAGR Enterprise Firewall**: 9.6% עד 2036 → שוק יגיע ל-$31.5B — [MarkWide Research](https://markwideresearch.com/enterprise-firewall-market/) *(assumption)*  
**Hardware Firewall growth**: מוסיף **$9.9B חדש** עד 2030 (4th largest category) — [Gartner IT Spending Forecast](https://www.gartner.com/en/information-technology/insights/it-spending-forecast) *(assumption)*  
**Unit shipments**: 4.5–5.5M יחידות שנתיות, גדל 6–8% לשנה — [IDC Worldwide Security Appliance Tracker](https://www.idc.com/getdoc.jsp?containerId=US51680824) *(assumption)*

> **המסר**: הperimeter appliance לא מת. הוא ממשיך לצמוח, גם בעולם עם SASE ו-cloud.

---

## עמדה תחרותית — Gartner Magic Quadrant 2026

> מקור: [Gartner Magic Quadrant for Network Firewalls 2026](https://www.gartner.com/en/documents/network-firewall-magic-quadrant) *(assumption — subscription required)*

| Vendor | מיקום בMQ | חוזק עיקרי |
|---|---|---|
| **Palo Alto Networks** | Leader — הכי רחוק ב-Vision | Cortex XDR + Prisma + AI platform |
| **Fortinet** | Leader — הכי גבוה ב-Execution | Price/performance, SD-WAN, נתח שוק |
| **Check Point** | Leader | Security efficacy, compliance, SmartConsole |
| **Cisco** | Challenger | Installed base, post-Splunk telemetry |

---

## נתח שוק (IDC 2024)

> מקור: [IDC Worldwide Security Appliance Tracker, Q4 2024](https://www.idc.com/getdoc.jsp?containerId=US51680824) *(assumption — subscription required)*

- **Fortinet**: **~19.7%** — מנהיג בunits shipped
- Top 5 vendors ביחד: פחות מ-70% revenue → שוק מפוזר, יש מקום לhyper-specialization

---

## TCO השוואתי — 10 Gbps Threat Prevention Tier, 3 שנים

> מקור: [Decryption Digest TCO Analysis, יוני 2026](https://decryptiondigest.substack.com) *(assumption — newsletter, ייתכן Substack)*

| Vendor | HW List Price | 3yr Subscription | **סה״כ TCO** |
|---|---|---|---|
| **Fortinet** | ~$8,500 | ~$5,500 | **~$14,000** |
| **Check Point** | ~$14,500 | ~$7,500 | **~$22,000** |
| **Palo Alto** | ~$25,000 | ~$18,000 | **~$43,000** |

**Check Point יקר מFortinet ב-57%**  
**Palo Alto יקר מFortinet ב-207%**

---

## Check Point 9000 Series — כל 6 הדגמים

> מקור: [Check Point Appliance Comparison Chart](https://www.checkpoint.com/downloads/products/check-point-appliance-comparison-chart.pdf) (רשמי)

### סקירה כללית — מיצוב הדגמים

| דגם | מיצוב | Use Case |
|---|---|---|
| **9100** | Entry Enterprise | Perimeter — סניף גדול, campus קטן |
| **9200** | Entry-Mid Enterprise | Perimeter — campus בינוני |
| **9300** | Mid Enterprise | Perimeter — HQ בינוני |
| **9400** | Mid-High Enterprise | Perimeter — HQ גדול |
| **9700** | High Enterprise | Perimeter / Data Center edge |
| **9800** | Top Enterprise | Perimeter / Data Center — ה-flagship |

### ביצועים — Enterprise Testing Conditions (אמיתיים, לא Lab)

| דגם | **Threat Prevention (Gbps)** | NGFW (Gbps) | IPS (Gbps) | Firewall (Gbps) | Latency | Connections/sec |
|---|---|---|---|---|---|---|
| **9100** | **6.5** | 18.6 | 25.7 | 55 | 10μs | 190K |
| **9200** | **8** | 19 | 31 | 60 | 9μs | 250K |
| **9300** | **10.5** | 28.2 | 39 | 70 | 9μs | 300K |
| **9400** | **14** | 33.3 | 46.4 | 72.6 | **1.85μs** | 355K |
| **9700** | **20** | 57.6 | 73.7 | 129 | **1.85μs** | 535K |
| **9800** | **25** | 67.7 | 90.5 | 185 | **1.85μs** | 715K |

### ביצועים — Lab (RFC 2544/3511)

| דגם | FW Lab (Gbps) | TP TLS/HTTPS (Gbps) | VPN (Gbps) | Concurrent Sessions |
|---|---|---|---|---|
| **9100** | 80 | **2.96** | 22.1 | 2.75M/7.27M/16.2M |
| **9200** | 80 | **3.6** | 28.6 | 2.75M/7.27M/16.2M |
| **9300** | 80.2 | **5.1** | 33 | 2.75M/7.27M/16.2M |
| **9400** | 200 | **5.5** | 40 | 2.75M/7.27M/16.2M |
| **9700** | 400 | **9.6** | 75 | 6.5M/15M/29M |
| **9800** | 400 | **10.1** | 75 | 7M/15M/29M |

### מפרט חומרה

| דגם | CPU Cores (Physical/Virtual) | RAM Base | Storage | Maestro Max (NGTP) |
|---|---|---|---|---|
| **9100** | 4 / 8 | 16 GB | 480 GB SSD | 74 Gbps |
| **9200** | 4 / 8 | 16 GB | 480 GB SSD | 99 Gbps |
| **9300** | 10 / 16 | 16 GB | 480 GB SSD | 135 Gbps |
| **9400** | 14 / 20 | 16–64 GB | 960 GB SSD | 165 Gbps |
| **9700** | 16 / 32 | 32–96 GB | 960 GB NVMe | 249 Gbps |
| **9800** | 20 / 40 | 32–128 GB | 960 GB NVMe | **300 Gbps** |

> ⚠️ **נקודה קריטית #1**: הפער FW/TP — 9800 עושה 185 Gbps FW אבל רק **25 Gbps TP**. בTLS זה צונח ל-**10.1 Gbps**.  
> ⚠️ **נקודה קריטית #2**: 9100–9300 חולקים את **אותו** concurrent sessions pool — ה-differentiation הוא TP ו-cores בלבד.  
> ✅ **יתרון**: Latency 1.85μs ב-9400/9700/9800 — מנצח Fortinet (4.8μs) ו-Palo Alto (~5μs).  
> ✅ **Maestro HyperScale**: הפתרון ל-TP limitation — clustering מרובה appliances. 9800 Maestro = עד 300 Gbps NGTP.

**ארכיטקטורה**: Intel x86 multi-core + ASIC acceleration לfirewall forwarding בלבד. TP = CPU-bound.

---

## טרנדים עיקריים בשוק 2026

> מקורות: [Gartner — Hybrid Mesh Firewall](https://www.gartner.com/en/documents/hybrid-mesh-firewall) *(assumption)* · [Gartner — Securing AI Forecast](https://www.gartner.com/en/newsroom/press-releases/2026-securing-ai-spending) *(assumption)* · [CyberRatings Trends](https://cyberratings.org/research/)

| טרנד | כיוון | רלוונטיות לCheck Point |
|---|---|---|
| **Hardware Refresh Wave** | חיובי | ארגונים שדחו 2020–21 נכנסים להחלפה עכשיו |
| **AI-Powered Threat Prevention** | ניטרלי/חיובי | כולם מציגים AI — נדרש differentiator ממשי |
| **Hybrid Mesh Firewall (HMF)** | חיובי | [Gartner קטגוריה חדשה](https://www.gartner.com/en/documents/hybrid-mesh-firewall) *(assumption)*: HW+virtual+cloud+unified mgmt |
| **SASE Cannibalization** | שלילי | Fortinet+Palo Alto מציעים SASE שמחליף חלק מהappliances |
| **Zero Trust / Microsegmentation** | חיובי | שוק מתרחב מperimeter לinternal east-west |
| **AI Traffic Security (NEW 2026)** | חיובי — חדש | כל enterprise שולח prompts ל-LLMs — צורך חדש באבטחה |
| **Securing AI (Gartner #1 growth)** | חיובי | $21.9B חדש עד 2030 — [Gartner Securing AI Forecast](https://www.gartner.com/en/newsroom/press-releases/2026-securing-ai-spending) *(assumption)* |

---

## דרישות קונה עיקריות

| דרישה | משקל | הערה |
|---|---|---|
| Throughput תחת Full Threat Prevention | **קריטי** | לא firewall throughput — TP throughput הוא המספר האמיתי |
| Security Efficacy (Detection Rate) | **קריטי** | CyberRatings, SE Labs — benchmark results |
| HA / Clustering | גבוה | 99.999% uptime דרישה ב-enterprise ו-carrier |
| TCO על 3–5 שנים | גבוה | HW + subscriptions + PS + renewal |
| Interface Density & Flexibility | בינוני-גבוה | מודולריות NIC לפי צרכי site |
| Management & Automation | גבוה | Single pane of glass, API, SOAR |
| Compliance Certifications | **קריטי (Regulated)** | FIPS 140-2, CC EAL4+, ממשלה/פיננסים |
| AI / Cloud Readiness | עולה מהר | Hybrid mesh, AI inline detection |

---

## Talking Points — Slide 1, Session 1 (עברית)

1. **גודל שוק**: שוק Firewall עומד על $13.8B ב-2026, צומח 9.6% CAGR. Hardware מוסיף $9.9B חדש עד 2030 — הperimeter לא מת.
2. **מצבנו**: אנחנו Gartner Leader עם 99%+ efficacy — אבל Fortinet לוקח 20% מהשוק עם TCO נמוך ב-57%.
3. **גל הRefresh**: ארגונים שדחו רכישות ב-COVID נכנסים לגל החלפה 2025–2027. זה החלון שלנו.
4. **AI כמניע**: Securing AI = $21.9B חדש עד 2030. לקוחות מחפשים AI security ללא עלות TCO נוספת.
5. **WHY NOW**: בלי differentiator ברור — ב-refresh הבא, לקוחות compliance שלנו יפנו לFortinet.

---
*קובץ זה הוא חלק מסדרת מחקר לAssignment — Check Point Senior PM Hardware Platforms*  
*הקובץ הבא: `02-competitive-analysis.md`*
