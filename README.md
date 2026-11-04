# Lingora

A real, working translation app — photo, live video, audio, and document
translation, plus a word-of-the-day learner. No backend, no API keys.

## How it works

| Feature | Technology | Notes |
|---|---|---|
| Photo translate | [Tesseract.js](https://github.com/naptha/tesseract.js) (in-browser OCR) | Runs entirely client-side; downloads its language model the first time |
| Live video translate | `getUserMedia` (camera) + Web Speech API | Live captions, translated as you speak |
| Audio translate | Web Speech API (speech-to-text) + `speechSynthesis` (text-to-speech) | Speak, see the translation, tap to hear it spoken back |
| Document translate | [mammoth.js](https://github.com/mwilliamson/mammoth.js) for `.docx`, native `FileReader` for `.txt` | Download the translated result as `.txt` |
| Word of the day | Local word bank + `localStorage` for streaks | Same word all day, based on the date |
| Translation engine | [MyMemory API](https://mymemory.translated.net/doc/spec.php) | Free, no signup, ~5000 words/day on the anonymous tier |

## Setup

```bash
npm install
npm run dev
```

Open the URL it prints (usually `http://localhost:5173`).

## Using it on your phone

```bash
npm run dev -- --host
```

This prints a "Network" URL like `http://192.168.x.x:5173` — open that on
your phone while it's on the same Wi-Fi.

**Camera/mic note:** browsers only allow camera and microphone access over
`https://` or `localhost`. Testing on your phone over plain `http://` on
your local network will work in Chrome for most local/private IPs, but if
your browser blocks it, the fix is deploying to a host with HTTPS (Vercel,
Netlify, etc. — all free) and opening that URL on your phone instead.

## Known limitations (worth knowing before you rely on this)

- **Browser support:** live speech recognition (audio + video features)
  uses the Web Speech API, which is solid in Chrome and Edge but not
  supported in Firefox and only partially in Safari.
- **Translation quality:** MyMemory is a free machine-translation
  aggregator — good enough for a real working demo, not as polished as
  Google Translate or DeepL. If quality becomes the priority, swap the
  `translateText` function in `src/lib/translate.js` for a paid provider
  (Google Cloud Translation, DeepL, OpenAI) — everything else in the app
  stays the same.
- **Rate limits:** the free MyMemory tier allows roughly 5,000 words/day
  per IP anonymously. Fine for personal use and demos; you'll want a paid
  key for real traffic.
- **Document translate:** currently reads `.txt` and `.docx`. Add PDF
  support later with `pdfjs-dist` if you need it.
- **Client-only architecture:** since there's no backend, all requests
  (including to MyMemory) happen straight from the user's browser. That's
  fine for personal/demo use; for a public product you'd normally proxy
  translation calls through a small backend to add rate-limiting, caching,
  and to keep any paid API keys off the client.

## Deploying so it's reachable from any device

```bash
npm run build
```

This outputs a static `dist/` folder you can drag-and-drop onto
[Netlify](https://app.netlify.com/drop) or deploy with `vercel` — either
gives you a public HTTPS URL, which also fixes the camera/mic permission
note above.
