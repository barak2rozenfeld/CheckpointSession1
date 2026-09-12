# Firewall Fundamentals — איך פיירוול עובד בפועל
> מדריך מלא: packet flow, TLS, identity, topology, integrations
> קובץ עזר לAssignment — Check Point Senior PM Hardware Platforms  
>
> **מקורות עיקריים**:
> - [Check Point R81.20 Admin Guide](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_Gaia_AdminGuide/) *(assumption)*
> - [IETF RFC 8446 — TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446)
> - [IETF RFC 2544 — Network Benchmarking](https://datatracker.ietf.org/doc/html/rfc2544)
> - [Check Point Identity Awareness Admin Guide](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_IdentityAwareness_AdminGuide/) *(assumption)*
> - [Sandvine GIPR 2026](https://www.sandvine.com/global-internet-phenomena-report) *(assumption — 85% HTTPS stat)*

---

## 1. שכבות OSI — הבסיס

```
Layer 7 — Application   HTTP, DNS, Zoom, Salesforce — מה האפליקציה?
Layer 4 — Transport     TCP/UDP, Port, state         — מי מחובר למי?
Layer 3 — Network       IP addresses                 — מאיפה לאן?
Layer 2 — Data Link     MAC address                  — באיזה segment?
Layer 1 — Physical      חוט, אלקטרונים
```

פיירוול NGFW בודק **כל השכבות** — מL2 עד L7.
Firewall ישן (legacy) בדק רק L3-L4.

---

## 2. מסע ה-Packet המלא

> מקור: [Check Point Security Gateway Technical Overview](https://www.checkpoint.com/quantum/security-gateway/) | [RFC 2544 Benchmarking Methodology](https://datatracker.ietf.org/doc/html/rfc2544)

### שלב 1 — Layer 3/4: מי שולח ולאן?

```
Packet מגיע
     │
     ▼
┌─────────────────────────────────────────────────┐
│  STEP 1: Layer 3/4 Check (מהיר, ASIC/hardware)  │
│                                                 │
│  ├── IP source מוכר? blacklist? geo-block?      │
│  ├── IP destination מותר?                       │
│  ├── Port מותר?                                 │
│  ├── Session קיים בstate table?                 │
│  └── Rate limit? DDoS protection?               │
│                                                 │
│  → אפשר לחסום כאן לפני TLS — מהיר מאוד         │
└─────────────────────────────────────────────────┘
     │ עבר ✓
     ▼
```

### שלב 2 — TLS Decryption: פתיחת ההצפנה

> מקור TLS: [RFC 8446 — TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446) | [Check Point HTTPS Inspection Admin Guide](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_HTTPS_Inspection_AdminGuide/) *(assumption)*  
> מקור 85% HTTPS: [Sandvine GIPR 2026](https://www.sandvine.com/global-internet-phenomena-report) *(assumption)*

```
┌─────────────────────────────────────────────────┐
│  STEP 2: TLS/SSL Inspection  ← הכי כבד!         │
│                                                 │
│  85% מהתעבורה ב-2026 = מוצפנת (HTTPS)          │
│  ללא פתיחה → הפיירוול עיוור לתוכן               │
│                                                 │
│  Client ──TLS──► Firewall ──TLS──► Server       │
│                     │                           │
│              Man-in-the-Middle (לגיטימי)        │
│              מקבל cert מServer                  │
│              מציג cert משלו לClient             │
│              רואה: plaintext                    │
│                                                 │
│  ⚠️ עלות TP:                                    │
│  Check Point 9800: 25G → ~10-13G עם TLS         │
│  Fortinet 6500F:  100G → ~40-55G עם TLS         │
│  Palo Alto PA-5450: 189G → ~80-100G עם TLS      │
└─────────────────────────────────────────────────┘
     │ plaintext ✓
     ▼
```

### שלב 3 — Deep Inspection: מה בפנים?

```
┌─────────────────────────────────────────────────┐
│  STEP 3: Deep Packet Inspection (DPI)           │
│                                                 │
│  ├── IPS: חיפוש CVE signatures, exploits        │
│  │        "יש buffer overflow בrequest הזה?"   │
│  │                                             │
│  ├── Antivirus: סריקת קבצים byte by byte        │
│  │        "הexe הזה = Emotet v3?"              │
│  │                                             │
│  ├── App Control: מזהה אפליקציה                 │
│  │        "Port 443 אבל זה Dropbox, לא HTTPS"  │
│  │                                             │
│  ├── URL Filtering: בדיקת domain/category      │
│  │        "gambling? phishing? malware?"        │
│  │                                             │
│  ├── DLP: יוצא מידע רגיש?                      │
│  │        "יש CC numbers בקובץ הזה?"           │
│  │                                             │
│  └── ML/AI: behavioral scoring                 │
│             "זה נראה כמו C2 communication?"    │
│                                                 │
│  → BLOCK? → drop + log + alert                  │
│  → ALLOW? → ממשיך                               │
└─────────────────────────────────────────────────┘
     │ נקי ✓
     ▼
```

### שלב 4 — Actions: יותר מ"חסום/אפשר"

```
┌─────────────────────────────────────────────────┐
│  STEP 4: Policy Actions                         │
│                                                 │
│  ALLOW     → תן לעבור                           │
│  BLOCK     → זרוק + log + הצג error לuser       │
│  ALERT     → תן לעבור אבל שלח alert ל-SIEM      │
│  QUARANTINE → שלח לsandbox לבדיקה              │
│  STRIP     → הסר תוכן זדוני, שלח את השאר        │
│              (מחיקת macro מ-Word file)           │
│  REDIRECT  → שלח לcaptive portal / auth page    │
│  QoS       → תעדף / הגבל bandwidth             │
│  LOG ONLY  → תעד ללא חסימה (TAP mode)           │
└─────────────────────────────────────────────────┘
     │
     ▼
```

### שלב 5 — Re-encryption ושליחה

```
┌─────────────────────────────────────────────────┐
│  STEP 5: Re-encrypt + Forward                   │
│                                                 │
│  מצפין מחדש → שולח לServer                      │
│  מוסיף log entry                               │
│  מעדכן session table                           │
│  שולח telemetry ל-ThreatCloud                  │
└─────────────────────────────────────────────────┘
     │
     ▼
[Server] — מקבל תעבורה נקייה
```

---

## 3. Identity Awareness — איך הפיירוול יודע "מי דני"?

> מקור: [Check Point Identity Awareness R81.20 Admin Guide](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_IdentityAwareness_AdminGuide/) *(assumption)* | [Microsoft AD Integration Docs](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

### הבעיה

```
פיירוול רואה: packet מ-IP 10.0.0.5 לdropbox.com
הוא לא יודע: מי זה? מה הרשאותיו?

נדרש: לקשר IP → User → Group → Policy
```

### הפתרון — אינטגרציה עם Active Directory

```
מה קורה כשדני מדליק מחשב:

דני ──► Windows Login ──► Active Directory (DC)
                                │
                    AD רושם event:
                    "danny@company.com
                     logged in from 10.0.0.5
                     member of: Finance, VPN-Users"
                                │
                                ▼
                  Check Point Identity Awareness
                  (מאזין לDC via WMI / API)
                                │
                                ▼
                  Security Gateway מעדכן:
                  10.0.0.5 = danny = Finance

עכשיו: כל packet מ-10.0.0.5 → הפיירוול יודע: דני, כספים
```

### מה אפשר לעשות עם Identity

```
Policy examples:

Finance group + Personal Cloud (Dropbox) = BLOCK
R&D group + GitHub = ALLOW
HR group + מחוץ לשעות עבודה = BLOCK
VPN-Users + Remote Access = ALLOW
Everyone + Gambling sites = BLOCK
```

---

## 4. כל מה שהפיירוול מחובר אליו

```
                              INTERNET
                                  │
                          ┌───────┴───────┐
                          │    ROUTER     │
                          └───────┬───────┘
                                  │
                    ┌─────────────┴──────────────┐
                    │         FIREWALL            │
                    │      Check Point 9800       │
                    │                             │
                    │  eth0 = external (internet) │
                    │  eth1 = internal (LAN)      │
                    │  eth2 = DMZ (servers)       │
                    │  mgmt = management only     │
                    └──┬──────────────────────┬───┘
                       │                      │
          MANAGEMENT   │                      │   DATA PLANE
                       ▼                      ▼
          ┌────────────────────┐    ┌─────────────────────┐
          │   SmartConsole     │    │    Core Switch      │
          │ (Management Server)│    └──────────┬──────────┘
          │                    │               │
          │  Admin מגדיר:      │    ┌──────────┼──────────┐
          │  ├── Rules         │    │          │          │
          │  ├── Objects       │  [Finance] [IT/R&D]  [Servers]
          │  ├── Blades        │
          │  └── Monitoring    │
          └────────────────────┘
```

### חיבורי ארגון מלאים

```
                         FIREWALL
                             │
         ┌───────────────────┼────────────────────────┐
         │                   │                        │
         ▼                   ▼                        ▼
┌─────────────────┐ ┌──────────────────┐ ┌────────────────────┐
│ Active Directory│ │   SIEM           │ │   Sandbox          │
│ (Windows AD /   │ │ (Splunk /        │ │ (ThreatCloud /     │
│  Azure AD /     │ │  Microsoft       │ │  Check Point       │
│  LDAP / Okta)   │ │  Sentinel)       │ │  Emulation Cloud)  │
│                 │ │                  │ │                    │
│ שואל:           │ │ מקבל:            │ │ מקבל:              │
│ "מי IP זה?"     │ │ כל log של        │ │ קבצים חשודים       │
│                 │ │ allow/block      │ │                    │
│ מקבל:           │ │                  │ │ מחזיר:             │
│ user + groups   │ │ CISO רואה        │ │ CLEAN / MALWARE    │
└─────────────────┘ │ dashboard        │ └────────────────────┘
                    └──────────────────┘

         ┌───────────────────┬────────────────────────┐
         │                   │                        │
         ▼                   ▼                        ▼
┌─────────────────┐ ┌──────────────────┐ ┌────────────────────┐
│  ThreatCloud AI │ │   CA / PKI       │ │   HR / SCIM        │
│  (Check Point   │ │  (Certificate    │ │  (User provisioning│
│   Cloud)        │ │   Authority)     │ │   automation)      │
│                 │ │                  │ │                    │
│ "האם IP/domain/ │ │ TLS Inspection   │ │ עובד חדש נוסף →    │
│  hash זדוני?"   │ │ דורש certificate │ │ קבוצה ב-AD →       │
│ 300+ מקורות     │ │ חתום ע"י CA      │ │ policy אוטומטית    │
│ גלובליים        │ │ שהclient סומך    │ │                    │
└─────────────────┘ └──────────────────┘ └────────────────────┘
```

### מה on-device vs. מה בענן

```
ON DEVICE (בזיכרון הgateway):
  ✅ Session table (כל החיברות הפתוחות)
  ✅ Rule base / policy (הורד מSmartConsole)
  ✅ Identity cache (IP → User, מתרענן כל שניות)
  ✅ Signature cache (IPS signatures, מתעדכן כל שעות)
  ✅ URL category cache (domains נפוצים)
  ✅ Threat indicators cache (IPs/hashes ידועים)

CLOUD / EXTERNAL (real-time queries):
  ☁️ ThreatCloud AI — domain/IP/hash חדש שלא בcache
  ☁️ Sandbox — קובץ חשוד נשלח לניתוח
  🏢 Active Directory — sync event-based
  📊 SIEM — לוגים נשלחים לניתוח
```

---

## 5. דוגמא מלאה — דני מנסה להעלות קובץ

```
דני פותח Chrome → גורר Excel לDropbox

─────────────────────────────────────────────────
STEP 1: Layer 3/4
  src: 10.0.0.5  dst: dropbox.com:443
  IP clean? ✓    Port allowed? ✓
  → ממשיך
─────────────────────────────────────────────────
STEP 2: Identity Lookup
  10.0.0.5 → AD cache → danny, Finance group
─────────────────────────────────────────────────
STEP 3: TLS Decrypt
  פותח HTTPS → רואה HTTP multipart upload
  קובץ: Q3-Financial-Report.xlsx
─────────────────────────────────────────────────
STEP 4: DPI
  App-ID: "זה Dropbox Personal, לא Business"
  DLP: "הקובץ מכיל מספרי חשבונות בנק"
  URL: "Personal Cloud Storage category"
─────────────────────────────────────────────────
STEP 5: Policy Check
  Finance + Personal Cloud Storage = BLOCK
  Finance + CC/Bank data outbound = BLOCK (DLP)
─────────────────────────────────────────────────
STEP 6: Actions
  BLOCK packet
  LOG: "danny@company | BLOCK | dropbox.com |
        Personal Cloud + DLP violation |
        filename: Q3-Financial-Report.xlsx"
  ALERT → SIEM → CISO email
  NOTIFY → דני רואה: "גישה חסומה — פנה לIT"
─────────────────────────────────────────────────
```

---

## 6. Deployment Modes — איך הפיירוול "יושב" ברשת

> מקור: [Check Point Deployment Guide](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_Installation_and_Upgrade_Guide/) *(assumption)* | [Check Point Maestro Hyperscale Docs](https://www.checkpoint.com/quantum/quantum-maestro/)

### Mode 1 — Perimeter (הכי נפוץ)

```
           INTERNET
               │
           [Router]
               │
    ┌──────────┴──────────┐
    │  FIREWALL (Routed)  │ ← IP משלו בכל interface
    │  eth0: 1.2.3.4      │
    │  eth1: 192.168.1.1  │
    └──────────┬──────────┘
               │
           [LAN Switch]
               │
          [Workstations]

✅ NAT, routing, כל features
✅ הכי נפוץ בארגונים
```

### Mode 2 — Internal Segmentation

```
           INTERNET
               │
    [Perimeter FW] ← Layer 1 — חיצוני
               │
    [Internal FW]  ← Layer 2 — פנימי, בין segments
               │
    ┌───────────┼───────────┐
 [Finance]   [R&D]       [HR]
 VLAN 10     VLAN 20    VLAN 30

✅ Lateral movement protection
✅ אם תוקף נכנס ל-Finance → לא מגיע ל-R&D
```

### Mode 3 — High Availability (HA)

```
           INTERNET
               │
    ┌──────────┴──────────┐
    │   FW Active ✓       │ ← מקבל תעבורה
    └──────────┬──────────┘
               │ sync (sessions + policy)
    ┌──────────┴──────────┐
    │   FW Standby ○      │ ← מחכה, מסונכרן
    └─────────────────────┘

Active נופל → Standby עולה תוך שניות
Sessions נשמרות — חיבורים לא מתנתקים
✅ חובה בסביבות production
```

### Mode 4 — Maestro Hyperscale

```
           INTERNET
               │
    ┌──────────┴──────────┐
    │  MAESTRO Orchestrator│ ← מחלק תעבורה
    │  (Hardware LB)       │   policy אחד מרכזי
    └──┬────┬────┬────┬───┘
       │    │    │    │
    [9800][9800][9800][9800]  × עד 52
    25G   25G   25G   25G
    TP    TP    TP    TP
       ↓    ↓    ↓    ↓
    = 100 Gbps TP effective
    (52 boxes = עד 1.3 Tbps)

✅ Scale without ASIC investment
✅ הוסף box כשצריך — pay as you grow
✅ Redundancy built-in
```

### Mode 5 — Bridge / Transparent

```
    Internet ──[FW transparent]── LAN

    הפיירוול = "שקוף" — אין IP משלו
    ✅ קל להוסיף לרשת קיימת ללא שינויים
    ✅ לא צריך לשנות routing
    ⚠️ פחות visibility לcertain features
```

### Mode 6 — TAP / Monitor Only

```
    Internet ──── Switch ──── LAN
                    │
                 [FW TAP] ← copy של תעבורה
                 רואה הכל, לא חוסם

    ✅ לPOC, audit, ללא השפעה על production
    ✅ ללמוד את הרשת לפני deployment
    ❌ לא מגן בפועל
```

---

## 7. מה ה-Admin מנהל — SmartConsole

> מקור: [Check Point SmartConsole R81.20](https://sc1.checkpoint.com/documents/R81.20/WebAdminGuides/EN/CP_R81.20_SecurityManagement_AdminGuide/) *(assumption)* | [SmartConsole Product Page](https://www.checkpoint.com/quantum/smart-management/)

```
Admin ──► SmartConsole (GUI)
               │
               ▼
    SmartCenter (Management Server)
               │
    ┌──────────┼──────────┐
    │          │          │
 [GW #1]   [GW #2]   [GW #N]
 מקבל      מקבל      מקבל
 policy    policy    policy
 בלחיצה    אחת       על כולם

Admin מגדיר:
  ├── Rules         (מי יכול לדבר עם מי, על איזה port)
  ├── Objects       (servers, networks, users, groups)
  ├── Blades        (IPS on? AV on? TLS inspection? DLP?)
  ├── Identity      (אילו AD groups מקבלים מה)
  ├── Logging       (מה נרשם, לאן נשלח)
  └── Monitoring    (dashboards, alerts, reports)
```

---

*קובץ זה הוא חלק מסדרת מחקר לAssignment — Check Point Senior PM Hardware Platforms*
*קבצים קשורים: `07-architecture-deep-dive.md` | `06-glossary.md` | `02-competitive-analysis.md`*
