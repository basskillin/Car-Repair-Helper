# AD Auto VIN Scan (Android)

Scan a VIN → alignment/chassis info first, then specs, readings, recalls, engine, safety, links,
notes, full decode, and the factory service manual (charm.li).

## Build
1. Android Studio (Ladybug 2024.2 or newer) → **Open** → this `VinScan` folder.
2. Let Gradle sync (downloads Gradle 8.9 + libraries the first time).
3. Plug in the phone (USB debugging on) → ▶ Run.  
   APK only: **Build → Build App Bundle(s)/APK(s) → Build APK(s)** → `app/build/outputs/apk/debug/app-debug.apk`.

Min Android 8.0 (API 26). Scanning is on-device (ML Kit), works without signal. Decode/recalls/manuals need data.

## Files (app/src/main/java/repair/adauto/vinscan)
| File | What it does |
|---|---|
| `Vin.kt` | VIN clean-up, import-prefix strip, check digit, OCR VIN extraction |
| `ScanScreen.kt` | CameraX + ML Kit: barcode mode (Code 39 / 128 / Data Matrix / QR / PDF417) and printed-text mode, flashlight |
| `HomeScreen.kt` | Scan buttons, VIN from photo, typed VIN, recent scans |
| `VehicleScreen.kt` | Everything shown for a VIN. **Add spec fields in `SPEC_FIELDS`** – they save automatically |
| `ManualScreen.kt` | charm.li viewer – opens Make/Year, highlights best model match, page filter |
| `Nhtsa.kt` | NHTSA vPIC decode + recalls API |
| `Db.kt` | Room database: vehicles (cached decode + notes), specs per year/make/model/drive, readings per VIN |

## Notes
- Specs/readings are stored as JSON maps, so new fields need no DB migration.
- If you change an `@Entity`, bump `version` in `ShopDb` (it currently wipes on version change –
  swap `fallbackToDestructiveMigration()` for a real migration once you have data you care about).
- charm.li covers ~1982–2013. If a make folder isn't found (e.g. RAM, filed under Dodge) it falls back to the home page with the make highlighted.
