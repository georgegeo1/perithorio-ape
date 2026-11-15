# Περιθώριο σύνδεσης ΑΠΕ · RES grid-connection pre-check (HEDNO / ΔΕΔΔΗΕ)

[Ελληνικά](#ελληνικά) · [English](#english)

---

## Ελληνικά

Εφαρμογή ενός αρχείου HTML που κάνει **προέλεγχο διαθεσιμότητας δικτύου** για σύνδεση σταθμού ΑΠΕ στο Δίκτυο Διανομής του ΔΕΔΔΗΕ. Δίνετε τις συντεταγμένες του αγροτεμαχίου και την ισχύ του σταθμού. Η εφαρμογή:

1. βρίσκει τα έργα ΑΠΕ γύρω από το αγροτεμάχιο στον γεωπληροφοριακό χάρτη της ΡΑΑΕΥ (geo.rae.gr),
2. τα εντοπίζει στο μητρώο αιτήσεων σύνδεσης του ΔΕΔΔΗΕ, για να βρει σε ποιον υποσταθμό (Υ/Σ), μετασχηματιστή ΥΤ/ΜΤ (Μ/Σ) και γραμμή ΜΤ είναι συνδεδεμένα,
3. ελέγχει σε αυτόν τον Μ/Σ το **θερμικό περιθώριο** και το **περιθώριο στάθμης βραχυκύκλωσης**, όπως τα δημοσιεύει ζωντανά ο ΔΕΔΔΗΕ, αφαιρώντας την ουρά αιτήσεων.

> ⚠️ Το αποτέλεσμα είναι **ενδεικτικό**. Δεν αποτελεί εργαλείο του ΔΕΔΔΗΕ ή της ΡΑΑΕΥ. Δεσμευτική είναι μόνο η Οριστική Προσφορά Σύνδεσης του ΔΕΔΔΗΕ.

### Χρήση

- **Τοπικά:** κατεβάστε το `index.html` και ανοίξτε το με διπλό κλικ σε Chrome, Edge ή Firefox. Δεν χρειάζεται εγκατάσταση ή διακομιστής.
- **Online:** ανοίξτε τη διεύθυνση GitHub Pages του αποθετηρίου (δείτε [Δημοσίευση](#δημοσίευση-github-pages)).
- Συντεταγμένες: WGS84 σε δεκαδικές μοίρες (`39.2950, 22.4170`), μοίρες-λεπτά-δεύτερα (`39°17'42"N 22°25'01"E`) ή ΕΓΣΑ87 (`X 352410, Y 4351870`). Μπορείτε και να κάνετε κλικ στον χάρτη.
- **Ρυθμίσεις:** πρακτικό όριο ΑΠΕ ανά γραμμή ΜΤ (10 MW), ακτίνα αναζήτησης Υ/Σ (30 km), ακτίνα για γειτονικά έργα ΡΑΑΕΥ (2 km), αφαίρεση ουράς (ναι/όχι).

### Πώς βγαίνει το αποτέλεσμα

| Βήμα | Τι γίνεται |
|---|---|
| Περιθώρια ΔΕΔΔΗΕ | Ζωντανή κλήση στο `apps.deddie.gr/WebAPE/services/views/margins` (≈450 Μ/Σ ΥΤ/ΜΤ): θερμικό περιθώριο (`TTHP`), περιθώριο βραχυκύκλωσης (`TPBK`), ένδειξη διαθεσιμότητας, δήμοι που εξυπηρετεί κάθε Υ/Σ. Αν η κλήση αποτύχει, χρησιμοποιείται ενσωματωμένο στιγμιότυπο (23/10/2026). |
| Κριτήρια | Σταθμός ισχύος P χωράει όταν θερμικό ≥ P και βραχυκύκλωση ≥ 1,1×P. Ο ίδιος συντελεστής 1,1 εφαρμόζεται στην ουρά. |
| Ουρά | Από το μητρώο αιτήσεων ΔΕΔΔΗΕ: εκκρεμείς αιτήσεις και αιτήσεις σε αναμονή μετά από γνωστοποίηση αδυναμίας σύνδεσης. Λαμβάνονται υπόψη και οι αδυναμίες των τελευταίων 18 μηνών και οι ΟΠΣ των τελευταίων 12 μηνών. |
| Γειτονικά έργα | Ζωντανή κλήση στο GeoServer WFS του geo.rae.gr για 7 επίπεδα (Φ/Β με άδεια/βεβαίωση, Φ/Β με εξαίρεση, Φ/Β με αποθήκευση 11Α/11Β, αιολικά, βιομάζα, ΜΥΗΕ) σε ακτίνα R γύρω από το σημείο. |
| Αντιστοίχιση | Κάθε έργο αναζητείται στο μητρώο ΔΕΔΔΗΕ με κανονικοποιημένη επωνυμία (κεφαλαία, χωρίς τόνους, χωρίς νομική μορφή), δήμο και ισχύ. Όταν ένας φορέας έχει πολλές αιτήσεις, κρατιέται ο Υ/Σ που είναι πλησιέστερα στο έργο. |
| Ψηφοφορία | Κάθε έργο που βρέθηκε ψηφίζει τον Υ/Σ/Μ/Σ του, με βάρος = βεβαιότητα ταύτισης × e^(−απόσταση/1,5 km) × βάρος κατάστασης (λειτουργία/σύμβαση 1, ακυρωμένη 0,6). Πρώτα επιλέγεται ο Υ/Σ και μετά ο Μ/Σ μέσα σε αυτόν. Εμπιστοσύνη: υψηλή, μέτρια ή χαμηλή. |
| Απόφαση | Με υψηλή ή μέτρια εμπιστοσύνη ο έλεγχος γίνεται στον Μ/Σ των γειτονικών έργων. Αλλιώς στον πλησιέστερο Υ/Σ από όσους εξυπηρετούν τον δήμο, στον Μ/Σ με το μεγαλύτερο περιθώριο. |

**Δεν ελέγχονται:** ανύψωση τάσης στη γραμμή ΜΤ, περιορισμοί του Συστήματος (ΑΔΜΗΕ), ο τοπικός Μ/Σ ΜΤ/ΧΤ για σταθμούς Χαμηλής Τάσης. Στα Μη Διασυνδεδεμένα Νησιά ο ΔΕΔΔΗΕ δεν δημοσιεύει περιθώρια.

### Πηγές δεδομένων

| Πηγή | Χρήση | Πώς φορτώνεται |
|---|---|---|
| ΔΕΔΔΗΕ – Περιθώρια ΑΠΕ (WebAPE) | θερμικό/βραχυκύκλωση ανά Μ/Σ | ζωντανά· στιγμιότυπο ως εφεδρεία |
| ΔΕΔΔΗΕ – Μητρώο αιτήσεων σύνδεσης ΑΠΕ (Excel, Οκτώβριος 2026) | ουρά, ιστορικό αδυναμιών, Υ/Σ/Μ/Σ/γραμμή κάθε αίτησης | ενσωματωμένο |
| ΡΑΑΕΥ – geo.rae.gr (GeoServer WFS) | έργα ΑΠΕ γύρω από το αγροτεμάχιο | ζωντανά |
| geoBoundaries (ADM3) | όρια δήμων | ενσωματωμένο |
| Natural Earth, GeoNames/Wikidata | δρόμοι, νερά, οικισμοί για τον χάρτη | ενσωματωμένο |
| Leaflet 1.9.4 | χάρτης | cdnjs (CSS ενσωματωμένο) |

**Ζωντανή σύνδεση:** οι κλήσεις στον ΔΕΔΔΗΕ και στο geo.rae.gr γίνονται από τον browser του χρήστη. Δουλεύουν όταν το αρχείο ανοίγει τοπικά ή από δικό σας site (π.χ. GitHub Pages). Περιβάλλοντα με αυστηρό Content-Security-Policy (π.χ. προεπισκόπηση μέσα σε άλλη εφαρμογή) τις μπλοκάρουν. Τότε η εφαρμογή χρησιμοποιεί το στιγμιότυπο και το δείχνει.

### Δημοσίευση (GitHub Pages)

Settings → Pages → Source: *Deploy from a branch* → Branch: `main` / `/ (root)` → Save. Μετά από 1–2 λεπτά η εφαρμογή είναι στο `https://<όνομα-χρήστη>.github.io/<αποθετήριο>/`.



---

## English

A single-file HTML application that **pre-checks grid hosting capacity** for connecting a renewable (RES) plant to the Greek distribution network operated by HEDNO (ΔΕΔΔΗΕ). You enter the plot coordinates and the plant capacity. The app:

1. finds the RES projects around the plot on the RAAEY (Greek energy regulator) geoportal, geo.rae.gr,
2. looks them up in HEDNO's register of connection applications to learn which HV/MV substation, HV/MV transformer and MV feeder they are connected to,
3. checks that transformer's **thermal margin** and **short-circuit-level margin**, as published live by HEDNO, after subtracting the queue of pending applications.

> ⚠️ Results are **indicative only**. This is not a HEDNO or RAAEY tool. Only HEDNO's Final Connection Offer is binding.

### Usage

- **Locally:** download `index.html` and double-click it (Chrome, Edge or Firefox). No installation or server needed.
- **Online:** open the repository's GitHub Pages address (see [Deployment](#deployment-github-pages)).
- Coordinates: WGS84 decimal degrees (`39.2950, 22.4170`), degrees-minutes-seconds (`39°17'42"N 22°25'01"E`) or Greek Grid ΕΓΣΑ87 / EPSG:2100 (`X 352410, Y 4351870`). You can also click on the map.
- **Settings:** practical RES limit per MV feeder (10 MW), substation search radius (30 km), radius for nearby RAAEY projects (2 km), subtract queue (on/off).

### How the result is produced

| Step | What happens |
|---|---|
| HEDNO margins | Live request to `apps.deddie.gr/WebAPE/services/views/margins` (~450 HV/MV transformers): thermal margin (`TTHP`), short-circuit margin (`TPBK`), availability flag, municipalities served by each substation. If the request fails, an embedded snapshot (23 Oct 2026) is used. |
| Criteria | A plant of capacity P fits when thermal ≥ P and short-circuit ≥ 1.1×P. The same 1.1 factor is applied to the queue. |
| Queue | From HEDNO's application register: pending applications plus applications waiting after an "inability to connect" notice. Refusals in the last 18 months and offers in the last 12 months are also taken into account. |
| Nearby projects | Live requests to the geo.rae.gr GeoServer WFS for 7 layers (licensed/certified PV, exempted PV, PV + storage 11A/11B, wind, biomass, small hydro) within radius R of the plot. |
| Matching | Each project is looked up in the HEDNO register by normalised company/owner name (upper-case, no accents, legal forms removed), municipality and capacity. When an owner has several applications, the one whose substation is closest to the project is kept. |
| Vote | Each matched project votes for its substation/transformer, weighted by match confidence × e^(−distance/1.5 km) × status weight (operating/contract 1, cancelled 0.6). The substation is chosen first, then the transformer within it. Confidence: high, medium or low. |
| Decision | With high or medium confidence the check is run on the transformer used by the nearby projects. Otherwise the nearest substation serving the municipality is used, with its transformer that has the most margin. |

**Not checked:** voltage rise on the MV feeder, transmission-system (IPTO / ΑΔΜΗΕ) constraints, and the local MV/LV transformer for low-voltage plants. HEDNO publishes no margins for the non-interconnected islands.

### Data sources

| Source | Used for | Loaded |
|---|---|---|
| HEDNO – RES margins (WebAPE) | thermal / short-circuit margin per transformer | live, snapshot fallback |
| HEDNO – RES connection-application register (Excel, Oct 2026) | queue, refusal history, substation/transformer/feeder of each application | embedded |
| RAAEY – geo.rae.gr (GeoServer WFS) | RES projects around the plot | live |
| geoBoundaries (ADM3) | municipality boundaries | embedded |
| Natural Earth, GeoNames/Wikidata | roads, water, places for the map | embedded |
| Leaflet 1.9.4 | map | cdnjs (CSS embedded) |

**Live data:** the requests to HEDNO and geo.rae.gr are made from the user's browser. They work when the file is opened locally or served from your own site (e.g. GitHub Pages). Environments with a strict Content-Security-Policy (e.g. previews inside another app) block them. The app then falls back to the snapshot and says so.

### Deployment (GitHub Pages)

Settings → Pages → Source: *Deploy from a branch* → Branch: `main` / `/ (root)` → Save. After 1–2 minutes the app is at `https://<username>.github.io/<repository>/`.



