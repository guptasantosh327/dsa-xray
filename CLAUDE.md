# CLAUDE.md

When given a LeetCode problem + solution code: generate the visualizer HTML and save it to the correct folder. No confirmation needed.

---

## Project

Static zero-dependency DSA visualizer. Single HTML files. No build step.

```
dsa-xray/
├── index.html
├── binary-tree/*.html
├── binary-search-tree/*.html
└── {category}/{problem}-visualizer.html
```

---

## Input Format

User posts a LeetCode problem number + their solution code:

```
#242 Valid Anagram

var isAnagram = function(s, t) { ... }
```

---

## Output: Single HTML File

Save to: `{category}/{kebab-title}-visualizer.html`

Every file must have:
- User's **exact code** in a syntax-highlighted `<code>` block
- `EXAMPLES[]` with 4–5 test cases including edge cases
- `steps[]` tracing the user's actual logic — their variable names, their branches
- `renderStep()` driving all DOM updates
- Prev / Next / Play controls + keyboard `←→` navigation
- Progress bar, step description, execution log
- Mobile responsive

---

## Visualization Type (infer from problem)

| Problem type | Render |
|---|---|
| Array / string | Horizontal cell grid with index pointers |
| HashMap / frequency | Key-value grid, deposit → drain animation |
| Binary tree | SVG nodes + edges, call stack panel |
| Two pointer / sliding window | Array with L/R pointer arrows |
| Stack / queue | Vertical stack or horizontal queue |
| Graph | SVG nodes + BFS/DFS frontier highlight |

---

## Design System (exact, no variation)

```css
:root {
  --bg: #0d0f14;       --surface: #13161d;    --surface2: #1a1e28;
  --surface3: #212638; --border: rgba(255,255,255,0.07);
  --border-mid: rgba(255,255,255,0.12);
  --text: #e8eaf0;     --text-muted: #6b7280; --text-dim: #3d4251;
  --accent: #7c6ef5;   --green: #34d399;      --amber: #fbbf24;
  --blue: #60a5fa;     --red: #f87171;        --orange: #fb923c;
  --mono: 'JetBrains Mono', monospace;
  --sans: 'Sora', sans-serif;
}
```

- Fonts: Google Fonts — JetBrains Mono + Sora
- Grid overlay: 40px lines at `rgba(124,110,245,0.03)`
- Sections: `fadeUp` animation with staggered `animation-delay`
- Progress bar: `amber → orange` gradient
- Active = amber · Match = green · Fail = red · Secondary = blue
- Cells: `38×38px`, `border-radius:8px`

---

## Teaching Sections (every file, this order)

1. **Pattern banner** — pattern name + why it fits + 3 related problems
2. **Problem statement** — plain English + analogy + 3 visual worked examples
3. **Intuition + Hints** — brute force → optimal, numbered, key trick called out
4. **Code block** — user's exact code, syntax highlighted, active line highlighted per step. Include a "Blur Code" toggle in the UI that blurs out the core logic lines (loops, if conditions) so the user can test their memory before revealing it (Active Recall).
5. **Interactive visualizer** — step engine
6. **Gotchas & Common Pitfalls** — 2-3 common mistakes, edge cases people forget, or typical interview traps for this specific problem (Failure Analysis).
7. **Trade-offs / Alternatives** — Briefly explain why another approach was NOT used, or the space/time trade-off.
8. **Complexity + Memory hooks** — O() table + a 1-sentence "ELI5" overall summary mnemonic to memorize + 2-3 quotable takeaways.

---

## Step Object Shape

```js
{
  desc:    "explanation — use <em> for key terms",
  log:     [{ t: "log line", h: true }],  // h=true = highlighted
  vPhase:  "phase name",
  vResult: "—" | "true" | "false" | number
  // + visualization-specific fields (activeIdx, map, stack, etc.)
}
```

---

## After Generating the Visualizer

Add entry to `index.html` TOPICS object:

```js
{
  num: 242, title: "Valid Anagram",
  href: "strings/valid-anagram-visualizer.html",
  diff: "easy", pattern: "HashMap Frequency",
  desc: "One map, two passes — deposit s, drain t",
  pills: ["HashMap", "String", "Frequency Count"],
  kw: "anagram frequency map string hash"
}
```

---

## Hard Rules

- No separate CSS/JS files — everything in one `.html`
- No frameworks, no build tools
- Never rewrite the user's solution — trace their exact logic
- Variable panel labels = user's actual variable names
- Always include a boundary/edge case as one of the examples
- If category folder doesn't exist, create it
- If unsure of category, use the LeetCode problem's primary tag