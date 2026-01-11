# SMART on FHIR Tutorial (Repo Overview)

This repository contains two main pieces:
- A static tutorial site (Slate-generated) at the repo root.
- A runnable example SMART on FHIR app under `example-smart-app/`.

## Repo layout

Top-level tutorial site:
- `index.html`: The tutorial content (static HTML).
- `stylesheets/`: Slate CSS (`screen.css`, `print.css`).
- `javascripts/`: Slate JS bundles (`all.js`, `all_nosearch.js`) for nav/search UI.
- `images/`, `fonts/`: Tutorial assets.

Example SMART app:
- `example-smart-app/index.html`: App UI page and SMART redirect target.
- `example-smart-app/launch.html`: EHR launch entry point.
- `example-smart-app/launch-patient.html`: Patient standalone launch entry point.
- `example-smart-app/launch-smart-sandbox.html`: SMART Health IT sandbox launch entry point.
- `example-smart-app/src/js/example-smart-app.js`: App logic (FHIR reads, transforms, render).
- `example-smart-app/src/css/example-smart-app.css`: Minimal app styling and spinner.
- `example-smart-app/lib/js/`: FHIR client library and Cerner additions.
- `example-smart-app/lib/css/`: Optional embed CSS (currently commented out in HTML).

## App flow (launch -> OAuth -> data -> render)

1) Launch
- One of the launch pages calls `FHIR.oauth2.authorize(...)` with `client_id` and `scope`.
  - `example-smart-app/launch.html` for EHR launch.
  - `example-smart-app/launch-patient.html` for patient standalone launch.
  - `example-smart-app/launch-smart-sandbox.html` for SMART Health IT sandbox.

2) Authorization redirect
- The SMART authorization server redirects to `example-smart-app/index.html` with `code` and `state`.

3) SMART client ready
- `example-smart-app/index.html` loads:
  - `example-smart-app/lib/js/fhir-client-v0.1.12.js`
  - `example-smart-app/src/js/example-smart-app.js`
  - `example-smart-app/lib/js/fhir-client-cerner-additions-1.0.0.js`
- `example-smart-app/src/js/example-smart-app.js` runs `FHIR.oauth2.ready(...)`.

4) Data fetch and render
- The app reads `Patient` and fetches `Observation` resources with specific LOINC codes.
- Values are mapped into a plain object and rendered into the DOM by `drawVisualization(p)`.

## Customizing for another FHIR server

Update launch configuration:
- Edit one or more of:
  - `example-smart-app/launch.html`
  - `example-smart-app/launch-patient.html`
  - `example-smart-app/launch-smart-sandbox.html`
- Set `client_id` to your server's client ID.
- Adjust `scope` for your server's requirements.
- Add `redirect_uri` if your authorization server requires an explicit redirect URL.

Swap SMART client library (if needed):
- Replace `example-smart-app/lib/js/fhir-client-v0.1.12.js` with a newer version and adjust API usage.

## Customizing data elements and UI

Change which FHIR data is fetched:
- Update the `Observation` query in
  - `example-smart-app/src/js/example-smart-app.js`
- Modify the LOINC codes in `$or` or add new resource fetches (e.g., `Condition`, `MedicationRequest`).

Change how data is mapped:
- Extend `defaultPatient()` and the mapping in the `$.when(pt, obv).done(...)` block.

Change the UI:
- Edit the table and placeholder IDs in `example-smart-app/index.html`.
- Update the rendering logic in `example-smart-app/src/js/example-smart-app.js` (`drawVisualization(p)`).
- Adjust styling in `example-smart-app/src/css/example-smart-app.css`.
