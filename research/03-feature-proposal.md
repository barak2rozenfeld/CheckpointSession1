# הצעת ה-Feature — Quantum Boost (שם עבודה)

> **מה זה:** מודול DPU (Nvidia BlueField-3) בחריץ ההרחבה של Quantum 9700/9800, שאליו יורדים TLS ו-pattern matching מה-x86.
> **סטטוס:** הצעה שלי ל-roadmap. **לא** מוצר קיים ולא פריט שאושר ב-Check Point. כל המספרים שמעבר למצב הקיים הם יעדי תכנון לאימות ב-PoC.
> מופיע ב-deck בשקפים 11 (החלטה), 12 (מה זה), 13 (איך זה יעבוד), 14 (בר-ביצוע וסיכונים), 15 (business case), 16 (הבקשה).

## מצב לפני ואחרי (שקף 12)

ההשוואה היא ברמת ארכיטקטורת המוצר, בשלוש שכבות, ולא רק ברמת ה-CPU:

| שכבה | לפני (9800 כפי שנמכר היום) | אחרי (ההצעה) |
|---|---|---|
| תוכנה | Gaia R82 · blades: FW, IPS, AV, Anti-Bot, App Control, TLS Inspection, SmartConsole | ללא שינוי. אותו Gaia, אותן blades, אותה policy, אותו SmartConsole |
| האצה | SecureXL · CoreXL, האצת L3/4 בלבד. אין offload ל-TLS ול-pattern matching | SecureXL · CoreXL + שכבת Boost offload: TLS ו-patterns יורדים לכרטיס |
| חומרה | NIC 2×100G · FW ASIC (L3/4, session) · Intel Xeon x86 (נושא 100% מעומס ה-TP) · חריץ PCIe Gen5 x16 **ריק** | אותה חומרה + BlueField-3 DPU בחריץ הקיים. ה-x86 משוחרר ל-policy, App ו-ThreatCloud AI |

### המדדים, בפירוט לפי סוג throughput

| מדד | לפני (נמדד, Appliance Chart) | אחרי (יעד תכנון) |
|---|---|---|
| Full TP + TLS | ~10 Gbps | ~40 Gbps (טווח 35–50) — **אומדן** |
| Full TP (בלי TLS) | 25 Gbps | ~60 Gbps — **אומדן** |
| TP+TLS per 1RU | 10 (מול Fortinet ~17, Palo Alto ~18) | 40 — **אומדן** |
| מסלול השדרוג | קופסה חדשה | כרטיס בשטח, אותו 1RU, אותו חוזה support |

> ערכי ה-"לפני" הם Enterprise Testing Conditions לפי Appliance Comparison Chart. ערכי ה-TLS של המתחרים מוערכים בכ-50% מה-TP שלהם ומנורמלים ל-RU (6500F הוא 3RU, PA-5450 הוא 5U) — **אומדן**.
> תקרת ה-×4 (ולא ×10) נובעת מ-offload boundary: policy, App Control ו-URL נשארים על ה-x86 והם ה-bottleneck הבא.

## החלופות שנשקלו ולמה נבחר offload (שקף 11)

הקריטריון: **לעלות בציר התפוקה per RU בלי לזוז שמאלה בציר הגמישות** (הזמן להוסיף engine או מודל AI חדש). הגמישות היא הנכס התחרותי של Check Point מול ASIC של Fortinet.

| # | חלופה | זמן | עלות (NRE + צוות) | גמישות | per RU | פסיקה |
|---|---|---|---|---|---|---|
| 1 | ASIC משלנו | 3–5 שנים | $300M+ — **אומדן** | יורדת: לוגיקה נצרבת, engine חדש מחכה לדור חומרה | עולה | נפסל |
| 2 | FPGA בתכנון עצמי | ~3 שנים | $30–50M — **אומדן** | בינונית: כל engine דורש HDL ו-timing closure | עולה חלקית, נשאר פער מול ASIC | נפסל |
| 3 | Maestro scale-out | קיים היום | 0 פיתוח | נשמרת | **ללא שינוי**: 4×9800 = אותם 10 per RU, ×4 rack ו-TCO | קיים, לא פותר את הבעיה |
| 4 | Offload ל-DPU קיים (BlueField-3) | 18 חודשים | $7.2M — **אומדן** | נשמרת: התוכנה נשארת שלנו | 10 → ~40 באותו 1RU | **ההמלצה** |

**למה 4 ולא 1/2:** את ה-$300M וה-tape-out מישהו אחר כבר שילם. אנחנו כותבים תוכנה על סיליקון מוכן, ולכן זה פרויקט תוכנה ואינטגרציה ולא פרויקט סיליקון. crypto בקצב 400G ומנוע RegEx (RXP) כבר בחומרה, ו-Check Point כבר מריצה BlueField-3 ב-AI Factory Firewall, כך שזו לא קפיצה לחלל.

**למה 4 ולא 3:** Maestro קונה throughput אבל לא מזיז את המדד שהלקוח מודד בארון. השניים משלימים: Boost הוא scale-up בקופסה, Maestro הוא scale-out, ו-SGM עם Boost הוא Maestro חזק יותר.

**למה FPGA נפסלה בפירוט:** היא הפשרה המתבקשת, אבל היא מחייבת צוות HDL שאינו ב-DNA של Check Point, timing closure לכל engine חדש (כלומר פגיעה בדיוק בציר שבו אנחנו מנצחים), ועדיין משאירה פער תפוקה מול ASIC. הרקע האיכותי ב-`07-architecture-deep-dive.md` (סיבה 3).

## כלל מיצוב ל-deck ולדיבור

להציג תמיד כ**הצעה שלי**, לא כמוצר שאושר:

- "ההצעה: Quantum Boost", "אני מציע כרטיס DPU בחריץ ההרחבה", "אחרי · ההצעה".
- זמן עתיד בתיאור הזרימה: "ה-packet ייכנס לכרטיס", "נגיע ל-4 מתוך 5 שלבים בסיליקון".
- כל מספר שאינו נמדד מסומן "יעד" (כולל מילוי מקווקו בגרפים).
- הבקשה בשקף 16 מנוסחת בגוף ראשון: "מה אני מבקש מהחדר הזה".

## מקורות

- Nvidia BlueField-3 datasheet · NVIDIA DOCA SDK
- Check Point AI Factory Firewall press release (BlueField בפועל)
- Check Point Appliance Comparison Chart 2026 (9800: 1RU, 1.85μs, TP 25 Gbps)
- FortiGate 6500F datasheet · Palo Alto PA-5450 datasheet
- NIST CMVP (FIPS 140-3) · `07-architecture-deep-dive.md`
