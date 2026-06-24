# ARKA — Tierrechte-Kalender Karlsruhe

A one-page calendar view of animal-rights events in and around Karlsruhe, so local
organizers can see what's already planned and schedule new actions around it.

Events come from the public [animalrightscalendar.com](https://animalrightscalendar.com)
ICS feed (Karlsruhe, 10 km radius). It's currently an early prototype.

## Run it

It's a single self-contained `index.html` — no build step.

- Double-click `index.html`, or
- serve the folder: `python3 -m http.server` then open <http://localhost:8000>

An internet connection is needed at view time for the Tailwind and Inter CDNs; the
event data itself is local.

## Data

The `/cal` endpoint returns an iCalendar (ICS) feed. A direct browser `fetch()` is
CORS-blocked, so a snapshot is embedded inline in `index.html` and parsed in the page.
`cal.ics` is that snapshot, kept as the source for rebuilds — the in-page parser is the
same code path that will work against the live feed once it's served same-origin.

Refresh the snapshot:

```sh
curl -s "https://animalrightscalendar.com/cal?coordinates=8.40444%2C49.00937&city=Karlsruhe&timezone=Europe%2FBerlin&radius=10&showOnlineEvents=false" -o cal.ics
```

then re-inject it into `index.html` (the snapshot lives in a `<script type="text/plain" id="ics">` block).

## Organisations

Events are color-coded by organisation. The feed has no `ORGANIZER` field, so the org is
inferred from each event's title / URL / description (`classifyOrg()` in `index.html`).
This is reliable for the current feed but heuristic — the clean fix is to add the standard
iCalendar `ORGANIZER` property to the feed and read it directly.

| Organisation | Color |
| --- | --- |
| PETA Streetteam | blue |
| Anonymous for the Voiceless | black |
| Activists for the Victims | amber |
| We The Free | hot pink |
| NEON Karlsruhe | red |
| Other | violet |

## Notes

- Times are shown in `Europe/Berlin`; the feed is UTC.
- Desktop shows a month grid; mobile collapses to a day-grouped agenda.
- Duplicate events (same start time + title) are removed.
