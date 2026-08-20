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
| Approach plates and airport diagrams | FAA d-TPP products | No Europe-wide authoritative open equivalent integrated | None |
| Airspace display | FAA airspace/SUA | OFM and optional openAIP polygons | High for static display |
| Airspace schedules and live activation | FAA products where available | Static metadata and `BY NOTAM` flags; no live activation feed | Partial |
| METAR and TAF | Aviation Weather Center | International AWC reports where stations are covered | High |
| Radar mosaic | US NEXRAD | No European radar provider integrated | None |
| Winds aloft and graphical weather | US AWC/WPC products | No equivalent provider integrated | Low |
| NOTAMs and temporary restrictions | FAA-specific services | No authoritative Europe-wide open API integrated | None |
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
runs the Flutter tests and analyzer, builds and signs the Android release, and
creates a GitHub release containing:

- `AvareX-EU-<tag>.apk`
- `AvareX-EU-<tag>-source.tar.gz`
- `AvareX-EU-<tag>-source.zip`
- `CHANGELOG-<tag>.md`

The changelist contains commits since the previous tag, is used as the GitHub
release description, and is also included inside both source archives as
`CHANGELOG-RELEASE.md`. The release workflow requires the Android signing,
FAA-service, and RevenueCat repository secrets used by the existing Android
workflow. It deliberately
builds with non-production Firebase placeholders, so no Firebase login or
Firebase service-account secret is required and Firebase-backed cloud features
are unavailable in the EU artifact. An openAIP API key is likewise not a build
secret; each user supplies and securely stores a personal key in the application.

Microsoft version scheme: pubspec.yaml (versions go like 1.0.9.0, last digit must be 0)

Apple version scheme: pubspec.yaml 0.0.9+9

Google version scheme: pubspec.yaml 0.0.9+9  (+9) is what shows up in the package)

Snap version scheme: snap/snapcraft.yaml 0.0.9


