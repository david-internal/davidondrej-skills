---
name: youtube-transcript
description: Use whenever the user needs the transcript of a YouTube video — fetching, extracting, downloading, or pulling captions/subtitles/transcript text from a YouTube URL. Triggers on "get the transcript", "transcript of this video", "pull the captions", "download subtitles", "what does this YouTube video say".
---

# YouTube Transcript (via yt-dlp)

Fetch a YouTube video's captions without downloading the video, then save a clean raw `.txt` transcript.

## Save location
- If the user is in a real project or working directory, save there.
- Otherwise, ask the user where to save the transcript or use a generic downloads/output directory.

## Fastest path

```bash
OUT="$(pwd)"
META=$(yt-dlp --print "%(channel)s|%(title)s" --skip-download "URL")
NAME=$(echo "$META" | tr '| ' '__' | tr -cd '[:alnum:]_.-')
yt-dlp --skip-download --write-subs --write-auto-subs \
  --sub-langs "en.*" --sub-format json3 \
  -o "$OUT/$NAME.%(ext)s" "URL"
```

Name the file `Channel_Title` with spaces replaced by `_`, for example `Example_Channel_example_video_title.txt`. Fall back `channel` → `uploader` → `uploader_id` if `channel` is null.

- `--skip-download` = captions only. `--write-subs` + `--write-auto-subs` = manual first, auto as fallback.
- Use `json3` rather than VTT/SRT when possible, because auto-generated VTT can contain repeated rolling-caption lines.

## Flatten json3 → raw text

```bash
python3 - "$OUT" <<'PY'
import json, html, re, glob, sys, pathlib
f = glob.glob(sys.argv[1] + "/*.json3")
if not f: sys.exit("no json3 file")
data = json.load(open(f[0], encoding="utf-8"))
parts = ["".join(s.get("utf8","") for s in e.get("segs") or []) for e in data.get("events", [])]
txt = re.sub(r"\s+", " ", html.unescape(" ".join(p.strip() for p in parts if p.strip()))).strip()
out = pathlib.Path(f[0]).with_suffix(".txt")
out.write_text(txt, encoding="utf-8"); print(out)
PY
```

Report the saved path and print the text if it is short.

## Notes
- Non-English or unknown language: run `yt-dlp --list-subs "URL"` first, then set `--sub-langs`.
- Newer `yt-dlp` versions may require compatible runtime dependencies for YouTube extraction.

## Failure handling
- On first failure: run `yt-dlp -U` once, retry once, then stop.
- `429` or `Sign in to confirm you're not a bot` means access is being rate-limited or challenged. Stop and do not retry in a loop.
- Empty or `no subtitles` means the video has no captions. Report it; do not retry or switch tools automatically.
- Never fall back to downloading audio for transcription unless the user explicitly asks.
