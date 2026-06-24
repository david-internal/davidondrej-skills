---
name: reading-speaking-time
description: Calculate reading and speaking time of a script (wordcounter.net method, reading 275 wpm, speaking 180 wpm) via an inline Python one-liner — all math in code, never in tokens. Use when the user gives a script and asks for reading time, speaking time, script length, "how long is this", or whether it fits a target duration.
---

# Reading & Speaking Time

Calculate how long a script takes to read or speak. Same rates as wordcounter.net: reading = 275 wpm, speaking = 180 wpm.

## Workflow

1. Save the script text to a temp file (e.g. `/tmp/script.txt`). If the script lives in a markdown file, count only the actual script body — strip headings, notes, metadata.
2. Run the math in code (never compute in tokens):

```bash
python3 - /tmp/script.txt <<'EOF'
import sys
text = open(sys.argv[1]).read()
w = len(text.split())
fmt = lambda s: f"{round(s)//60} min {round(s)%60} sec"
print(f"Words: {w}")
print(f"Reading Time: {fmt(w/275*60)}")
print(f"Speaking Time: {fmt(w/180*60)}")
EOF
```

3. Reply with exactly the three lines below — nothing else. If the user mentioned a target duration, add one line: over/under target by N sec (also computed in code).

## Output format

```
Words: 358
Reading Time: 1 min 18 sec
Speaking Time: 1 min 59 sec
```
