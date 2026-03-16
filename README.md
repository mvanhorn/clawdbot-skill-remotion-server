# Remotion Server Skill for OpenClaw

Create professional videos from code with [Remotion v4](https://remotion.dev) (latest: v4.0.435). Headless rendering on any Linux server - no Mac or GUI required.

## What it does

- **5 rendering backends** - local CLI, Lambda, Vercel, Cloud Run, GPU on EC2
- **Headless rendering** - generate MP4, WebM, GIF, or PNG sequences from code
- **Social media presets** - TikTok (9:16), YouTube (16:9), Instagram Reel (9:16), Instagram Post (1:1), Twitter (16:9)
- **Template library** - chat demo, title card, blank starter, plus guidance for building custom templates
- **New packages** - @remotion/sfx (sound effects), @remotion/starburst (shapes), @remotion/captions, @remotion/player
- **Mediabunny** - GPU-accelerated format conversion (MP4, WebM, MOV, MKV, AVI, MP3, FLAC, WAV, and more)
- **Thumbnail generation** - extract any frame as PNG or JPEG using `npx remotion still`
- **Batch rendering** - render multiple compositions, formats, or prop variations in one pass
- **TailwindCSS 4.2.0** - pre-configured in all templates
- **Zod v4** - props validation
- **Rspack bundler** - experimental faster builds
- **Any Linux server** - works on VPS, Pi, CI, Docker, wherever Chrome Headless Shell runs

## Quick start

### Install the skill

```bash
git clone https://github.com/mvanhorn/clawdbot-skill-remotion-server.git ~/.openclaw/skills/remotion-server
```

### One-time setup

Install browser dependencies (Ubuntu/Debian):

```bash
bash scripts/setup.sh
```

### Create and render a project

```bash
bash scripts/create.sh my-video
cd my-video
npx remotion render MyComp out/video.mp4
```

### Generate a thumbnail

```bash
npx remotion still MyComp out/thumbnail.png --frame=45
```

### Example chat usage

- "Make a TikTok video showing a chat about our new feature"
- "Create a YouTube intro for my channel"
- "Render a promo video for the product launch"
- "Generate thumbnails for episodes 1-5"
- "Batch render this video in all social media formats"
- "Render this video on Lambda"
- "Add a ding sound effect at the 2 second mark"

## Templates

**Chat Demo** - phone mockup with animated chat bubbles:
```bash
bash scripts/create.sh my-promo --template chat
# Edit src/messages.json with your conversation
```

**Title Card** - simple animated intro:
```bash
bash scripts/create.sh my-intro --template title
```

**Blank** - empty canvas for custom compositions:
```bash
bash scripts/create.sh my-project
```

## Output formats

| Format | Command | Notes |
|--------|---------|-------|
| MP4 (H.264) | `npx remotion render MyComp out/video.mp4` | Default, best compatibility |
| MP4 (H.265) | `--codec=h265` | Smaller files |
| WebM (VP8) | `--codec=vp8` | Web-friendly |
| WebM (VP9) | `--codec=vp9` | Better quality |
| GIF | `--codec=gif` | No audio |
| ProRes | `--codec=prores` | Post-production |
| PNG sequence | `--sequence --image-format=png` | One PNG per frame |

## Social media formats

| Platform | Resolution | Ratio |
|----------|-----------|-------|
| TikTok / Shorts / Reels | 1080x1920 | 9:16 |
| YouTube | 1920x1080 | 16:9 |
| Instagram Post | 1080x1080 | 1:1 |
| Twitter/X | 1280x720 | 16:9 |

## Rendering backends

| Backend | Best for | Setup |
|---------|----------|-------|
| Local CLI | Dev, CI, single server | None (default) |
| Lambda | Scale, async, webhooks | AWS account + deploy |
| Vercel | Teams on Vercel | `npm install @remotion/vercel` |
| Cloud Run | Longer videos, lower cost | GCP account |
| GPU (EC2) | Heavy animations, 3D | EC2 GPU instance |

## How it works

Wraps Remotion v4's CLI renderer with project scaffolding and templates. The setup script installs Chrome Headless Shell dependencies (libnss3, libatk, libgbm, etc.) so rendering works on headless Linux. FFmpeg is baked into each project - no separate install needed. All templates use fake demo data only.

## License

MIT
