# finalanimal.com — site files

Three files. Drop them in a folder, push to GitHub, connect to Netlify.

## Files

- `index.html` — hero landing page with animated point cloud canvas
- `work.html`  — filterable portfolio grid with lightbox
- `style.css`  — shared variables and base styles

## Deploying to Netlify (free tier)

1. Create a free account at netlify.com
2. Drag and drop this folder onto the Netlify dashboard, OR
3. Push to a GitHub repo → New site from Git → select repo → Deploy
4. Point your domain: Netlify dashboard → Domain settings → Add custom domain → enter finalanimal.com
5. In your domain registrar (Bluehost or wherever the domain lives), change the nameservers to Netlify's:
   - dns1.p01.nsone.net
   - dns2.p01.nsone.net
   - dns3.p01.nsone.net
   - dns4.p01.nsone.net
   SSL is automatic (Let's Encrypt). No Bluehost hosting needed.

## Adding your own images

In work.html, each .grid-item has a .grid-placeholder div.
Replace it with a real image:

  <!-- BEFORE -->
  <div class="grid-placeholder"> ... </div>

  <!-- AFTER -->
  <img src="images/funeral-for-ashes-01.jpg" alt="Funeral for Ashes">

Put your images in an /images/ subfolder alongside these files.
Recommended: compress to ~300KB per image (use squoosh.app).

## Adding YouTube private video embeds

Each .grid-item has a data-video="" attribute.
Add the YouTube video ID (the part after ?v= in the URL):

  data-video="dQw4w9WgXcQ"

The lightbox will embed it automatically using youtube-nocookie.com
(works with private/unlisted videos when the viewer is logged in to
the correct Google account, or you've enabled embeds for the video).

To enable embedding for a private YouTube video:
  YouTube Studio → select video → Details → More options
  → Allow embedding → Save

## Customising

Colours are CSS variables in style.css:
  --accent      #3aff6a    the luminous green
  --accent-dim  #2a7040    muted green for nav/labels
  --bg          #050a06    near-black with green tint

Fonts via Google Fonts (already linked):
  Cormorant Garamond — display/headings
  Share Tech Mono    — labels, nav, monospace details

Point cloud tree on the homepage:
  All canvas animation is in the <script> block at the bottom of index.html.
  Adjust branch depth (7), spread angles, and animation speed (t += .008) to taste.
  When you have a cables.gl scene ready, replace the <canvas> element with
  the cables.gl embed iframe and delete the canvas script block.

## Adding more portfolio pieces

Copy any .grid-item block in work.html and update:
  data-cat="gallery|av|electronics"
  data-title="Work title"
  data-cat-label="DISPLAY CATEGORY"
  data-year="2025"
  data-desc="Description shown in lightbox."
  data-video="youtubeID"  (optional)

Size variants (add as a class on .grid-item):
  .tall   — 2:3 portrait
  .wide   — 16:9 landscape
  .square — 1:1 (default)
