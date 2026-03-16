---
name: remotion-server
version: "2.0.0"
description: Create professional videos from code with Remotion v5 - social media format presets, pre-built templates, thumbnail generation, batch rendering. Headless on any Linux server using Chrome Headless Shell. No Mac or GUI needed.
author: mvanhorn
license: MIT
repository: https://github.com/mvanhorn/clawdbot-skill-remotion-server
homepage: https://remotion.dev
metadata:
  openclaw:
    emoji: "\U0001F3AC"
    requires:
      bins:
        - node
        - npm
    tags:
      - video
      - remotion
      - rendering
      - react
      - headless
      - social-media
      - tiktok
      - youtube
      - instagram
      - templates
      - thumbnails
      - batch
      - mp4
      - webm
    triggers:
      - render a video
      - make a video
      - create a video
      - generate a video
      - tiktok video
      - youtube short
      - instagram reel
      - promo video
      - chat demo video
      - code walkthrough video
      - product demo video
      - generate a thumbnail
      - batch render
---

# Remotion Server

Render videos headlessly on any Linux server using Remotion v5. No Mac or GUI required.
Chrome Headless Shell handles all rendering - works on VPS, Raspberry Pi, CI runners, Docker containers, anywhere Node.js runs.

## Setup (one-time)

Install browser dependencies for Chrome Headless Shell:

```bash
bash {baseDir}/scripts/setup.sh
```

This installs system libraries (libnss3, libatk, libgbm, libcups2, etc.) required by Chrome on headless Linux.
Supports Ubuntu 22.04/24.04, Debian, and Amazon Linux.

### Verify setup

After setup, confirm Chrome Headless Shell works:

```bash
npx @remotion/chrome-headless-shell --help
```

If this fails, the system dependencies are missing. Re-run setup.sh or install manually:

```bash
# Ubuntu/Debian
sudo apt install -y libnss3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libgbm1 \
  libpango-1.0-0 libcairo2 libxcomposite1 libxdamage1 libxfixes3 libxrandr2

# Amazon Linux
sudo yum install -y mesa-libgbm libX11 libXrandr libdrm libXdamage libXfixes \
  libxkbcommon dbus-libs libXcomposite alsa-lib nss pango cups-libs at-spi2-core atk
```

## Quick Start

### Create a project

```bash
bash {baseDir}/scripts/create.sh my-video
cd my-video
```

### Render a video

```bash
npx remotion render MyComp out/video.mp4
```

### Preview in browser (dev mode)

```bash
npx remotion preview
```

Opens a local dev server where you can scrub through the video timeline, adjust props, and iterate before rendering.

## Social Media Format Presets

Use these resolution and aspect ratio presets when creating compositions. Pass `--width` and `--height` to `npx remotion render` to override the composition defaults.

### TikTok / YouTube Shorts / Instagram Reels (9:16 vertical)

```bash
npx remotion render ChatDemo out/tiktok.mp4 --width=1080 --height=1920
```

- Resolution: 1080x1920
- Aspect ratio: 9:16
- Recommended FPS: 30
- Max duration: 60s (TikTok), 60s (Shorts), 90s (Reels)

### YouTube Standard (16:9 landscape)

```bash
npx remotion render MyComp out/youtube.mp4 --width=1920 --height=1080
```

- Resolution: 1920x1080
- Aspect ratio: 16:9
- Recommended FPS: 30 or 60

### Instagram Post (1:1 square)

```bash
npx remotion render MyComp out/instagram-post.mp4 --width=1080 --height=1080
```

- Resolution: 1080x1080
- Aspect ratio: 1:1
- Max duration: 60s

### Twitter/X Video (16:9 landscape)

```bash
npx remotion render MyComp out/twitter.mp4 --width=1280 --height=720
```

- Resolution: 1280x720
- Aspect ratio: 16:9
- Max file size: 512MB
- Max duration: 140s

### Format preset summary

| Platform        | Width | Height | Ratio | FPS |
|-----------------|-------|--------|-------|-----|
| TikTok          | 1080  | 1920   | 9:16  | 30  |
| YouTube Shorts  | 1080  | 1920   | 9:16  | 30  |
| Instagram Reel  | 1080  | 1920   | 9:16  | 30  |
| Instagram Post  | 1080  | 1080   | 1:1   | 30  |
| YouTube         | 1920  | 1080   | 16:9  | 30  |
| Twitter/X       | 1280  | 720    | 16:9  | 30  |

When building a Composition in code, set `width`, `height`, and `fps` directly:

```tsx
<Composition
  id="TikTokPromo"
  component={PromoVideo}
  durationInFrames={30 * 15}
  fps={30}
  width={1080}
  height={1920}
/>
```

## Templates

All templates are created via the `create.sh` script. Each generates a full Remotion project with TypeScript, Tailwind CSS, and the template components.

### Chat Demo (Telegram-style phone mockup)

Animated chat bubbles appearing in sequence inside a phone mockup. Good for product demos, bot showcases, customer support highlights.

```bash
bash {baseDir}/scripts/create.sh my-chat --template chat
cd my-chat
```

Edit `src/messages.json` to customize the conversation:

```json
[
  {"text": "How do I reset my password?", "isUser": true},
  {"text": "Go to Settings > Account > Reset Password. You'll get an email in ~30 seconds.", "isUser": false},
  {"text": "Got it, thanks!", "isUser": true}
]
```

Render at TikTok resolution:

```bash
npx remotion render ChatDemo out/chat-tiktok.mp4 --width=1080 --height=1920
```

Default composition: 1080x1920 (vertical), 30fps, duration auto-calculated from message count.

### Title Card (animated intro)

Fade-in title with spring animation. Use for video intros, section headers, or standalone announcement cards.

```bash
bash {baseDir}/scripts/create.sh my-intro --template title
cd my-intro
```

Customize by editing `src/TitleCard.tsx` defaultProps:

```tsx
defaultProps={{ title: "Product Launch", subtitle: "Coming March 2026" }}
```

Render at YouTube resolution:

```bash
npx remotion render TitleCard out/title-youtube.mp4 --width=1920 --height=1080
```

Default composition: 1920x1080 (landscape), 30fps, 3 seconds.

### Blank (starter template)

Empty canvas for building custom compositions from scratch.

```bash
bash {baseDir}/scripts/create.sh my-project
cd my-project
```

Shows frame counter. Replace `src/Composition.tsx` with your own component.

### Building Custom Templates

To add a new template (e.g., promo, code walkthrough, stats dashboard, countdown, testimonial), create a React component that uses Remotion primitives:

```tsx
import { AbsoluteFill, useCurrentFrame, spring, useVideoConfig, interpolate, Sequence, Img, Audio } from "remotion";
```

Key Remotion APIs for templates:

- `useCurrentFrame()` - current frame number for animation
- `spring({ frame, fps, config })` - spring physics animation
- `interpolate(frame, inputRange, outputRange)` - linear interpolation
- `<Sequence from={30}>` - delay child content by N frames
- `<Img src={staticFile("logo.png")} />` - load static assets
- `<Audio src={staticFile("music.mp3")} />` - add background audio
- `<AbsoluteFill>` - full-frame container

Register compositions in `src/Root.tsx`:

```tsx
import { Composition } from "remotion";
import { PromoVideo } from "./PromoVideo";

export const RemotionRoot: React.FC = () => {
  return (
    <>
      <Composition
        id="PromoVideo"
        component={PromoVideo}
        durationInFrames={300}
        fps={30}
        width={1080}
        height={1920}
      />
    </>
  );
};
```

## Thumbnail Generation

Extract a single frame as a still image using Remotion's `still` command:

```bash
npx remotion still MyComp out/thumbnail.png
```

### Options

```bash
# Specific frame (default is frame 0)
npx remotion still MyComp out/thumb.png --frame=45

# JPEG output
npx remotion still MyComp out/thumb.jpeg --image-format=jpeg

# JPEG quality (0-100)
npx remotion still MyComp out/thumb.jpeg --image-format=jpeg --jpeg-quality=90

# Custom resolution
npx remotion still MyComp out/thumb.png --width=1280 --height=720

# Pass props
npx remotion still MyComp out/thumb.png --props='{"title":"Episode 1"}'
```

### Thumbnail for YouTube

```bash
npx remotion still TitleCard out/yt-thumb.png --width=1280 --height=720 \
  --props='{"title":"How to Build an AI Agent","subtitle":"Step by step guide"}'
```

YouTube recommended thumbnail: 1280x720 (16:9), PNG or JPEG.

### Thumbnail for Instagram

```bash
npx remotion still MyComp out/ig-thumb.png --width=1080 --height=1080
```

## Batch Rendering

Render multiple videos in sequence using a shell loop or xargs.

### Render multiple compositions from one project

If your Root.tsx registers multiple compositions:

```bash
npx remotion render PromoVideo out/promo.mp4
npx remotion render ChatDemo out/chat.mp4
npx remotion render TitleCard out/title.mp4
```

### Render with different props

```bash
for topic in "AI Agents" "Open Source" "DevTools"; do
  slug=$(echo "$topic" | tr ' ' '-' | tr '[:upper:]' '[:lower:]')
  npx remotion render TitleCard "out/${slug}.mp4" \
    --props="{\"title\":\"$topic\",\"subtitle\":\"Deep dive\"}"
done
```

### Render all social formats at once

```bash
npx remotion render MyComp out/youtube.mp4 --width=1920 --height=1080
npx remotion render MyComp out/tiktok.mp4 --width=1080 --height=1920
npx remotion render MyComp out/square.mp4 --width=1080 --height=1080
npx remotion render MyComp out/twitter.mp4 --width=1280 --height=720
```

### Batch from JSON config

Create a `render-jobs.json` file:

```json
[
  {"comp": "TitleCard", "output": "out/intro.mp4", "props": {"title": "Welcome"}},
  {"comp": "ChatDemo", "output": "out/demo.mp4"},
  {"comp": "TitleCard", "output": "out/outro.mp4", "props": {"title": "Thanks!"}}
]
```

Then render all jobs:

```bash
cat render-jobs.json | jq -c '.[]' | while read job; do
  comp=$(echo "$job" | jq -r '.comp')
  output=$(echo "$job" | jq -r '.output')
  props=$(echo "$job" | jq -r '.props // empty')
  if [ -n "$props" ]; then
    npx remotion render "$comp" "$output" --props="$props"
  else
    npx remotion render "$comp" "$output"
  fi
done
```

## Output Formats

Remotion supports multiple codecs via `--codec`:

```bash
# MP4 with H.264 (default, best compatibility)
npx remotion render MyComp out/video.mp4

# MP4 with H.265 (smaller files, less compatible)
npx remotion render MyComp out/video.mp4 --codec=h265

# WebM with VP8
npx remotion render MyComp out/video.webm --codec=vp8

# WebM with VP9 (better quality, slower encode)
npx remotion render MyComp out/video.webm --codec=vp9

# GIF (no audio, large files)
npx remotion render MyComp out/video.gif --codec=gif

# ProRes (lossless, huge files, for post-production)
npx remotion render MyComp out/video.mov --codec=prores

# PNG sequence (one PNG per frame)
npx remotion render MyComp out/frames/ --image-format=png --sequence
```

### Quality and performance flags

```bash
# CRF (quality) - lower = better quality, larger file. Default ~20
npx remotion render MyComp out/video.mp4 --crf=18

# Concurrency - render multiple frames in parallel
npx remotion render MyComp out/video.mp4 --concurrency=4

# Log level for debugging
npx remotion render MyComp out/video.mp4 --log=verbose
```

## Progress Tracking

Remotion prints render progress to stderr during rendering. The output includes:

- Frames rendered / total frames
- Percentage complete
- Estimated time remaining
- Encoding progress

To capture progress programmatically, parse stderr output:

```bash
npx remotion render MyComp out/video.mp4 2>&1 | tee render.log
```

For CI/CD pipelines, check the exit code:

```bash
npx remotion render MyComp out/video.mp4
if [ $? -eq 0 ]; then
  echo "Render complete: out/video.mp4"
  ls -lh out/video.mp4
else
  echo "Render failed"
  exit 1
fi
```

## Asset Management

### Static files (fonts, images, audio)

Place assets in the `public/` directory. Reference them with `staticFile()`:

```tsx
import { Img, Audio, staticFile } from "remotion";

// Image
<Img src={staticFile("logo.png")} />

// Audio
<Audio src={staticFile("background-music.mp3")} />

// Video overlay
<OffthreadVideo src={staticFile("clip.mp4")} />
```

### Custom fonts

Load web fonts or local fonts in your component:

```tsx
// Google Fonts via CSS import in src/index.css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');

// Or load a local font file
@font-face {
  font-family: 'CustomFont';
  src: url('./fonts/CustomFont.woff2') format('woff2');
}
```

Then use in components:

```tsx
<div style={{ fontFamily: "'Inter', sans-serif" }}>Your text</div>
```

### Background images and gradients

```tsx
<AbsoluteFill style={{
  backgroundImage: `url(${staticFile("bg.jpg")})`,
  backgroundSize: "cover",
  backgroundPosition: "center",
}} />
```

## Error Recovery

### Chrome Headless Shell not found

```
Error: Could not find Chrome Headless Shell
```

Fix: Run setup script or install Chrome dependencies:

```bash
bash {baseDir}/scripts/setup.sh
```

Or install Chrome Headless Shell explicitly:

```bash
npx @remotion/install-chrome-headless-shell
```

### Out of memory during render

```
FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory
```

Fix: Increase Node.js memory limit:

```bash
NODE_OPTIONS="--max-old-space-size=4096" npx remotion render MyComp out/video.mp4
```

Or reduce concurrency:

```bash
npx remotion render MyComp out/video.mp4 --concurrency=1
```

### Missing npm dependencies

```
Cannot find module 'remotion'
```

Fix: Install dependencies in the project directory:

```bash
npm install
```

### Composition not found

```
Could not find composition with id "MyComp"
```

Fix: List available compositions:

```bash
npx remotion compositions
```

Then use the correct composition ID in your render command.

### Render timeout on slow machines

For long videos or slow servers, increase the timeout per frame:

```bash
npx remotion render MyComp out/video.mp4 --timeout=60000
```

Default timeout is 30000ms (30 seconds) per frame.

### Display server errors (headless Linux)

```
Error: Failed to launch the browser process
```

On headless Linux without a display server, Remotion uses Chrome Headless Shell which does not require X11/Wayland. If you see display errors, ensure you are not accidentally using a full Chrome install. Set the browser explicitly:

```bash
npx remotion render MyComp out/video.mp4 --browser=chrome-headless-shell
```

## Usage Examples

### Product launch promo (TikTok)

```bash
bash {baseDir}/scripts/create.sh launch-promo --template title
cd launch-promo
# Edit src/TitleCard.tsx to customize
npx remotion render TitleCard out/promo.mp4 --width=1080 --height=1920
```

### Customer support bot demo (Instagram Reel)

```bash
bash {baseDir}/scripts/create.sh support-demo --template chat
cd support-demo
# Edit src/messages.json with support conversation
npx remotion render ChatDemo out/support-reel.mp4 --width=1080 --height=1920
```

### YouTube intro with custom branding

```bash
bash {baseDir}/scripts/create.sh yt-intro --template title
cd yt-intro
# Add logo to public/logo.png, edit TitleCard to include <Img>
npx remotion render TitleCard out/intro.mp4 --width=1920 --height=1080
```

### Generate thumbnails for a video series

```bash
for i in 1 2 3 4 5; do
  npx remotion still TitleCard "out/thumb-ep${i}.png" --width=1280 --height=720 \
    --props="{\"title\":\"Episode ${i}\",\"subtitle\":\"The Series\"}"
done
```

### Square Instagram post

```bash
bash {baseDir}/scripts/create.sh ig-post
cd ig-post
# Build a 1:1 composition
npx remotion render MyComp out/post.mp4 --width=1080 --height=1080
```

## Related Skills

- Use **/nano-triple** to generate still images (logos, backgrounds, icons) to use as assets in your Remotion compositions
- Use **/last30days** to research trending topics and generate content ideas for your videos

## Linux Dependencies Reference

The setup script installs these system packages required by Chrome Headless Shell:

### Ubuntu/Debian

```
libnss3 libdbus-1-3 libatk1.0-0 libasound2 libxrandr2 libxkbcommon-dev
libxfixes3 libxcomposite1 libxdamage1 libgbm-dev libcups2 libcairo2
libpango-1.0-0 libatk-bridge2.0-0
```

Note: Ubuntu 24.04 uses `libasound2t64` instead of `libasound2`.

### Amazon Linux

```
mesa-libgbm libX11 libXrandr libdrm libXdamage libXfixes libxkbcommon
dbus-libs libXcomposite alsa-lib nss dbus pango cups-libs at-spi2-core atk at-spi2-atk
```

### Docker

For Docker, use a base image with Chrome dependencies pre-installed, or add the apt-get install to your Dockerfile:

```dockerfile
FROM node:20-slim
RUN apt-get update && apt-get install -y \
  libnss3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libgbm1 \
  libpango-1.0-0 libcairo2 libxcomposite1 libxdamage1 libxfixes3 libxrandr2 \
  && rm -rf /var/lib/apt/lists/*
```

### Full dependency reference

See https://www.remotion.dev/docs/miscellaneous/linux-dependencies

## Privacy Note

All templates use FAKE demo data only:
- Fake GPS coords (San Francisco: 37.7749, -122.4194)
- Placeholder names and values
- Never includes real user data

Always review generated content before publishing.
