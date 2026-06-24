---
name: tldraw-whiteboard
description: Use when the user wants to create visual whiteboard graphics for videos using the tldraw SDK, or inspect/extract content from an existing .tldr board file. Triggers on tldraw, whiteboard, visual diagram, canvas graphic, video visual, board design, "what's in this board", "read this column from the tldr".
---

# tldraw Whiteboard

Two modes:
1. **Authoring** — build new board graphics in a local tldraw app using programmatic shapes.
2. **Inspecting** — read content from an existing `.tldr` file: parse JSON, extract richText, isolate a column.

For inspection, use the workflow described below.

---

## Authoring Mode

Build visual whiteboard graphics for videos using the tldraw SDK. The app can run locally via `npm run dev` on the default Vite port.

### Setup

If a tldraw app does not already exist, create one:

```bash
npm create vite@latest tldraw -- --template react-ts
cd tldraw
npm install
npm install tldraw
```

All visuals can be built programmatically in `src/App.tsx` using the tldraw Editor API.

### How It Works

Everything happens in the `onMount` callback. Create shapes programmatically using `editor.createShape()`. Each "page" or section can be offset horizontally, for example section 1 at x=0, section 2 at x=1500, section 3 at x=3200. The presenter can pan between them during recording.

```tsx
import { Editor, Tldraw, createShapeId, toRichText } from 'tldraw'
import 'tldraw/tldraw.css'

export default function App() {
  const handleMount = (editor: Editor) => {
    editor.user.updateUserPreferences({ colorScheme: 'light' })
    // Create shapes here...
    setTimeout(() => {
      editor.zoomToFit({ animation: { duration: 500 } })
    }, 100)
  }

  return (
    <div style={{ position: 'fixed', inset: 0, background: '#ffffff' }}>
      <Tldraw onMount={handleMount} />
    </div>
  )
}
```

### Shape Types & API

#### Text
```tsx
editor.createShape({
  id: createShapeId('mytext'),
  type: 'text',
  x: 100, y: 100,
  props: {
    richText: toRichText('HEADLINE TEXT'),
    size: 'xl',  // 's' | 'm' | 'l' | 'xl'
    color: 'black',
  },
})
```

#### Geo
```tsx
editor.createShape({
  id: createShapeId('mybox'),
  type: 'geo',
  x: 100, y: 200,
  props: {
    w: 340, h: 140,
    geo: 'rectangle',
    color: 'violet',
    fill: 'solid',
    richText: toRichText('Box content here'),
    size: 'm',
    font: 'sans',
  },
})
```

#### Arrows
Point-based arrows are the safest option when creating shapes programmatically:

```tsx
editor.createShape({
  id: createShapeId('myarrow'),
  type: 'arrow',
  x: 300, y: 400,
  props: {
    color: 'black',
    start: { x: 0, y: 0 },
    end: { x: 100, y: 150 },
  },
})
```

**Avoid binding-based arrows with `boundShapeId` in generated shapes; they can throw validation errors.**

### Colors
`'black'` `'white'` `'grey'` `'light-violet'` `'violet'` `'blue'` `'light-blue'` `'green'` `'light-green'` `'yellow'` `'orange'` `'light-red'` `'red'`

### Geo Shapes
`'rectangle'` `'ellipse'` `'diamond'` `'star'` `'hexagon'` `'cloud'` `'triangle'` `'arrow-right'` `'arrow-left'` `'arrow-up'` `'arrow-down'`

### Design Preferences

- **Headlines:** `size: 'xl'`, uppercase, `color: 'black'`, or `'white'` on a dark background.
- **Subtitles:** `size: 'm'`, sentence case, `color: 'grey'`.
- **Section labels:** `size: 's'`, uppercase, colored to match their section.
- **Bullet text inside shapes:** 5-10 words max per box.
- **No newline characters** — they render as literal `\n`. Use separate shapes.
- **Generous spacing** — at least 80px between rows and 40px between columns. Row gap between tiers: 140-180px.
- **Multiple sections:** Offset each section horizontally by 1500-1700px.
- **Always `editor.zoomToFit()` at the end** with a small delay.
- Use different colors for different concepts. Example mapping: red=warning, green=positive, violet=vision, orange=question, blue=tools.
- Match arrow colors to the target shape's color.
- Default to a white background: `colorScheme: 'light'`, `#ffffff`; ask the requester if they prefer dark mode, such as `colorScheme: 'dark'`, `#1e1e2e`, with white titles.
- Use animations sparingly via `setTimeout`. When in doubt, show everything at once.
- Keep the toolbar visible during recording.

### Loading an Existing `.tldr` Into the Live App

Use `parseTldrawJsonFile` + `getSnapshot` + `loadSnapshot` from the SDK. Put the `.tldr` file in the app's `public/` directory and `fetch()` it on mount. **Back up `App.tsx` first** — the load-on-mount pattern replaces the current store.

### Running

```bash
cd tldraw
npm run dev
```

Open the local Vite URL shown in the terminal. After code changes, hit **Reset data** in the error screen or browser, then refresh.

### Process

1. The requester provides section content, such as bullets from a video outline.
2. Decide the structure pattern: concept map, bullet list, flow, timeline, etc.
3. Create a new component per video, for example `src/videos/<video-name>.tsx`, and import it from `App.tsx`.
4. Review in the browser, gather feedback, and iterate.

### Authoring Rules

- **Max 10 words per shape.**
- **Never use newline characters.**
- **Each section gets its own horizontal space.**
- **Always use point-based arrows.**
- **Variety over uniformity.**

---

## Inspection Mode

When asked "what's in this board / column / section", load the `.tldr` and parse it.

Quick rules:
- `.tldr` is JSON. Records are at `data['records']`; filter for `typeName == 'shape'`.
- Text lives in `props.richText`, a ProseMirror tree, not `props.text`. Walk the tree.
- Sort by `(y, x)` for reading order.
- Boards are often arranged as columns of vertically stacked sections. To isolate a column: find its header, then find the next header in the same x-band; that gives the y bound.
- Gaps in y usually mean a tall video/image shape, not missing content.
