# dingus.win

This is the separate project for the main `dingus.win` website and the
`lidar.dingus.win` LiDAR explorer. The existing `forgh/` app is not modified
by this project.

## Project layout

- `website/` — main public landing website; deploy as its own Cloudflare Pages
  project to `dingus.win` (and optionally `www.dingus.win`).
- `lidar/` — copied LiDAR explorer; deploy as another Pages project to
  `lidar.dingus.win`.

The LiDAR app is configured for a **new Firebase project** and must not be
pointed at the old `dep-lidar-scan` project. No Firebase credentials or
database contents are included here.

## Build locally

From PowerShell:

```powershell
Set-Location C:\Users\C\Pictures\BuildingLocator\dingus-win\website
npm.cmd install
npm.cmd run dev
```

In another terminal:

```powershell
Set-Location C:\Users\C\Pictures\BuildingLocator\dingus-win\lidar
npm.cmd ci
npm.cmd test
npm.cmd run build
```

For the main website, also install dependencies once with
`npm.cmd install` before running its development server or build.

For the main website, use `npm run build` and `dist`. For the LiDAR site, use
the `lidar/` directory as the separate Pages project root, with build command
`npm run build` and output directory `dist`.

## Important: autonomous scanner is not deployed

The copied LiDAR site can scan an area when a visitor uses the browser. It
does **not** run continuously while visitors are away. A separate scheduled
scanner service still needs to be configured for Winter Haven/Polk County,
connected to the new Firebase project, and hosted on an always-on runner. Do
not treat the current site as an autonomous or live county-wide scan.

LiDAR can identify evidence-supported structure candidates, but imagery and
LiDAR cannot establish that a building is abandoned, vacant, unoccupied, or
available to enter. Candidate locations are leads for lawful, independent
verification only.

## Firebase setup for `lidar.dingus.win`

1. Create a new Firebase project and Realtime Database; do not select
   `dep-lidar-scan`.
2. Enable Anonymous and Google Authentication. Add `lidar.dingus.win` to the
   Firebase authorized domains.
3. Register a Web App and copy its public Web SDK configuration into the
   Cloudflare Pages environment variables listed in
   `lidar/.env.example`. The `VITE_` values are public browser configuration,
   not server secrets. Never put service-account keys or Cloudflare API tokens
   in this app.
4. Create the owner Google account and replace
   `REPLACE_WITH_OWNER_FIREBASE_UID` in `lidar/database.rules.json` with that
   account's Firebase Auth UID. Review the rules before publishing them.
5. Apply those rules to the new Realtime Database and deploy the LiDAR Pages
   project. Do not apply these rules to the old database.

The main `dingus.win` site is static and needs no Firebase configuration.
