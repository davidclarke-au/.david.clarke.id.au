# david-clarke.id.au

A single self-contained `index.html` — no build step, no dependencies to install, no framework. Open it directly in a browser or drop it into GitHub Pages.

## How it works

- **Everything is in one file.** HTML, CSS, and JavaScript all live in `index.html`.
- **Two external things load over the network:**
  - Google Fonts (Saira Condensed / Inter / JetBrains Mono) via a CDN link.
  - The photos themselves, fetched from `https://david-clarke.id.au/images/`.
- **The gallery** is an infinite vertical scroll through a list of photos. Each one has a caption (title + optional sub-line), lights up its position on the left-hand tick rail, and updates the counter bottom-right.
- **Idle behaviour:** after 5 seconds with no input (mouse, touch, scroll, or keyboard), the page fades into a slow "screensaver" crossfade through the same photo list, and hands control straight back the moment you touch anything.
- **Keyboard:** Arrow Up/Down, Page Up/Down, and Space jump one photo at a time.
- There's also a small fullscreen toggle button near the top-right menu.

## Adding a new photo

1. Add the image file to the `images/` folder in the repo — e.g. `images/bg22.jpg`.
2. Open `index.html` and find the `SHOTS` array near the top of the `<script>` block. It looks like this:

   ```js
   var SHOTS = [
     ["bg01.jpg","Desmosedici","Ducati Corse · MotoGP"],
     ["bg02.jpg","Corse","Ducati · Livery"],
     ...
   ];
   ```

3. Add a new line in the same `[ "filename.jpg", "Title", "Sub-line" ]` format:

   ```js
   ["bg22.jpg","Race Day","Phillip Island"],
   ```

   - **Filename** must match exactly what you put in `images/`.
   - **Title** is required — it's the big text on the photo.
   - **Sub-line** is optional. Leave it as `""` (or drop the third item entirely) if you don't want one.
4. The array's order is the scroll order — insert your new line wherever you want the photo to appear in the sequence.
5. Commit and push (see the deployment steps in chat, or the summary below).

That's the entire process — nothing else in the file needs to change. The counter, tick rail, and idle slideshow all pick up new entries automatically since they just read from `SHOTS.length`.

## Keeping photos fast

- Compress before adding — aim for roughly **200–400KB** per photo. ~2000px on the long edge is plenty for full-screen display; anything larger is wasted bandwidth.
- One current photo, `bg03.jpg`, is ~19MB and is worth shrinking — it's the single biggest thing slowing the gallery down.
- Quick compress with ImageMagick: `magick bg22.jpg -resize 2000x2000\> -quality 82 bg22.jpg`. Any "export for web" option in a photo editor works too.

## Local preview

Just open `index.html` directly in a browser — no server needed. One thing to know: the script currently loads images from the **live production URL**, not the repo's local `images/` folder, so a photo you've just added won't actually appear in local preview until it's pushed live. If you'd rather test locally before pushing, change this line near the top of the script:

```js
var BASE = "https://david-clarke.id.au/images/";
```
to:
```js
var BASE = "images/";
```

## Deploying a change

1. Replace the repo's `index.html` with your updated copy (and add any new images to `images/`).
2. `git add -A && git commit -m "Update gallery" && git push origin main`
3. Give Cloudflare/GitHub Pages a minute, then reload the site — hard-refresh (Cmd/Ctrl+Shift+R) if you still see the old version.

## Notes

- `ct.html` / `ct2.html` (the canary tokens) are separate from all of this and don't need any changes.
- If Google Fonts or the image host is ever unreachable, the page still renders — it just falls back to system fonts, and any unreachable photo shows as blank in that one frame.
