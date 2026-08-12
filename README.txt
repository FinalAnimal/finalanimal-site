# finalanimal.com — site files

A plain static site. No build step, no dependencies. Edit the HTML, push, it deploys.

## Files

- `index.html`   — hero landing page with looping background video
- `work.html`    — filterable portfolio grid with lightbox
- `about.html`   — biography and selected works
- `contact.html` — contact details
- `404.html`     — not-found page
- `style.css`    — shared variables and base styles
- `Assets/`      — media. See "Background video" below.

Everything the site needs to run is committed to this repo. There is no build
step and nothing is fetched at deploy time, so copying the repo contents to any
static host or VPS docroot is sufficient — the video comes with it.

## Hosting

The site is hosted on **Cloudflare Pages**, project `finalanimal-site`,
connected to the GitHub repo `FinalAnimal/finalanimal-site`.

  Production branch:      main
  Framework preset:       None
  Build command:          (none)
  Build output directory: /

Pushing to `main` triggers a deploy automatically. There is nothing to build —
Cloudflare serves the files exactly as they are in the repo.

Project URL:  https://finalanimal-site.pages.dev
Live site:    https://finalanimal.com  (and www)

## DNS — read this before changing anything

The domain is registered through Bluehost (registrar of record: Network Solutions)
but DNS is served by **Cloudflare**. Nameservers:

  cullen.ns.cloudflare.com
  donna.ns.cloudflare.com

IMPORTANT: in the Bluehost domain panel these must stay set to *custom*
nameservers. If the domain is switched back to "using default nameservers",
Bluehost reasserts ns1/ns2.bluehost.com and the site goes down — this is exactly
what happened on 31 July 2026, when finalanimal.com started serving a Bluehost
WordPress starter page instead of this site.

### Email lives on Bluehost — do not proxy it

conor@finalanimal.com is hosted on Bluehost, not Cloudflare. These records must
stay set to **DNS only** (grey cloud) in the Cloudflare dashboard. If any of them
are switched to Proxied, mail silently stops being delivered, because Cloudflare's
proxy only carries HTTP/HTTPS:

  MX      finalanimal.com    -> mail.finalanimal.com
  A       mail               -> 162.241.244.112
  A       webmail            -> 162.241.244.112
  CNAME   imap, pop, smtp    -> mail.finalanimal.com
  A       cpanel, whm, ftp, webdisk, autoconfig, autodiscover, cpcalendars, cpcontacts
  TXT     SPF                -> v=spf1 a mx include:websitewelcome.com ~all
  TXT     default._domainkey -> DKIM key

Only the apex (`finalanimal.com`) and `www` should be Proxied — those are the two
that point at Pages.

## Cloudflare settings worth knowing

- **Email Address Obfuscation** (Scrape Shield) is ON by default. It rewrites the
  `mailto:` link in the footer into an encoded `/cdn-cgi/l/email-protection` link
  that reads "[email protected]" until JavaScript decodes it. Turn it off if you
  want the plain address in the markup.
- Requests to `work.html` are 308-redirected to `/work`. Both work; Pages prefers
  the extensionless form.

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
  YouTube Studio -> select video -> Details -> More options
  -> Allow embedding -> Save

## Customising

Colours are CSS variables in style.css. The palette is bone/off-white on
near-black:

  --accent      #d8d4c8    warm bone, used for labels and accents
  --accent-dim  #8b877d    muted, for nav and secondary labels
  --text        #f2efe7    body text
  --bg          #080808    near-black

Fonts via Google Fonts (already linked):
  Cormorant Garamond — display/headings
  Inter              — labels, nav, small caps details

## Background video (homepage)

The landing page background is a muted, looping video. Files:

  Assets/GSTest_Small.mov     master, as exported — 1920x1080 60fps, 12.8 MB.
                              Not used by the site. Kept here as the source to
                              re-encode from.
  Assets/video/hero.mp4       what the page actually plays — 6.8 MB
  Assets/video/hero-poster.jpg  first frame, shown before the video paints

The markup is the <video> element at the top of <body> in index.html; the paths
are relative, so they survive being moved to any docroot unchanged. Note that
most hosts (Cloudflare Pages included) are case-sensitive: the folder is
`Assets`, capital A.

The .mov is deliberately NOT referenced by the page. QuickTime .mov does not
play in Firefox and is unreliable in some Chrome builds, so it is transcoded to
H.264 MP4. To re-encode after replacing the master:

  ffmpeg -i Assets/GSTest_Small.mov -an -sn -dn -r 30 \
    -c:v libx264 -profile:v high -pix_fmt yuv420p -crf 30 -preset slow \
    -movflags +faststart Assets/video/hero.mp4

  ffmpeg -i Assets/GSTest_Small.mov -frames:v 1 -q:v 4 Assets/video/hero-poster.jpg

  -an -sn -dn      strips audio and the timecode track — a background loop is
                   muted anyway, and the master's audio was uncompressed PCM
  +faststart       moves the index to the front so playback starts before the
                   whole file has downloaded
  -crf             quality knob, lower = better + bigger. This footage is
                   painterly and compresses badly; 30 is the sweet spot. VP9
                   WebM was tried and came out *larger* than the MP4, so the
                   site ships MP4 only.

Keep it under 25 MiB — that is Cloudflare Pages' per-file limit.

Legibility: the video sits under `.hero-scrim`, a dark gradient that keeps the
bone-coloured type readable over bright frames. The ENTER button carries its own
darker backdrop because it sits mid-screen where the scrim is thinnest. If you
swap in brighter footage, those are the two knobs to adjust.

Reduced motion: visitors with "reduce motion" set get the poster frame held
still instead of playback. Handled in the script block at the bottom of
index.html.

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
