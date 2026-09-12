# ניתוח תחרותי — Check Point 9000 vs. המתחרים
> **Session**: ראשון | **תאריך**: ספטמבר 2026  
> **מקורות**: [Gartner MQ 2026](https://www.gartner.com/en/documents/network-firewall-magic-quadrant) *(assumption)* · [Decryption Digest TCO](https://decryptiondigest.substack.com) *(assumption)* · [CyberRatings](https://cyberratings.org/research/) · [PeerSpot](https://www.peerspot.com/comparisons/check-point-next-generation-firewalls_vs_fortinet-fortigate) · [Heimdal Security](https://heimdalsecurity.com/blog/check-point-vs-fortinet/) *(assumption)* · datasheets רשמיים  
> **הקובץ הקודם**: `01-market-overview.md`

---

## 1. השוואת ביצועים טכניים — Head-to-Head

> כל הנתונים מ-datasheets רשמיים של הvendors:  
> • [Check Point 9800 Datasheet (PDF)](https://www.checkpoint.com/downloads/products/check-point-appliance-comparison-chart.pdf) | [Quantum 9000 Series](https://www.checkpoint.com/quantum/quantum-9000-series/)  
> • [Fortinet 6500F Datasheet (PDF)](https://www.fortinet.com/content/dam/fortinet/assets/data-sheets/fortigate-6500f.pdf) *(assumption)*  
> • [Palo Alto PA-5450 Datasheet](https://www.paloaltonetworks.com/resources/datasheets/pa-5450-series)  
> ⚠️ Threat Prevention = המספר הקריטי — לא Firewall throughput!

### 📖 מילון מדדים טכניים — מה אומר כל שורה?

| מדד | הסבר | הערה |
|---|---|---|
| **Firewall Throughput** | כמה Gbps עוברים דרך האפליינס ב-Layer 3-4 בסיסי — **ללא** כל security inspection. packet forwarding גרידא | מספר שיווקי. לא מייצג סביבת production |
| **⚡ Threat Prevention** | כמה Gbps עוברים **עם כל ה-blades פעילים** — IPS + AV + Application Control + URL Filtering + Anti-Bot | **המספר האמיתי**. זה מה שרץ בפועל אצל לקוח |
| **NGFW Throughput** | Next-Gen Firewall — FW + Application Control + IPS בסיסי. **ביניים** בין FW pure לTP מלא | יותר מציאותי מFW, פחות מ-TP מלא |
| **IPS Throughput** | Intrusion Prevention System בלבד — כמה Gbps ניתן לסרוק לחתימות התקפה ידועות | מדד חשוב לסביבות DC שמדגישות network intrusion |
| **Latency** | זמן העיכוב שהאפליינס מוסיף לכל packet (במיקרו-שניות, μs) | קריטי רק ל-HFT, OT, SCADA. לא רלוונטי ל-web/ERP/email |
| **New Connections/sec** | כמה חיברות TCP חדשות האפליינס יכול לפתוח בשנייה | קריטי לסביבות עם הרבה short-lived connections — web servers, APIs, CDN |
| **Concurrent Sessions** | כמה חיברות פתוחות בו-זמנית האפליינס יכול להחזיק בזיכרון | קריטי לסביבות DC גדולות. מעט מדי = sessions נפלות |
| **Form Factor** | גודל פיזי של האפליינס ב-Rack Units (1RU = גובה של ~4.5 ס"מ). קריטי לתכנון Data Center | השוואה חייבת להיות apples-to-apples — 1RU vs. 1RU! |
| **ASIC Type** | סוג הרכיב החומרתי שמריץ את העיבוד — x86 CPU רגיל / SPU (Fortinet) / DPC (PA) / ASIC ייעודי | מסביר ישירות **למה** יש הבדלי TP גדולים בין vendors |

---

### רמת Enterprise (1RU Appliance)

> 📖 הסבר על כל מדד: ראה [`06-glossary.md`](06-glossary.md)  
> 📋 מקורות datasheets: [CP 9800](https://www.checkpoint.com/downloads/products/check-point-appliance-comparison-chart.pdf) | [Fortinet 6500F](https://www.fortinet.com/content/dam/fortinet/assets/data-sheets/fortigate-6500f.pdf) *(assumption)* | [PA-5450](https://www.paloaltonetworks.com/resources/datasheets/pa-5450-series)

| | **Check Point 9800** | **Fortinet 6500F** | **Palo Alto PA-5450** |
|---|---|---|---|
| **Firewall Throughput** | 400 Gbps | 239 Gbps | 200 Gbps |
| **⚡ Threat Prevention** | **25 Gbps** | **100 Gbps** | **189 Gbps** |
| **NGFW Throughput** | 67.7 Gbps | 150 Gbps | — |
| **IPS Throughput** | 90.5 Gbps | 170 Gbps | — |
| **Latency** | **1.85μs** | 4.80μs | ~5μs |
| **New Connections/sec** | 715K | 3M | 3.6M |
| **Concurrent Sessions** | 29M (max) | 200M | 100M |
| **Form Factor** | **1RU** | **3RU** | 2RU |
| **ASIC Type** | x86 + FW ASIC | **NP7+CP9 SPU** | Custom DPC |

### רמת High-End / Data Center (Chassis)

> 📋 מקורות: [Maestro Hyperscale](https://www.checkpoint.com/quantum/quantum-maestro/) | [Fortinet 7121F](https://www.fortinet.com/content/dam/fortinet/assets/data-sheets/fortigate-7121f.pdf) *(assumption)* | [PA-7080](https://www.paloaltonetworks.com/resources/datasheets/pa-7000-series)

| | **Check Point Maestro** | **Fortinet 7121F** | **Palo Alto PA-7080** |
|---|---|---|---|
| **Threat Prevention** | עד 1.5 Tbps* | **520 Gbps** | **305 Gbps** |
| **Firewall** | עד 3 Tbps* | 1.89 Tbps | 590 Gbps |
| *Maestro Hyperscale — clustered architecture, לא single chassis

---

## 2. ניתוח ארכיטקטורה — למה הפערים קיימים

### Check Point — x86 + Selective ASIC

```
Traffic → x86 CPU (Threat Prevention, DPI) → ASIC (Firewall offload)
```

**המשמעות**: TP throughput מוגבל על ידי CPU cores. ASIC רק מאיץ FW basic forwarding.

- ✅ גמישות תוכנתית — update capabilities ללא שינוי HW
- ✅ Latency מצוינת ב-9800 (1.85μs) — CPU fast path
- ❌ TP/Gbps ביחס למחיר נמוך משמעותית מFortinet

### Fortinet — SPU (Security Processing Unit) Architecture

```
Traffic → NP7 (Network Processor, Layer 3-4) + CP9 (Content Processor, DPI/AV) → CPU
```

**המשמעות**: Threat Prevention מאוץ חומרה. CPU לניהול בלבד.

- ✅ TP throughput גבוה פי 4–8 מCheck Point באותו form factor
- ✅ Energy efficiency — 73% יותר יעיל מהמתחרה הקודם
- ❌ Latency 4.8μs לעומת 1.85μs של Check Point
- ❌ ASIC lock-in — upgrade TP = HW refresh

### Palo Alto — DPC (Data Processing Card) Architecture

```
Traffic → App-ID (per-flow classification) → DPC (security inspection) → CPU
```

**המשמעות**: Security built around application identity. ML inference על כל flow.

- ✅ App-ID מצוינת — הכי מדויקת בזיהוי applications
- ✅ Inline ML — detection מתוחכמת ביותר בשוק
- ✅ TP/Gbps ratio מצוין ב-PA-5450 (189 Gbps TP / 200 Gbps FW)
- ❌ המחיר הגבוה ביותר — $43K TCO לעומת $14K של Fortinet
- ❌ PAN-OS complex — steep learning curve

---

## 3. ניתוח תחרותי מלא — יתרונות וחסרונות

---

### 🔵 Fortinet FortiGate — המתחרה העיקרי

#### יתרונות

| # | יתרון | פירוט |
|---|---|---|
| 1 | **TCO הנמוך ביותר** | $14K לעומת $22K של CP — זול ב-57%. SD-WAN bundled בלי עלות נוספת |
| 2 | **Throughput/Price הטוב ביותר** | SPU ASIC נותן 100 Gbps TP במחיר שCheck Point נותן 25 Gbps |
| 3 | **Market Share מוביל** | 19.7% מהשוק (IDC) — הrecognition הכי גדול |
| 4 | **SD-WAN native** | FortiGate הוא גם SD-WAN — OPEX חיסכון על appliance נפרד |
| 5 | **FortiOS ecosystem** | FortiSIEM, FortiSOAR, FortiClient — single-vendor fabric |
| 6 | **Deployment פשוט** | GUI ברורה, documentation מצוינת, onboarding מהיר |
| 7 | **AI patents** | 500+ AI patents, FortiGuard AI threat intel |

#### חסרונות / נקודות תורפה

| # | חסרון | פירוט | מקור |
|---|---|---|---|
| 1 | **Security Efficacy נמוכה יותר** | Fortinet מפגר ב-2–5% לעומת Check Point (99%+) | [CyberRatings Enterprise Firewall Report](https://cyberratings.org/research/) |
| 2 | **Firmware instability** | לקוחות מדווחים על bugs בfeatures חדשות, patches שצריכים testing שנתי | [PeerSpot Reviews — Fortinet](https://www.peerspot.com/products/fortinet-fortigate-reviews) |
| 3 | **False positives גבוהים יותר** | CyberRatings מציין false positive rate גבוה יותר מCP ו-PA | [CyberRatings](https://cyberratings.org/research/) |
| 4 | **Latency גבוהה יותר** | 4.80μs לעומת 1.85μs של Check Point 9800 | [Fortinet 6500F Datasheet](https://www.fortinet.com/content/dam/fortinet/assets/data-sheets/fortigate-6500f.pdf) *(assumption)* |
| 5 | **Compliance פחות חזק** | פחות certifications מCheck Point. נחלש ב-regulated verticals | [Common Criteria Portal](https://www.commoncriteriaportal.org) |
| 6 | **ThreatCloud inferior** | FortiGuard מסתמך בעיקר על Fortinet sensor network — פחות רחב מThreatCloud AI של CP (300+ מקורות) | [Check Point ThreatCloud AI](https://www.checkpoint.com/infinity/threatcloud-ai/) |
| 7 | **ASIC lock-in** | TP upgrade = HW refresh. גמישות נמוכה בשינוי profile סביבה | [Fortinet SPU Architecture Whitepaper](https://www.fortinet.com/content/dam/fortinet/assets/white-papers/wp-spu.pdf) *(assumption)* |
| 8 | **Support quality** | ביקורות על TAC quality, CLI-heavy troubleshooting | [PeerSpot Reviews — Fortinet TAC](https://www.peerspot.com/products/fortinet-fortigate-reviews) |

#### **How to Win Against Fortinet**
> הנרטיב: "Fortinet מוזיל — Check Point מגן."
> - בפרסנטציה ללקוח regulated: "1% הפרש ב-detection rate = אלפי threats missed ביום. מה העלות של breach אחד?"
> - TP latency: "Check Point 9800 latency 1.85μs — Fortinet 4.8μs. בסביבות HFT/OT זה קריטי."
> - Compliance: "FIPS 140-2 + EAL4+ standard across entire Quantum line — Fortinet רק בדגמים נבחרים."

---

### 🟠 Palo Alto Networks PA-Series

#### יתרונות

| # | יתרון | פירוט |
|---|---|---|
| 1 | **App-ID — הכי מדויק בשוק** | Per-flow application identification, industry reference point |
| 2 | **Inline ML threat detection** | Machine learning בתוך PAN-OS — zero-day prevention מתוחכמת |
| 3 | **Security efficacy = Check Point** | שניהם 99%+ ב-CyberRatings — top tier |
| 4 | **TP/Firewall ratio הכי טוב** | PA-5450: 189/200 Gbps — כמעט 1:1 |
| 5 | **Platform breadth** | Cortex XDR + Prisma Cloud + XSOAR + AI Ops = ecosystem שלם |
| 6 | **SASE leading** | Prisma Access — הSASE החזק ביותר בשוק |
| 7 | **Gartner Vision** | הכי רחוק ב-Completeness of Vision |
| 8 | **PA-7500 בראייה עתידית** | 1.5 Tbps FW, 1.44 Tbps TP — unprecedented |

#### חסרונות / נקודות תורפה

| # | חסרון | פירוט |
|---|---|---|
| 1 | **TCO הגבוה ביותר** | $43K לעומת $22K CP ו-$14K Fortinet — יקר ב-95% מFortinet |
| 2 | **Licensing complexity** | Stacked subscriptions — קשה לחשב TCO ללא specialist |
| 3 | **Migration complexity** | מ-CP ל-PA = project גדול. רוב לקוחות נוטשים רק ברענון HW |
| 4 | **PAN-OS steep curve** | מורכב לתפעול, דורש הכשרה ארוכה |
| 5 | **SASE cannibalization עצמית** | Prisma Access "מאכל" את ה-PA-Series בcloud-first customers |
| 6 | **Budget-sensitive deals** | בכל deal שמחיר מדובר — Palo Alto מפסיד לFortinet |

#### **How to Win Against Palo Alto**
> הנרטיב: "Security efficacy שלנו זהה — במחיר שחצי."
> - TCO: "PA-5450 + 3yr subscriptions = $43K. Check Point 9800 = $22K. תקבלו את אותה efficacy."
> - Management: "SmartConsole unified management — כל domains, כל gateways, ממקום אחד. PA צריך Panorama + Cortex."
> - Compliance: "Check Point הוא הבחירה ההיסטורית של regulated verticals — banking, gov, defense."

---

### ⚫ Cisco Secure Firewall (Firepower)

#### יתרונות

| # | יתרון | פירוט |
|---|---|---|
| 1 | **Installed base ענק** | מיליוני Cisco רכיבים בארגונים — stickiness טבעי |
| 2 | **Splunk integration** | אחרי רכישת Splunk — telemetry + SIEM + response מאוחד |
| 3 | **Cisco campus stack** | ASA → Firepower → ISE → DNA Center — single vendor |
| 4 | **SecureX platform** | Integration עם Talos threat intel (אחת החזקות בעולם) |
| 5 | **Support & services** | Cisco TAC — רחב ומוכר |

#### חסרונות / נקודות תורפה

| # | חסרון | פירוט |
|---|---|---|
| 1 | **Legacy architecture** | Firepower = ASA + Sourcefire מאוחדים — לא native NGFW |
| 2 | **Gartner Challenger** | לא Leader — נחשב פחות mature מהשלושה האחרים |
| 3 | **Complex management** | FMC (Firepower Management Center) — מסורבל לעומת SmartConsole |
| 4 | **Migration nightmare** | ASA → Firepower migration = פרויקט מייגע |
| 5 | **Performance lag** | TP throughput נמוך ביחס לFortinet ול-PA באותו price tier |
| 6 | **Innovation pace** | פיתוח איטי יחסית — features מגיעים אחרי Fortinet ו-PA |
| 7 | **Greenfield weakness** | בdeal חדש (לא Cisco shop) — Cisco לרוב לא מנצח |

#### **How to Win Against Cisco**
> הנרטיב: "Cisco = complexity. Check Point = elegance."
> - "FMC vs SmartConsole — נדגים Live שניהם. הLearning curve מדבר בעד עצמו."
> - "Check Point רלוונטי גם מחוץ לCisco ecosystem — flexible vendor strategy."

---

## 4. טבלת השוואה מרכזת

> 📖 הסבר על כל קריטריון: ראה [`06-glossary.md`](06-glossary.md)

| קריטריון | **Check Point 9000** | **Fortinet 6000F** | **Palo Alto PA-5450** | **Cisco Firepower** |
|---|---|---|---|---|
| **TP Throughput (Gbps)** | 25 | **100** | **189** | ~40 |
| **Firewall Gbps** | **400** | 239 | 200 | ~100 |
| **Latency** | **1.85μs** | 4.80μs | ~5μs | ~10μs |
| **Security Efficacy** | **99%+** | ~96% | **99%+** | ~91% |
| **False Positive Rate** | נמוך | בינוני | נמוך | בינוני |
| **TCO 3yr ($K)** | $22K | **$14K** | $43K | $18–25K |
| **Management** | **SmartConsole** | FortiManager | Panorama | FMC |
| **SD-WAN** | נוסף בתוספת | **Bundled** | נוסף בתוספת | נוסף בתוספת |
| **SASE** | Harmony Connect | FortiSASE | **Prisma Access** | Umbrella+FTD |
| **FIPS 140-2** | **כל הדגמים** | דגמים נבחרים | דגמים נבחרים | — |
| **CC EAL4+** | **כן** | חלקי | חלקי | לא |
| **Gartner Position** | Leader | **Leader** | **Leader** | Challenger |
| **Market Share** | ~10% | **~20%** | ~15% | ~8% |
| **TP Architecture** | x86 CPU | **SPU ASIC** | DPC custom | x86 |
| **AI Capabilities** | ThreatCloud AI | FortiGuard AI | **ML-Powered** | Talos |

> ### 💡 למה Check Point מוביל ב-Firewall אבל מפגר ב-Threat Prevention?
>
> **הסיבה: ארכיטקטורת x86 + ASIC סלקטיבי**
>
> ```
> תעבורה נכנסת
>     ↓
> ASIC (FW basic forwarding) ← מהיר מאוד, hardware-offloaded → 400 Gbps ✅
>     ↓ (תעבורה שצריכה בדיקה עמוקה)
> x86 CPU (Threat Prevention, DPI, IPS, AV) ← CPU bottleneck → 25 Gbps ⚠️
> ```
>
> **פשוט**: ה-ASIC של Check Point רץ כהרף-עין על packet forwarding בסיסי (Layer 3-4).  
> אבל ברגע שנדרש **Threat Prevention אמיתי** — DPI, IPS, Antivirus, SSL Inspection — התעבורה עוברת ל-CPU הרגיל (x86), שהוא ה-bottleneck.
>
> לעומת זאת, **Fortinet** ו-**Palo Alto** בנו ASIC ייעודי גם לTP (SPU / DPC) — לכן הם מגיעים ל-100–189 Gbps TP מבלי לפגוע ב-CPU.
>
> ⚠️ **המשמעות המעשית**: כשרואים "400 Gbps Firewall" ב-Check Point 9800 — זה נכון, אבל רק לתעבורה שלא עוברת TP. בסביבת Enterprise אמיתית עם כל ה-blades פעילים, ה-throughput האפקטיבי נמוך בהרבה.
>
> 📌 ראה הסבר מורחב: **סעיף 2 — ניתוח ארכיטקטורה**

---

## 5. Battlecard — Quick Reference

### Check Point חזק כאשר:
- ✅ לקוח regulated (Banking, Gov, Defense, Healthcare)
- ✅ Compliance certifications נדרשות (FIPS, EAL4+)
- ✅ מנהלי רב-domain, rule base גדול, multi-admin
- ✅ Latency-sensitive environments (trading, OT)
- ✅ Unified management cross-environment (on-prem + cloud + mobile)
- ✅ לקוח שמחפש TCO ביניים עם security efficacy מקסימלית

### Check Point חלש כאשר:
- ❌ Budget-constrained, mid-market → Fortinet מנצח
- ❌ Cloud-first, SASE-driven → Palo Alto מנצח
- ❌ Cisco shop עם integration needs → Cisco Secure מנצח
- ❌ Throughput/Price ratio = KPI עיקרי → Fortinet מנצח

---

## 6. הודעות מרכזיות לPresentation

### לSlide SWOT (Session 1 — עברית):

**Strengths שכדאי להדגיש:**
1. 99%+ threat prevention accuracy — top tier עם Palo Alto
2. ThreatCloud AI — 300+ מקורות גלובליים לעומת Fortinet's walled garden
3. SmartConsole — הconsole המשוכלל ביותר בקטגוריה
4. Latency 1.85μs (9800) — מנצח Fortinet ו-Palo Alto
5. FIPS 140-2 + EAL4+ על כל הline — compliance advantage
6. Unified: on-prem + cloud + mobile + IoT ממקום אחד

**Weaknesses שצריך להודות בהם:**
1. TP throughput נמוך ביחס לFW throughput — 25 Gbps vs. 400 Gbps
2. TCO גבוה מFortinet ב-57%
3. Blade licensing complexity — קשה לtotal cost self-service
4. SD-WAN לא bundled — עלות נוספת
5. Support quality inconsistent מחוץ לAmericas/Europe

**Opportunities:**
1. Hardware refresh wave 2025–2027
2. **AI Factory Security — מוצר קיים ובשטח** — ראה הערה¹ למטה
3. Regulated verticals expansion (FinTech, Gov 2.0, Healthcare)
4. Vendor consolidation trend
5. Neocloud market (CoreWeave, Lambda, Nebius) — $20B, כולם על BlueField DPUs
6. Sovereign AI — ממשלות בונות AI פרטי, צריכות FIPS+EAL4+, air-gapped
7. Agentic AI Security (Lakera) — AI agents רצים אוטונומית, תוקפים חדשים
8. Post-Quantum Cryptography — NIST סגר תקנים, enterprises צריכים לשדרג הצפנה

**Threats:**
1. Fortinet SPU advancement — הם ממשיכים לשפר TP gap
2. Palo Alto SASE narrative — "perimeter is dead"
3. AWS/Azure/GCP native firewalls
4. Check Point own CVE history ([CVE-2026-50751](https://nvd.nist.gov/vuln/detail/CVE-2026-50751) *(assumption — NVD URL)* — authentication bypass, 8 June 2026)
5. Mid-market erosion — Fortinet אוכל נתח שוק ב-renewals

---

> ¹ **הערה — Check Point AI Factory Firewall (AIFF)**
> מוצר GA זמין לרכישה נכון ליולי 2026. רץ כcontainer נייטיב על **Nvidia BlueField-3 DPU** בתוך כל AI server.
> מספק: inline NGFW + tenant segmentation + AI prompt inspection + runtime threat detection (NVIDIA DOCA Argus).
> Zero impact על CPU/GPU. תמיכה ב-air-gapped environments. BlueField-4 בדרך.
> **רכישת Lakera** (ספטמבר 2025, ~$300M) — הוסיפה AI-native security לLLMs, agents, MCP servers.
> מקורות: [Check Point AIFF](https://www.checkpoint.com/press-releases/check-point-redefines-ai-security-for-enterprises-with-ai-cloud-protect-powered-by-nvidia-bluefield/) | [AI Factory Blueprint](https://www.checkpoint.com/press-releases/check-point-releases-the-ai-factory-security-blueprint-a-definitive-architecture-to-protect-the-ai-factory-from-gpu-to-governance/) | [Lakera acquisition](https://www.checkpoint.com/press-releases/check-point-acquires-lakera-to-deliver-end-to-end-ai-security-for-enterprises/)

---

## 7. Use-Case Matrix — מי מנצח איפה ולמי מתאים

> טבלה זו מסכמת עבור כל תרחיש עסקי: מה הצורך, מי הלקוח, ומי ה-vendor המנצח.

| Use Case | מה זה בפועל? | מתאים ל | CP | Fortinet | Palo Alto | Cisco | מנצח |
|---|---|---|---|---|---|---|---|
| **Enterprise HQ Perimeter** | פיירוול בכניסה למטה ארגוני — מסנן כל תעבורה פנים/חוץ, מנהל כללי אבטחה מורכבים | חברות ביטוח, תעשייה, לוגיסטיקה, קמעונאות גדולה, אוניברסיטאות — 500+ עובדים | מצוין | טוב | טוב | חלש | **Check Point** |
| **Regulated Verticals** | ארגונים שרגולטור מחייב תקנים מחמירים — FIPS, EAL4+, PCI-DSS, HIPAA. Breach = קנס + נזק תדמיתי | בנקים, קופות חולים, ממשלה, צבא, Elbit/IAI/Rafael, תשתיות קריטיות | מצוין | חלש | טוב | חלש | **Check Point** |
| **Data Center (High TP)** | שרתים שמתקשרים ביניהם בכמויות ענק. East-West traffic בתוך DC. צריך 50–500+ Gbps TP עם כל features | בנקים עם DC עצמאי, SaaS, streaming, colocation | חלש | מצוין | מצוין | חלש | **Fortinet / PA** |
| **Mid-Market** | חברה בינונית עם budget מוגבל ו-IT team קטן. קונה לפי TCO בספרדשיט | הייטק בינוני, יצרנים, משרדי עו"ד, שירותי בריאות פרטיים | חלש | מצוין | לא מתאים | חלש | **Fortinet** |
| **Latency-Sensitive (HFT/OT)** | כל מיקרו-שניה קריטית. בורסה = כסף. SCADA = בטיחות. 1.85μs של CP לעומת 4.8μs של Fortinet | בורסות (TASE), בנקים לאלגו-טריידינג, מפעלים, תחנות כוח, רכבות, הגנה | מצוין | חלש | חלש | לא מתאים | **Check Point** |
| **Branch / Distributed** | עשרות-מאות סניפים. SD-WAN מחליף MPLS. Zero Touch = קופסה שמתקינה את עצמה | רשתות קמעונאות, בנקים עם סניפים, רשתות מזון מהיר, חברות תחבורה | חלש | מצוין | טוב | טוב | **Fortinet** |
| **Cloud-First / SASE** | עובדים מכל מקום, אפליקציות בענן, אין "פנים ו-חוץ" ברשת. SASE = פיירוול בענן | סטארטאפים, SaaS, media & tech, כל ארגון שעבר ל-hybrid work | חלש | טוב | מצוין | טוב | **Palo Alto** |
| **AI Traffic Security (2026)** | כל עובד שולח prompts ל-ChatGPT/Claude — עלול לדלוף מידע רגיש. CISO צריך לראות ולשלוט | כל enterprise ב-2026, בייחוד regulated שאוסר AI אבל לא יכול לאכוף | חלש | חלש | טוב | לא מתאים | **Palo Alto** (חלקי) |
| **Service Provider / Carrier** | ISP/סלולר — מיליוני משתמשים בו-זמנית, Tbps throughput, 99.999% uptime | Bezeq, HOT, Partner, Cellcom, AWS/Azure edge PoPs, CDN, Equinix | טוב | מצוין | טוב | טוב | **Fortinet** |

### סיכום — איפה Check Point מנצח ומפסיד

| | Use Cases | הסיבה |
|---|---|---|
| ✅ **מנצח** | Enterprise HQ, Regulated, Latency-Sensitive | FIPS+EAL4+ על כל הline · 99%+ detection · Latency 1.85μs · SmartConsole |
| ❌ **מפסיד** | Mid-Market, Branch, Data Center, Cloud/SASE, AI Traffic | TCO גבוה ב-57% מFortinet · SD-WAN לא bundled · TP נמוך בDC · SASE פחות mature |
| ⚡ **הזדמנות** | AI Traffic Security | שוק חדש, אף vendor לא פתר — Check Point יכול להיות ראשון |

### מחיר לפי Vendor — TCO 3 שנים (10 Gbps TP tier)

| Vendor | TCO 3yr | אסטרטגיית תמחור | מי הלקוח |
|---|---|---|---|
| **Fortinet** | **$14K** | Volume leader — price aggression לwin market share | Mid-market, branches, budget-driven |
| **Check Point** | **$22K** | Premium justified — efficacy + compliance + management | Regulated enterprise, gov, defense |
| **Palo Alto** | **$43K** | Platform premium — security + SASE + XDR bundled | Cloud-first, security-mature, platform buyers |
| **Cisco** | **$18–25K** | Ecosystem lock-in — existing Cisco customers | Cisco shops עם investment קיים |

> מקור: [Decryption Digest TCO Analysis, יוני 2026](https://decryptiondigest.substack.com) *(assumption — newsletter)*

---
*קובץ זה הוא חלק מסדרת מחקר לAssignment — Check Point Senior PM Hardware Platforms*  
*הקובץ הקודם: `01-market-overview.md` | הקובץ הבא: `03-feature-proposal.md`*
