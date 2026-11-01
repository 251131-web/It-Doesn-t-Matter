# It Doesn't Matter | R0 Hosting Edition

This is the free Netlify version of the browser-based experimental film. It uses a single service: Netlify Free for both the HTML/JS interface and MP4 files. No Bunny subscription, paid CDN, credit card, domain name, build framework, or API keys are required. You still need your own video files.

## Quick installation

1. Open `videos/ADD_YOUR_MP4_FILES_HERE.txt` and copy the **20 real video files** into this package's `videos/` folder. All MP4 names are case-sensitive and must match the filenames in `index.html`.
2. Push `index.html`, `netlify.toml`, `_headers`, and the entire `videos/` folder to the *root* of your existing GitHub repository.
3. Sign up for **Netlify Free** at https://app.netlify.com/ . Choose **Add new project > Import an existing project > GitHub**, grant repository access, and select your project.
4. The `netlify.toml` file already sets `publish = "."`. For this plain HTML project, leave the build command empty; there are no dependencies to install.
5. Publish. Open the resulting `https://YOUR-SITE.netlify.app/` address on a phone. You do not need to purchase a domain.
6. Test `https://YOUR-SITE.netlify.app/videos/memory1.mp4` by itself. If it returns a 404, the file path or capitalization is wrong.

**Alternative:** You can deploy using Netlify CLI (`netlify deploy --prod --dir=.`) if you prefer not to connect GitHub. Avoid drag-and-drop for a large package: Netlify notes that its browser Drop uploader works best below 50 MB total, and individual files over 10 MB may hang.

## What the player improves

- Buffers a logo and a randomly selected narrative clip while the title screen is visible, subject to browser preload restrictions.
- Plays each scene on one video element while preparing the next on the other.
- Checks that the upcoming clip has a few seconds buffered before making the crossfade; if not, it holds/loops the visible clip and tries again.
- Preserves all dimensions, random cuts, glitch chance, intro, credits, and a three-minute timer beginning when the first main film scene starts.
- Uses **local relative MP4 paths** (`videos/...`) and needs NO external media hostname.
- Caches MP4 files for one day in viewers' browsers to limit repeat downloads; Netlify CDN has its own cache.

## Important caveats

- No hosting site can force a viewer's mobile browser to preload all 20 files. Cellular data, device memory and iOS audio policies may still cause stalls or require a second tap for sound.
- The 5–12-second cuts are a *target*: on a slow connection the film intentionally delays a cut instead of fading into a black loading screen.
- After the browser cache expires, people will fetch MP4 files again; streaming still consumes some bandwidth credits.
- Netlify Free uses **300 credits/month**. As of October 2026, each production deployment uses 15 credits and each GB of bandwidth uses 20 credits, plus a small amount for web requests. The free plan never bills overages, but your site pauses if credits are exhausted until they reset. Check https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/ for current rates.
- **Do not use Netlify Large Media / Git LFS** for these videos. Netlify's own documentation says Large Media is not intended for streamed audio/video. Commit normal MP4 files to the repository as long as your Git repository limits allow.

## Check byte-range video serving

To test the actual site using a terminal:

```bash
curl -sS -o /dev/null -D - -H 'Range: bytes=0-1023' \
  https://YOUR-SITE.netlify.app/videos/memory1.mp4
```

A correctly handled range request returns `206 Partial Content` and a `Content-Range` header.

## Video export tip (optional)

For reliable mobile viewing, aim for smaller-than-25-MB clips, where the visual aesthetic allows. Use web-playable H.264 video / AAC audio / MP4 with moov atom at the beginning (`faststart`). You can remux an existing compatible clip without re-encoding:

```bash
ffmpeg -i input.mp4 -c copy -movflags +faststart optimized.mp4
```

## Folder structure

```
index.html
netlify.toml
_headers
videos/
  OW-FV-LOGO.mp4
  CREDITS.mp4
  memory1.mp4 ... memory6.mp4
  nightmare1.mp4 ... nightmare3.mp4
  dream1.mp4 ... dream6.mp4
  void1.mp4 ... void3.mp4
```

The downloadable package does not contain your own MP4 files because they were not uploaded to this conversation. Your repository already contains them.
