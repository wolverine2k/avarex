# AvareX

Avare, written in Flutter. Runs on Linux, Windows, MacOS, iOS, Android, and Raspberry Pi.

AvareX is a pilot's all in one electronic flight bag solution.

By Apps4Av.

## User Manual

A comprehensive, code-derived user manual is available at:

- [USER_MANUAL.md](USER_MANUAL.md)

## European data support

AvareX keeps its existing FAA-backed United States workflow and adds optional
European data providers. European data is downloaded on demand rather than
bundled in the application package.

### OpenFlightMaps

[OpenFlightMaps](https://openflightmaps.org/) provides the primary European VFR
chart and aeronautical-data integration:

- Georeferenced EPSG:3857 MBTiles for the offline moving map. Standard-resolution
  tiles are the default; larger `@2x` archives are optional.
- OFMX airport, runway, runway-end, communication, navaid, reporting-point, and
  airspace records.
- Regional PDF VFR chart sheets for offline reference. These documents are
  identified as not GPS-referenced and are not moving-map layers.
- Region and AIRAC-cycle selection, progress reporting, cancellation,
  replacement, and removal.
- Separate map controls for `OFM VFR Chart` and `OFM Interactive Data`.
- Source, region, cycle, attribution, and disclaimer information in search and
  destination details.

Only one cycle of a region should be active at a time. Missing raster tiles are
transparent so regional chart boundaries do not obscure other map content.

### openAIP

[openAIP](https://www.openaip.net/) is an optional supplementary provider for
European airports, navaids, VFR reporting points, airspace, and obstacles.
AvareX uses the authenticated openAIP Core API rather than embedding a shared
project credential.

To use it:

1. Create an openAIP account and personal API client.
2. In AvareX, open **Menu > Data > openAIP**.
3. Enter the personal API key and select **Test Connection**.
4. Enter a two-letter ISO country code, such as `SE`, `DE`, or `FR`.
5. Select **Download Country Data**.
6. Enable **openAIP Interactive Data** in the map-layer controls when its
   airspace display is wanted.

Credential and data handling:

- The API key is masked and stored with platform secure storage. It is not
  included in source code, application diagnostics, downloaded data, or the APK.
- The settings screen provides Save, Test Connection, Clear Key, country
  download, and country removal actions.
- API results are paginated and cached offline in a separate `openaip.db`.
- Downloaded airports and runways participate in search, nearby-airport lookup,
  runway-length filtering, and destination details.
- Navaids and reporting points participate in search, nearby lookup, route-point
  selection, and nearest-VOR lookup.
- Obstacles augment the existing obstacle layer when a usable top elevation is
  available. Records lacking sufficient elevation information are not used for
  altitude filtering.
- Airspace polygons include vertical limits and `BY NOTAM`/`ON REQUEST` labels.
- All openAIP results retain source and country provenance and display openAIP
  attribution.

openAIP data is licensed under
[CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/). It is
community-maintained supplementary data and must not be treated as certified or
as the sole source for primary navigation or flight planning. See the
[openAIP legal information](https://www.openaip.net/legal) and
[API documentation](https://docs.openaip.net/) before redistributing data or
shipping it in a commercial product.

### Official AIP charts (aip.aero)

For any covered European (and several nearby) airport, the destination details
provide a **Official AIP & approach charts** action that opens the airport's
page on [aip.aero](https://aip.aero/) in the platform browser. aip.aero is a
free index that links straight to the country's official national AIP
publication (VFR/IFR charts, aerodrome data).

- This is a link hand-off only. AvareX does not fetch, cache, or redistribute
  any chart PDF or AIP content; it merely builds and opens the correct URL.
- The ICAO identifier is mapped to the aip.aero country entry; where the
  airport deep link is not available the country landing page is used instead.
- The action is clearly marked as an external site and reminds the pilot to
  verify AIRAC currency before every flight.

This provides a legal, low-friction route to official plates and airport
diagrams without embedding third-party charts. It is not a georeferenced,
moving-map plate overlay.

### Decoded METAR with selectable VFR/IFR thresholds

Airport weather panels show, below the raw METAR, a **Decoded METAR** card that
translates the report into plain English (wind, visibility, ceiling, present
weather, temperature/dewpoint spread, and pressure) and color-codes each element
by threat level. A per-view **VFR/IFR** selector switches the threshold profile
so the same report is assessed against thresholds appropriate to the operation.

- Uses only the raw METAR AvareX already holds; no third-party service.
- The selected profile is remembered across sessions.
- Advisory only; it is not a substitute for an official weather briefing.

### Default map layers

On a fresh installation the Europe map layers — `OFM VFR Chart`,
`OFM Interactive Data`, and `openAIP Interactive Data` — are enabled by default
so installed European data is visible without first opening the layer controls.
These layers render nothing until the corresponding regional data is installed,
so United-States-only users are unaffected. Existing users keep their saved
layer choices; the default applies only to new installs.

### Winds aloft (Open-Meteo)

The built-in winds aloft come from NWS FB text products that only cover the
United States and its territories. Outside that coverage the destination Wind
tab retrieves pressure-level winds from [Open-Meteo](https://open-meteo.com/)
and converts them into the same altitude bands (surface through 39,000 ft) so
the display is identical.

- Selection is automatic: within US FB coverage the existing product is used;
  beyond it (Europe and the rest of the world) Open-Meteo is queried for the
  destination coordinate, with the US data as a last-resort fallback.
- The free Open-Meteo endpoint is used by default. A pilot who needs
  commercial-compliant access can enter a personal Open-Meteo API key under
  **Menu > Data > Open-Meteo Winds** (Save, Test Connection, Clear). The key is
  stored in platform secure storage and never embedded in the app.
- Winds are shown with `Winds © Open-Meteo.com, CC BY 4.0` attribution. They are
  forecast, advisory, and not a substitute for an official weather briefing.

Meteostat was evaluated as an additional surface-observation provider but is not
integrated: its JSON API requires a per-user paid RapidAPI key and largely
duplicates METAR/Open-Meteo coverage. It remains a possible future option for
historical/station surface observations.

### NOTAMs (FlyBrief, offline)

The built-in NOTAM source is a United States FAA API that returns nothing in
Europe. Where it has no coverage, the airport NOTAM tab falls back to
[FlyBrief](https://flybrief.app/) per-country, georeferenced NOTAM GeoJSON
(polygons with altitude bands, schedules and active-now flags).

- Selection is automatic: the built-in source is tried first; outside its
  coverage the FlyBrief country under the destination is used, filtered to
  NOTAMs near the point (active ones first).
- Offline-first: **Menu > Data > NOTAMs (FlyBrief)** downloads and stores the
  current country's NOTAMs (and obstacles) under `{dataDir}/flybrief/` so they
  are available without a connection. The tab loads the stored file when
  present and only fetches live when it is missing.
- ~28 European countries are covered. NOTAMs carry
  `NOTAMs © OpenAIP contributors & national AIS via FlyBrief (CC BY-NC-SA 4.0)`
  attribution and are advisory only — always confirm against the official
  national briefing.

### Not available in open form (documented, not faked)

Some parity items have no authoritative, machine-readable open source and are
deliberately **not** implemented rather than approximated with fragile scrapers:

- **SID/STAR and instrument procedures**: there is no open, machine-readable
  European equivalent of the FAA CIFP; this data is commercial/licensed.
- **Georeferenced approach plates / airport diagrams**: no uniform cross-country
  open catalog. AvareX links out to the official national AIP via aip.aero
  instead (see above); it does not store or overlay plates.
- **Terrain/elevation/GPWS outside the US**: the engine is geography-agnostic
  but depends on elevation tiles that are only distributed for the US today. A
  future option is transcoding open global DEM tiles (e.g. AWS Terrain Tiles)
  into the app's elevation-tile format.

### United States and European feature parity

"Partial" means the feature works with available community/open data but does
not have the same coverage, authority, or product depth as the FAA-backed US
implementation.

| Capability | United States | Europe | Parity |
| --- | --- | --- | --- |
| GPS moving map and direct-to navigation | Full | Full | Full |
| Offline VFR raster charts | FAA chart products | OFM regional MBTiles | High |
| Offline VFR PDF sheets | FAA documents | OFM reference sheets, not georeferenced | High |
| Airport search and nearby lookup | FAA database | OFM plus openAIP | High |
| Runway dimensions and surfaces | FAA database | OFM plus openAIP | High |
| Runway-end details and runway awareness | Full where FAA data exists | Available where source runway-end geometry exists | Partial |
| Minimum-runway-length filtering | Full | OFM plus openAIP runway lengths | High |
| Airport frequencies | FAA/NASR | OFMX services plus openAIP frequencies | Partial to high |
| Navaids | FAA/NASR | OFMX plus openAIP VOR/NDB/DME/TACAN data | High for covered countries |
| VFR reporting points and fixes | FAA fixes | OFMX designated points plus openAIP reporting points | High for VFR use |
| Airways and automatic IFR routing | FAA airway graph | No dependable Europe-wide open airway graph integrated | Low |
| SID, STAR, and instrument procedures | FAA CIFP | Not available from the integrated open sources | None |
| Approach plates and airport diagrams | FAA d-TPP products | Link-out to official national AIP via aip.aero (not georeferenced, not in-app overlay) | Partial |
| Airspace display | FAA airspace/SUA | OFM and optional openAIP polygons | High for static display |
| Airspace schedules and live activation | FAA products where available | Static metadata and `BY NOTAM` flags; no live activation feed | Partial |
| METAR and TAF | Aviation Weather Center | International AWC reports where stations are covered | High |
| Decoded METAR with VFR/IFR threat coloring | Plain-English decode with selectable profile | Same decode, geography-independent | Full |
| Radar mosaic | US NEXRAD | No European radar provider integrated | None |
| Winds aloft and graphical weather | US AWC/WPC products | Winds aloft from Open-Meteo pressure-level forecasts outside US coverage; no graphical products | Partial |
| NOTAMs and temporary restrictions | FAA-specific services | Per-country georeferenced NOTAMs via FlyBrief, stored for offline use | Partial |
| Terrain, elevation, and GPWS | US regional terrain packages | No European terrain package integrated | None |
| Obstacles | FAA obstacle data | Supplementary openAIP obstacles with incomplete-authority warning | Partial |
| ADS-B/GDL90 traffic and external GPS | Geography-independent | Geography-independent | Full |
| NMEA/autopilot output | Geography-independent | Geography-independent | Full |
| Logbook, checklists, aircraft, W&B, notes | Geography-independent | Geography-independent | Full |
| Flight-plan filing and briefing | Leidos/1800wxbrief | No European filing/briefing provider integrated | None |

European support is suitable for supplementary VFR situational awareness and
direct waypoint navigation when the required regional data is installed. It is
not equivalent to the US implementation for IFR procedures, legal preflight
briefing, NOTAMs, terrain clearance, weather radar, or filing. Pilots remain
responsible for obtaining current authoritative AIP, NOTAM, weather, and
procedure information from the applicable national and European services.

### Data-source boundaries

- FAA-backed US behavior remains unchanged and is preferred for FAA records.
- OFM is the primary European VFR chart/OFMX source.
- openAIP supplements gaps and remains independently attributable and removable.
- Provider data is stored separately instead of being inserted into the FAA
  database.
- Search and nearby queries merge providers while retaining provenance.
- Neither OFM nor openAIP data is represented as certified.

Known EU gaps and future options (not yet integrated): winds aloft outside US
coverage is now provided by Open-Meteo and European NOTAMs by FlyBrief (both
above). Meteostat surface observations, weather radar, terrain/GPWS outside the
US, and IFR procedures/plates as georeferenced overlays remain open (see "Not
available in open form").

## Getting Started

### Downloading


** Windows 

Download on Windows using Microsoft Store.

** MacOS

Download on Apple App Store from your Mac with Apple Silicon.

** Linux

Download on Linux using Snap Store.

** iOS

Download on Apple App Store from your iPhone or iPad.

** Android

Download on Google Play Store from your Android device.

** Raspberry Pi

Download at https://github.com/apps4av/avarex/actions/workflows/arm64.yaml from your Pi. 

Tested on 64-bit Raspberry Pi OS (may run on other configurations).
 - Pi 5 with 8 GB memory
 - Pi 4 with 1 GB memory
 - Prerequisites: sudo apt-get install libgtk-3-0 libblkid1 liblzma5 libsqlite3-dev

## Store Consoles

Google / Android: https://play.google.com/console

iOS, MacOS: https://appstoreconnect.apple.com/login

Linux: https://snapcraft.io

Windows: https://partner.microsoft.com/en-us/dashboard/home

## Store Locations

Google / Android : https://play.google.com/store/apps/details?id=com.apps4av.avaremp

iOS, MacOS: https://apps.apple.com/us/app/avarex/id6502421523

Linux: https://snapcraft.io/avarex

Windows: https://apps.microsoft.com/detail/9mx4hkl30mww?hl=en-us&gl=US

## Building:

Github Actions builds all store builds.

### AvareX-EU tagged releases

Pushing any Git tag runs `.github/workflows/avarex-eu-release.yaml`. The workflow
runs the Flutter tests and analyzer, builds the Android release APK, and
creates a GitHub release containing:

- `AvareX-EU-<tag>.apk`
- `AvareX-EU-<tag>-source.tar.gz`
- `AvareX-EU-<tag>-source.zip`
- `CHANGELOG-<tag>.md`

The changelist contains commits since the previous tag, is used as the GitHub
release description, and is also included inside both source archives as
`CHANGELOG-RELEASE.md`. The EU APK uses the release build configuration without
requiring the Play Store signing secrets; it is intended for direct installation
from the GitHub release. The workflow still uses the existing FAA-service and
RevenueCat repository secrets. It deliberately
builds with non-production Firebase placeholders, so no Firebase login or
Firebase service-account secret is required and Firebase-backed cloud features
are unavailable in the EU artifact. An openAIP API key is likewise not a build
secret; each user supplies and securely stores a personal key in the application.

Microsoft version scheme: pubspec.yaml (versions go like 1.0.9.0, last digit must be 0)

Apple version scheme: pubspec.yaml 0.0.9+9

Google version scheme: pubspec.yaml 0.0.9+9  (+9) is what shows up in the package)

Snap version scheme: snap/snapcraft.yaml 0.0.9


