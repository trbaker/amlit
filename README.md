# The Road West — a StoryMap-style app for *The Grapes of Wrath*

A single-page, scroll-driven literary geography app for the American literature classroom.
Built with the **ArcGIS Maps SDK for JavaScript (4.30)**. All geography is drawn client-side
as a **`GraphicsLayer`** — no hosted feature services, no ArcGIS Online item, no sign-in.

- Scroll the story panel → the camera flies to each stop and the red route line extends.
- Click any numbered marker on the map → the story scrolls to that stop.
- Three question banks (What's where? / Why is it there? / What's the larger relationship?) with
  checkboxes and a **Copy selected questions** button for building handouts and exit tickets.

## Files

```
index.html                 the whole app (map, story, question banks)
data/joad-route.geojson    the same geometry + attributes, standalone
```

`index.html` has no build step and no dependencies to install. The GeoJSON is a bonus: drag it into
ArcGIS Online's Map Viewer and you get the same route and stops as a hosted layer, with the
questions carried in the attribute table.

## Deploy to GitHub Pages (about 3 minutes)

1. Create a new repository (public).
2. Upload `index.html` and the `data/` folder to the root of the `main` branch.
3. **Settings → Pages → Build and deployment → Source: Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. Wait ~1 minute. Your app is at `https://<your-username>.github.io/<repo-name>/`.

That's it. The ArcGIS SDK loads from Esri's CDN, so nothing else needs hosting.

## Basemaps: no key needed, but a key looks better

Out of the box the app uses the **OpenStreetMap** basemap, which requires no key and no account.

To use Esri's basemaps instead (the topographic one is lovely for this — it shows relief, which
makes the Tehachapi Pass and the Mojave read instantly), get a free **API key** from an
[ArcGIS Location Platform](https://location.arcgis.com/) account and paste it near the top of the
`<script>` block in `index.html`:

```js
const CONFIG = {
  apiKey: "AAPK…your key here…",
  esriBasemap: "arcgis/topographic",   // try also arcgis/imagery, arcgis/terrain
  fallbackBasemap: "osm"
};
```

An API key is *not* a username and password — it's a scoped, revocable string that's safe to put in
a public client-side app. Restrict it to your GitHub Pages domain in the Location Platform dashboard
and set it to basemap access only.

## How the graphics layer works

Three `GraphicsLayer`s, all built in the browser from the `PATH` and `STOPS` arrays in `index.html`:

| Layer | Geometry | Symbol |
|---|---|---|
| `routeLayer` | one polyline, the full generalized U.S. 66 alignment | dashed umber — the road not yet driven |
| `traveledLayer` | one polyline, rebuilt on every scroll from `PATH.slice(0, stop.pi + 1)` | solid atlas red — miles behind them |
| `stopLayer` | 13 points + 13 text labels | cream circles, red when active, with popups |

Each stop object carries a `pi` (an index into `PATH`), which is what lets the traveled line grow to
exactly the right place. If you move a stop, update its `pi`.

## Adapting it to another novel

Everything book-specific lives in three places in `index.html`:

1. **`PATH`** — an array of `[longitude, latitude]` pairs tracing the route. For a place-rooted novel
   rather than a journey (say, *The House on Mango Street* or *Their Eyes Were Watching God*), you can
   delete the polylines entirely and keep only the point graphics.
2. **`STOPS`** — one object per stop: `pi`, `zoom`, `miles`, `name`, `region`, `ch`, `text`, and the
   three questions `q1`, `q2`, `q3`.
3. **The question banks** — the three `<details class="qgroup">` blocks in the panel. Each checkbox
   needs a unique `id` and a `data-g` of `1`, `2`, or `3` so the copy button can sort it.

The title, subtitle, intro, and closing sections are plain HTML — rewrite them and the design carries over.

## Accuracy notes for the classroom

- The route is a **generalized** U.S. 66 alignment, good for teaching, not for navigation.
- Mileages are approximate cumulative distances from Sallisaw.
- Sallisaw, Bethany, Santa Rosa, Needles, Daggett, Bakersfield, and the Arvin/Weedpatch camp are
  **real places named or clearly implied in the novel**.
- The peach ranch, the boxcar camp, and the final barn are **fictional**; they are placed at their
  approximate Tulare-basin setting and labeled as such in the app and in the GeoJSON.
- Steinbeck never dates the novel precisely; it is set in the mid-to-late 1930s.

## License / credits

Novel text and plot: John Steinbeck, *The Grapes of Wrath* (1939) — summarized and paraphrased here,
not quoted. Basemaps © Esri or © OpenStreetMap contributors, depending on your configuration.
The question framework follows the geographic inquiry tradition of *what is where, why there, why care?*
