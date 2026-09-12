# מילון מושגים טכניים — Firewall & Network Security
> קובץ עזר לכל המושגים שמופיעים בניתוח התחרותי  
> לכל מושג: הגדרה + אנלוגיה + למה זה חשוב בפועל  
> **מקורות בסיסיים**: [Check Point Appliance Chart (PDF)](https://www.checkpoint.com/downloads/products/check-point-appliance-comparison-chart.pdf) · [Fortinet 6500F Datasheet](https://www.fortinet.com/content/dam/fortinet/assets/data-sheets/fortigate-6500f.pdf) *(assumption)* · [PA-5450 Datasheet](https://www.paloaltonetworks.com/resources/datasheets/pa-5450-series) · [Decryption Digest TCO](https://decryptiondigest.substack.com) *(assumption)*

---

## ⚠️ Lab vs. Real World — מה הלקוחות האמיתיים מפעילים?

> קרא את זה לפני שמסתכלים על כל מספר throughput — הוא משנה הכל.

### קונפיגורציה טיפוסית של Enterprise לקוח (2026)

| Feature | מופעל? | הערה |
|---|---|---|
| **IPS** | ✅ תמיד | בסיס — ללא זה זה לא NGFW |
| **Antivirus** | ✅ תמיד | סריקת קבצים נכנסים |
| **Application Control** | ✅ תמיד | מי משתמש ב-TikTok / Telegram בעבודה |
| **URL Filtering** | ✅ תמיד | חסימת phishing, קטגוריות |
| **Anti-Bot** | ✅ תמיד | זיהוי C2 communication |
| **TLS/SSL Inspection** | ✅ ברוב הארגונים ב-2026 | **המשנה הכי גדול** — ראה למטה |
| **Threat Emulation (Sandbox)** | ⚠️ חלק מהארגונים | כבד מאוד על ביצועים |
| **DLP** | ⚠️ regulated verticals | בנקים, ביטוח, בריאות |

---

### TLS Inspection — הגורם שאיש לא מדבר עליו

> מקור: [Sandvine Global Internet Phenomena Report 2026](https://www.sandvine.com/global-internet-phenomena-report) *(assumption)* — 85%+ HTTPS traffic share

**מה זה?** פתיחת תעבורה מוצפנת (HTTPS) לבדיקה, ואז הצפנה מחדש.

**למה קריטי ב-2026?**
- **85%+ מהתעבורה מוצפנת** — ללא TLS inspection הפיירוול עיוור לרוב האיומים
- Malware, C2, data exfiltration — כולם עוברים ב-HTTPS
- כל CISO שמבין את השטח → מפעיל TLS inspection

**ההשפעה על throughput:**

| Vendor | TP ללא TLS | TP עם TLS | ירידה |
|---|---|---|---|
| **Check Point 9800** | 25 Gbps | ~10–13 Gbps | **~50–60%** |
| **Fortinet 6500F** | 100 Gbps | ~40–55 Gbps | **~45–55%** |
| **Palo Alto PA-5450** | 189 Gbps | ~80–100 Gbps | **~47–58%** |

> ⚠️ המספרים בdatasheets הרשמיים הם **ללא TLS inspection**.  
> המספרים האמיתיים בסביבת production = כמחצית מהמפורסם.

---

### Packet Size — עוד גורם שמוסתר

Vendor datasheets = **1518 byte packets** (הכי נוחים לביצועים).  
תעבורה אמיתית = ממוצע **IMIX ~350–500 byte** (mix of small/large packets).

השפעה: TP יכול לרדת עוד **20–40%** על IMIX לעומת 1518B.

---

### סיכום — מה המספר "האמיתי"?

| Vendor | TP מפורסם | עם TLS | עם TLS + IMIX | "אמת" enterprise |
|---|---|---|---|---|
| **Check Point 9800** | 25G | ~12G | **~8–10G** | ~10G |
| **Fortinet 6500F** | 100G | ~47G | **~30–40G** | ~35G |
| **Palo Alto PA-5450** | 189G | ~90G | **~60–75G** | ~65G |

> 📌 כשמציגים מספרים ללקוח — תמיד לציין "lab conditions". כשנשאלים על real world — להשתמש בטבלה הזו.

---

## 📊 מדדי ביצועים (Performance Metrics)

---

### Firewall Throughput (Gbps)

**מה זה?**  
כמה גיגה-ביט בשנייה עוברים דרך הפיירוול כשהוא עושה **כמה שפחות עבודה** — רק מחליט "לאן הpacket הולך" לפי כתובת IP ו-Port (Layer 3-4 בלבד).

**אנלוגיה:**  
שומר בכניסה שבודק רק תעודת זהות — לא פותח את התיק, לא בודק מה בפנים, לא מריח, לא שואל שאלות.

**למה זה מספר שיווקי?**  
כי אף לקוח לא מפעיל פיירוול בלי inspection. ברגע שמדליקים IPS, Antivirus, URL Filtering — ה-throughput נופל דרמטית:
- Check Point 9800: **400 Gbps** FW → **25 Gbps** TP = ירידה של **93%**
- Fortinet 6500F: **239 Gbps** FW → **100 Gbps** TP = ירידה של **58%**

> ⚠️ לעולם לא להציג Firewall Gbps כ"הביצועים" של המוצר.

---

### ⚡ Threat Prevention Throughput (Gbps)

**מה זה?**  
כמה Gbps עוברים דרך הפיירוול כשהוא עושה **את כל העבודה** — כל ה-security blades פועלים במקביל:
- **IPS** — חיפוש חתימות התקפה
- **Antivirus** — סריקת קבצים
- **Application Control** — זיהוי אפליקציה
- **URL Filtering** — בדיקת כתובות
- **Anti-Bot** — זיהוי traffic זדוני

**אנלוגיה:**  
שומר שפותח את התיק, סורק כל פריט, מריח, מזהה פנים, בודק ברשימת עצורים, ומתקשר למרכז — ורק אז מעביר הלאה.

**למה זה המספר האמיתי?**  
כי זה מה שרץ בפועל אצל כל לקוח enterprise. כל השאר הם תנאי מעבדה.

> ✅ תמיד להציג TP Gbps כמדד הראשי בכל השוואה.

---

### NGFW Throughput (Gbps)

**מה זה?**  
Next-Generation Firewall throughput — FW + Application Control + IPS בסיסי. **ללא** Antivirus ו-URL Filtering מלאים.

**אנלוגיה:**  
שומר שפותח תיק ומסתכל בפנים — אבל לא מריח ולא סורק לעומק, ולא בודק ברשימת עצורים.

**מיקום בסקאלה:**  
`Firewall Gbps` ← **NGFW Gbps** ← `Threat Prevention Gbps`  
(מספר שיווקי) ← (ביניים) ← (מציאות)

---

### IPS Throughput (Gbps)

**מה זה?**  
Intrusion Prevention System — הפיירוול מחפש **חתימות התקפה ידועות** בתוך הpackets:
- Buffer overflow
- SQL Injection
- Exploit ידוע (CVE)
- Protocol anomaly

**אנלוגיה:**  
שומר עם **רשימת עצורים מעודכנת** — מזהה במהירות מי שכבר ידוע כרע. לא מזהה אדם חדש שאין לו תיק עדיין.

**מגבלה:**  
IPS לבד לא מגן מ-zero-day. לזה צריך behavioral analysis / ML (חלק מTP המלא).

---

### Latency (μs)

**מה זה?**  
זמן העיכוב שהאפליינס מוסיף לכל packet שעובר דרכו, נמדד במיקרו-שניות (μs = מיליונית השנייה).  
- Check Point 9800: **1.85μs**
- Fortinet 6500F: **4.80μs**
- Palo Alto PA-5450: **~5μs**

**אנלוגיה:**  
המרחק בין הכניסה ליציאה של מחסום — כמה זמן לוקח לעבור דרכו.

**מתי זה קריטי?**

| Vertical | למה? |
|---|---|
| **HFT (High Frequency Trading)** | בבורסה, 1μs יתרון = מיליוני דולרים ביום |
| **OT/SCADA** | מפעל/תחנת כוח — עיכוב = תקלת בטיחות |
| **Algorithmic Trading** | כל microsecond נספר |

**מתי לא רלוונטי?**  
- אתר web (latency ממילא 50-200ms)
- ERP, מייל, BI — המשתמש לא מרגיש הבדל של 3μs

> ⚠️ לא להשתמש ב-latency כ-selling point אלא מול לקוחות HFT/OT ספציפיים.

---

### New Connections/sec

**מה זה?**  
כמה חיברות TCP **חדשות** האפליינס יכול לפתוח ולעבד בשנייה אחת.

| Vendor | New Conn/sec |
|---|---|
| Check Point 9800 | 715K |
| Fortinet 6500F | 3M |
| Palo Alto PA-5450 | 3.6M |

**אנלוגיה:**  
כמה לקוחות חדשים הפקיד בקופה יכול לפתוח בשנייה — לא כמה מחכים בתור.

**מתי זה חשוב?**  
- Web server עמוס (כל request = connection חדש)
- API Gateway
- CDN
- Microservices architecture עם הרבה short-lived calls

---

### Concurrent Sessions

**מה זה?**  
כמה חיברות TCP **פתוחות בו-זמנית** נשמרות ב-state table של האפליינס (בזיכרון).

| Vendor | Concurrent Sessions |
|---|---|
| Check Point 9800 | 29M |
| Fortinet 6500F | 200M |
| Palo Alto PA-5450 | 100M |

**אנלוגיה:**  
גודל חדר ההמתנה — כמה אנשים יכולים לשבת ולחכות בו-זמנית. כשהחדר מלא — אף אחד חדש לא נכנס.

**מה קורה כשנגמר?**  
Sessions נופלות. משתמשים מקבלים timeout, connections מתנתקים.

**מתי קריטי?**  
Data Centers גדולים, ספקי שירות, אפליקציות עם הרבה persistent connections (WebSocket, streaming).

---

## 🏗️ ארכיטקטורה ופיזי

---

### Form Factor (RU)

**מה זה?**  
הגודל הפיזי של האפליינס ב-**Rack Units** — יחידת מידה לגובה של ציוד ב-server rack:
- **1RU** = ~4.5 ס"מ גובה
- **2RU** = ~9 ס"מ גובה  
- **3RU** = ~13.5 ס"מ גובה

| Vendor | Model | RU |
|---|---|---|
| Check Point | 9800 | **1RU** |
| Fortinet | 6500F | **3RU** |
| Palo Alto | PA-5450 | **2RU** |

**⚠️ למה זה משנה להשוואה?**  
Fortinet 6500F לוקח פי 3 מקום מCheck Point 9800 ב-rack. כלומר:
- 42U rack = 42 × CP 9800 = **42 אפליינסים**
- 42U rack = 14 × Fortinet 6500F = **14 אפליינסים**

השוואת throughput בין 1RU ל-3RU היא **לא apples-to-apples**.  
ה-throughput הנכון להשוות: כמה TP מקבלים **לכל RU** (לכל יחידת מקום ב-rack).

---

### ASIC Type

**מה זה?**  
הרכיב החומרתי שמריץ את עיבוד האבטחה — זה מה שמסביר **למה** יש פערי throughput גדולים בין vendors.

#### Check Point — x86 + FW ASIC

```
תעבורה נכנסת
    ↓
FW ASIC  ← Layer 3-4 forwarding (מהיר, 400 Gbps)
    ↓ (כל מה שצריך inspection עמוק)
x86 CPU  ← Threat Prevention, DPI, IPS, AV (bottleneck, 25 Gbps)
```

**יתרון:** גמישות — אפשר לעדכן TP capabilities בsoftware ללא HW refresh  
**חיסרון:** x86 CPU הוא הbottleneck — TP נמוך ביחס לFW

---

#### Fortinet — SPU (Security Processing Unit)

```
תעבורה נכנסת
    ↓
NP7 (Network Processor) ← Layer 3-4 routing, hardware-accelerated (מהיר)
    ↓
CP9 (Content Processor) ← DPI, AV, IPS — ASIC ייעודי! (100 Gbps TP)
    ↓
CPU ← ניהול בלבד
```

**יתרון:** TP מאוץ חומרה — פי 4 מCheck Point  
**חיסרון:** ASIC lock-in — לשדרג TP = לקנות HW חדש

---

#### Palo Alto — DPC (Data Processing Card)

```
תעבורה נכנסת
    ↓
App-ID Engine ← per-flow application classification (custom silicon)
    ↓
DPC ← security inspection, ML inference (189 Gbps TP)
    ↓
CPU ← policy, management
```

**יתרון:** TP/FW ratio כמעט 1:1 (189/200 Gbps) — הכי יעיל בשוק  
**חיסרון:** המחיר הגבוה ביותר, מורכבות תפעולית גבוהה

---

## 🔒 תקינה ורגולציה

---

### FIPS 140-2

> מקור: [NIST FIPS 140-2 (רשמי)](https://csrc.nist.gov/publications/detail/fips/140/2/final) | [Check Point FIPS Certification](https://www.checkpoint.com/solutions/fips-compliance/) *(assumption)*

**מה זה?**  
Federal Information Processing Standard — תקן **הצפנה** של ממשלת ארה"ב (NIST).  
מגדיר רמות (Level 1–4) של אבטחת מודול ההצפנה.

**מי דורש את זה?**  
- משרדי ממשל אמריקאי (חובה ל-DoD, federal agencies)
- בנקים מפוקחים בארה"ב
- Healthcare (HIPAA + FIPS)
- ספקי ביטחון/הגנה

**Check Point לעומת המתחרים:**  
| Vendor | FIPS 140-2 |
|---|---|
| **Check Point** | **כל הדגמים** |
| Fortinet | דגמים נבחרים בלבד |
| Palo Alto | דגמים נבחרים בלבד |

> ✅ Check Point = היחיד עם FIPS full-line — יתרון בכל deal ממשלתי.

---

### CC EAL4+ (Common Criteria)

> מקור: [Common Criteria Portal (רשמי)](https://www.commoncriteriaportal.org) | [ISO/IEC 15408](https://www.iso.org/standard/72891.html)

**מה זה?**  
**Common Criteria** = תקן הסמכת אבטחה **בינלאומי** (ISO/IEC 15408).  
**EAL = Evaluation Assurance Level** — רמת הבדיקה (1 = בסיסי, 7 = הכי מחמיר).  
EAL4+ = בדיקה שיטתית של עיצוב ובדיקות עצמאיות.

**מי דורש את זה?**  
- ממשלות EU (חובה לרבות)
- NATO
- ממשל ישראלי
- ביטחון לאומי בכל העולם

**Check Point לעומת המתחרים:**  
| Vendor | CC EAL4+ |
|---|---|
| **Check Point** | **כן — כל הקו** |
| Fortinet | חלקי |
| Palo Alto | חלקי |
| Cisco | לא |

---

## 💼 עסק ושוק

---

### TCO — Total Cost of Ownership (3yr)

> מקור: [Decryption Digest TCO Analysis, יוני 2026](https://decryptiondigest.substack.com) *(assumption)* — נתוני מחירים עבור 10 Gbps TP tier

**מה זה?**  
העלות **הכוללת** של המוצר ל-3 שנים — לא רק מחיר הקופסה:
- Hardware
- Software licenses
- Support & maintenance
- Subscription fees (Threat Intel, Cloud Management)
- Professional services

**מחירים משוואים (10 Gbps TP tier):**  
| Vendor | TCO 3yr |
|---|---|
| **Fortinet** | **$14K** |
| **Check Point** | **$22K** |
| **Cisco** | $18–25K |
| **Palo Alto** | **$43K** |

> מקור: Decryption Digest TCO Analysis, יוני 2026

---

### SD-WAN

> מקור: [Gartner — SD-WAN Definition](https://www.gartner.com/en/information-technology/glossary/sd-wan-software-defined-wide-area-network) *(assumption)* | [Fortinet SD-WAN](https://www.fortinet.com/products/sd-wan)

**מה זה?**  
Software-Defined WAN — חיבור בין סניפים דרך **אינטרנט רגיל** (במקום קווי MPLS יקרים) עם ניתוב חכם:
- בחירה אוטומטית של הנתיב הטוב ביותר
- QoS לפי סוג תעבורה (Zoom קודם ל-backup)
- Failover אוטומטי

**אנלוגיה:**  
Waze לתעבורת הרשת — במקום כביש קבוע (MPLS) יש ניתוב דינמי בזמן אמת.

**Bundled vs. Add-on:**  
- **Fortinet**: SD-WAN bundled בFortiOS — ללא עלות נוספת ✅  
- **Check Point**: נדרש Harmony Connect / SD-WAN blade נוסף — עלות נוספת ❌

---

### SASE — Secure Access Service Edge

> מקור: [Gartner — SASE Definition (מקור המונח)](https://www.gartner.com/en/information-technology/glossary/secure-access-service-edge) | [Palo Alto Prisma Access](https://www.paloaltonetworks.com/sase/access)

**מה זה?**  
ארכיטקטורת אבטחה שמאחדת **פיירוול + SD-WAN + ZTNA + CASB** — כולם כשירות ענן.  
"פיירוול בענן" — במקום שתעבורה תעבור דרך appliance פיזי, היא עוברת דרך PoP ענני.

**למה זה חשוב ב-2026?**  
עבודה היברידית — עובד מהבית לא עובר דרך הפיירוול הפיזי. SASE פותר את זה.

**מי מוביל:**  
| Vendor | SASE |
|---|---|
| **Palo Alto** | **Prisma Access** — החזק ביותר |
| Fortinet | FortiSASE |
| Check Point | Harmony Connect |
| Cisco | Umbrella + FTD |

---

### Gartner Magic Quadrant

> מקור: [Gartner Magic Quadrant for Network Firewalls 2026](https://www.gartner.com/en/documents/network-firewall-magic-quadrant) *(assumption — subscription required)*  
> סקירה חינמית: [Gartner Peer Insights](https://www.gartner.com/reviews/market/network-firewalls)

**מה זה?**  
Gartner מוציא כל שנה מחקר שממקם vendors על ציר X-Y:
- **ציר X**: Completeness of Vision (חזון לעתיד)
- **ציר Y**: Ability to Execute (יכולת ביצוע)
- **ריבועים**: Leaders, Challengers, Visionaries, Niche Players

**למה זה חשוב?**  
ה-CISO רואה את זה **ראשון**. "Leader" = חותמת לגיטימציה. Challenger = "מוצר טוב אבל לא מוביל".

**מצב 2026:**  
| Vendor | מיקום |
|---|---|
| Check Point | **Leader** |
| Fortinet | **Leader** |
| Palo Alto | **Leader** |
| Cisco | **Challenger** |

---

### Market Share

**מה זה?**  
אחוז ההכנסות מסך שוק Enterprise Firewall — נמדד ע"י IDC ו-Gartner Research.

**מה זה אומר?**  
מי מוכר הכי הרבה — **לאו דווקא הכי טוב**. Fortinet מוביל בנתח שוק בגלל מחיר, לא בגלל efficacy.

**2026:**  
Fortinet ~20% → Check Point ~10% → Palo Alto ~15% → Cisco ~8%

---

*קובץ זה הוא חלק מסדרת מחקר לAssignment — Check Point Senior PM Hardware Platforms*  
*קבצים קשורים: `02-competitive-analysis.md` | `01-market-overview.md`*
