# Hinglish Audiobook Studio — GitHub Pages + Cloudflare Worker

Browser-based chapter editor and Gemini TTS, with browser-side PCM merge, WAV download and MP3 encoding. No Gemini key appears in the site. Designed for one person (NOT a multi-user service).

## 1. GitHub Pages

Create a NEW repository named `hinglish-audiobook-studio` (do not edit existing repositories). Upload `index.html`, `app.js`, `.gitignore`, and optionally the `worker` folder and README. In Settings > Pages select Deploy from branch, `main`, root. Site URL: `https://YOUR-USERNAME.github.io/hinglish-audiobook-studio/`.

## 2. New Cloudflare Worker

Cloudflare dashboard > Workers & Pages > Create Worker. Name it `hinglish-audiobook-tts`. Replace the starter code with `worker/index.js` and deploy.

Set **encrypted secrets** (Settings > Variables and Secrets > Add > Secret):
- `GEMINI_API_KEY`: new key from https://aistudio.google.com/apikey
- `STUDIO_PASSWORD`: strong random password for personal access (NOT your Google account password).

Set **plain variable**:
- `ALLOWED_ORIGIN` = `https://YOUR-USERNAME.github.io` (exact origin, no slash/path).

Never put API keys, passwords, `.dev.vars` or `.env` into a public repository. Don't put your studio password in the GitHub source either. Configure a rate limit in Cloudflare's dashboard where possible. A shared password is basic protection, not production-grade authentication, and requests can still consume quota if the password leaks.

## 3. Use

Visit your GitHub Pages site, enter the `https://...workers.dev` Worker URL and your Studio password. Paste Chapter 1, generate, play, and download. The MP3 encoding library loads from a public jsDelivr CDN, so internet and access to that CDN are required. WAV remains available if it fails. Chapters exist in the current tab memory only; download immediately; local drafts save only text/title/endpoint, never password or generated audio. Generate each chapter and click `Merge chapters MP3` while the same tab remains open.

## Limits / caveats

- Live generation depends on Gemini model availability, quota, and billing. Test one paragraph first.
- Gemini may output raw 24kHz L16 PCM or other audio formats. This version expects L16 mono PCM; if your model returns WAV, the decoder will need adjustment.
- Large output via JSON/base64 can hit Worker response or browser memory limits. Split into smaller chunks if necessary.
- Some responses may time out due to platform limits. Retry failed chunks only; the current MVP restarts an entire chapter on retry.
- The app does not persist audio between reloads and does not include a billing system.
- CORS origin checks are not authorization; keep the studio password secret.
