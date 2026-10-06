# Ask Berlin

Live, hands-on demo of **Natural Language Queries in the Mapbox Search Box API** (public preview),
built for the Mapbox Customer Advisory Board in Berlin, October 2026.

Type a question the way a person would say it ("sushi in Friedrichshain open now",
"Apotheke in der Nähe vom Hauptbahnhof Berlin", "pizza in Chicago"). The page makes exactly one
`GET /search/searchbox/v1/forward` call, shows the ranked places on a map, the measured latency,
and the request itself. Clicking a result fetches Place Details (`/retrieve`) to show the
hours, rating and amenities behind filters like "open now".

- **Demo:** `index.html`
- **Live wall for the projector:** `wall.html` (needs the optional Firebase config below)

## How it works

Everything is a single static page; no build step.

| What | Where |
|---|---|
| Mapbox token | Never in the repo. The `MAPBOX_TOKEN` repository secret is injected into `index.html` by `.github/workflows/pages.yml` on every deploy. Use a public token whose allowed URL is this site. Rotate with `gh secret set MAPBOX_TOKEN` and re-run the workflow. |
| Venue / proximity point | `VENUE` in `index.html` (Pressehaus Podium, Karl-Liebknecht-Str. 29A). "Near me" means near this point. |
| Example queries | `GROUPS` in `index.html`. Every example was verified against the public preview from this venue on 6 Oct 2026. |
| Filters | "Open now" adds `open_now=true`; "4★ and up" adds `minimum_rating=4`; EN/DE sets `language`. |
| Deep link | `index.html?q=pizza%20in%20Chicago` runs a query on load (`&lang=de` for German). |

## Optional: the live query wall

The wall aggregates every attendee's queries, latency and 👍/👎 votes in real time. It uses a
Firebase Realtime Database (free tier). Without it, the demo works unchanged and the wall link
stays hidden.

1. Go to <https://console.firebase.google.com>, **Add project** (any name, Analytics off).
2. **Build → Realtime Database → Create database**, location `europe-west1`, start in **locked mode**.
3. In the **Rules** tab paste the contents of `database.rules.json` and publish.
4. **Project settings → Your apps → Web (</>)**, register an app, copy the `firebaseConfig` object.
5. Copy `firebase-config.example.js` to `firebase-config.js`, paste the values, commit and push.

The rules allow anyone to append a query record (max 256 chars) and read the feed. Delete the
database or tighten the rules after the event.

## Query tips learned while testing

- Add the city when the place name is ambiguous. "pharmacies near Hauptbahnhof" resolved to
  Stuttgart; "Apotheke in der Nähe vom Hauptbahnhof Berlin" is right.
- German works for category + neighbourhood ("Sushi in Friedrichshain") but location resolution is
  weaker than English for phrasings like "am Potsdamer Platz" or "jetzt geöffnet".
- Reasoning-style asks ("best … within a 15 minute walk", "plan me a day") return the places the
  API can resolve but no reasoning. That is a separate roadmap item, and the page says so.
