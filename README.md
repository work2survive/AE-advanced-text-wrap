# AE Advanced Text Wrap

> **Next-Generation Typographic Paragraph Composer & Intelligent Text Wrapping for Adobe After Effects.**  
> *Co-authored & architected with Google DeepMind Antigravity for `work2survive` ecosystems.*

[![Live Web Demo](https://img.shields.io/badge/Live%20Demo-AE%20JS%20Builder-4183C4?style=for-the-badge&logo=adobeaftereffects&logoColor=white)](https://work2survive.github.io/AE_JS_builder/?jsx=AE_Advanced_Text_Wrap.jsx&autobuild=true)
[![Adobe After Effects](https://img.shields.io/badge/AE-CS6%20to%202026%2B-9999FF?style=for-the-badge&logo=adobe&logoColor=white)](https://www.adobe.com/products/aftereffects.html)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

---

## 1. Overview & Problem Statement

Both Adobe After Effects native Paragraph (Box) Text and web browsers' standard `word-wrap` rely on **naive greedy first-fit line breaking**. Greedy line breaking packs words onto each line until the next word exceeds the container width, then breaks.

This causes notorious typography defects in video and motion graphics:
- **Widows & Orphans**: An awkward single word sitting lonely on the final line of a paragraph.
- **Dangling Short Words**: Short articles and prepositions (`a`, `in`, `on`, `to`, `de`, `el`, `pour`, `und`) dangling alone at the end of a line, breaking reading flow.
- **Separated Units**: Numbers separated from their units or percentages (`100` on line 1, `px` on line 2; or `$` on line 1, `500` on line 2).
- **Severe Raggedness**: Wildly unbalanced line lengths on centered titles or quote cards.

```
NAIVE GREEDY WRAPPING (Standard AE / CSS)
┌──────────────────────────────────────────────┐
│ Typography is the art and technique of       │ <-- Packed to 98% of container
│ arranging type to make written language      │ <-- Packed to 94% of container
│ legible, readable, and appealing when        │ <-- Packed to 96% of container
│ displayed.                                   │ <-- AWFUL WIDOW: 1 lonely word (10%)
└──────────────────────────────────────────────┘

BALANCED PARAGRAPH COMPOSITION (AE Advanced Text Wrap)
┌──────────────────────────────────────────────┐
│     Typography is the art and technique      │ <-- Balanced at 82%
│      of arranging type to make written       │ <-- Balanced at 80%
│     language legible, readable, and          │ <-- Balanced at 81%
│         appealing when displayed.            │ <-- Harmonious ending (60%)
└──────────────────────────────────────────────┘
```

**`AE Advanced Text Wrap`** elevates After Effects typography to desktop-publishing standards by implementing a **Knuth-Plass dynamic paragraph composer**, **multilingual algorithmic hyphenation**, and **micro-typographic semantic binding**.

---

## 2. Key Features

### 🌟 1. Balanced Paragraph Composer (Knuth-Plass / InDesign Style)
- Evaluates candidate line breaks across the entire paragraph using dynamic programming.
- Optimizes total paragraph slack: minimizes raggedness variance and produces uniform visual density.
- Eliminates ugly line jumps and prevents orphan lines.

### 📐 2. Five Selectable Wrap Methods
| Wrap Method | Ideal Use Case | Visual Behavior |
| :--- | :--- | :--- |
| **Balanced Paragraph** | Body text, captions, subtitles | Minimizes raggedness variance across the entire paragraph. |
| **Greedy First-Fit** | Standard layout | Polished first-fit with orphan and short-word guardrails. |
| **Optical Pyramid** | Titles, poetic stanzas, quote cards | Symmetrically grades lines so length expands gracefully. |
| **Reverse Pyramid** | Headlines, broadcast lower-thirds | Top-heavy composition: first line is widest, tapering down. |
| **Compact Block** | UI cards, restricted badges | Tightest packing of lines within the max pixel constraint. |

### 🔤 3. Multilingual Algorithmic Syllabification & Hyphenation
- Zero external dependencies: 100% native ExtendScript ECMAScript 3 engine.
- Language-specific syllable rules:
  - **English (US/UK)**: Morphological prefixes (`un-`, `dis-`, `trans-`), suffixes (`-tion`, `-ment`, `-ness`), and double consonants (`let-ter`, `run-ning`).
  - **Spanish (Español)**: Digraph preservation (`ch`, `ll`, `rr`), inseparable consonant-liquid blends (`pr`, `br`, `cl`, `pl`), and diphthongs.
  - **French (Français)**: Elision apostrophes (`l'`, `d'`) and French syllable patterns.
  - **German (Deutsch)**: Compound prefixes (`ver-`, `zer-`, `ent-`, `be-`) and suffixes (`-ung`, `-heit`, `-keit`).
  - **Italian (Italiano)**: Liquid consonant division and vocalic syllabification.
  - **Universal Fallback**: $V-CV$ and $VC-CV$ syllabification.
- **Safety**: Configurable minimum word length ($\ge 6$ letters), minimum prefix ($\ge 3$), minimum suffix ($\ge 3$), and **hyphen ladder prevention** (penalizes consecutive lines ending with hyphens).

### 🔗 4. Micro-Typographic Rules & Non-Breaking Spaces (`\u00A0`)
- **Avoid Dangling Short Words**: Automatically binds short prepositions and articles (`a`, `the`, `in`, `on`, `for`, `with`, `by`, `of`, `de`, `pour`, `dans`, `und`) to the next word with `\u00A0`.
- **Numbers & Units**: Keeps figures and units/symbols together (`100 px`, `50 %`, `1920 x 1080`, `25 kg`, `15 min`).
- **Currency & Honorifics**: Keeps currencies (`$ 500`, `€ 250`) and titles (`Dr. Smith`, `Mr. Jones`, `e.g. typography`) bound.
- **Widow & Orphan Prevention**: Binds the penultimate word to the final word of every paragraph so no single word can ever sit alone on a line.

### 📏 5. Max Text Width Constraint (in Pixels)
- Scrubbable slider (100 px to 1920 px) and direct numeric text input.
- Serves as the strict boundary constraint for all wrapping and hyphenation calculations.

### 💾 6. Non-Destructive State Storage & 1-Click Restore
- Stores the original pre-wrap text, box type, and stated dimensions inside an **`Advanced Text Wrap`** Slider Control effect expression comment payload:
  ```javascript
  /* AdvancedTextWrapState:{"text":"...","isBox":false,"boxSize":[0,0]} */
  ```
- Mirrored in `layer.comment` for native After Effects metadata inspection.
- Clicking **"↺ Restore Original Text"** instantly reverts the layer back to its exact pre-wrap text and box geometry.

### 🔄 7. Universal Text Layer & Alignment Support
- **Point Text**: Injects native After Effects carriage returns (`\r`), converting infinite 1-line Point text into an immaculately wrapped multi-line paragraph without shifting the layer's baseline origin or anchor point.
- **Paragraph (Box) Text**: Updates `doc.boxTextSize = [maxWidth, newHeight]` and injects balanced line breaks so AE's internal naive wrapping doesn't distort the composition.
- **All Alignments**: Left, Center, Right, and Justified text work flawlessly without any coordinate drifting.

### 🤝 8. Ecosystem Interoperability
- **[AE-Text-Highlight](https://github.com/work2survive/AE-Text-Highlight)**: Seamless compatibility. Highlights built on wrapped text perfectly track the newly formatted line lengths, and search queries match across non-breaking spaces.
- **[AE-replace-color-emojis](https://github.com/work2survive/AE-replace-color-emojis)**: UTF-16 surrogate pairs (e.g. 🍿, 🚀, ❤️) are treated as indivisible atomic tokens. The tokenizer and hyphenator never split a multi-byte emoji glyph.

---

## 3. Live Web Simulator

Test and explore **AE Advanced Text Wrap** directly inside your web browser without opening After Effects:

👉 **[Launch Live Demo in AE JS Builder](https://work2survive.github.io/AE_JS_builder/?jsx=AE_Advanced_Text_Wrap.jsx&autobuild=true)**

The web emulator provides:
1. Interactive ScriptUI panel on the right.
2. Real-time 3D canvas viewport rendering on the left.
3. Timeline and Effect Controls inspector showing the `Advanced Text Wrap` effect slider and stored expression state.
4. "📝 Create Demo Text" button to test on both Point and Box text with 1 click.

---

## 4. Installation & Usage in After Effects

### Method A: ScriptUI Panels (Dockable Panel)
1. Download [`AE_Advanced_Text_Wrap.jsx`](https://raw.githubusercontent.com/work2survive/AE-advanced-text-wrap/main/AE_Advanced_Text_Wrap.jsx).
2. Copy it into your After Effects `ScriptUI Panels` folder:
   - **Windows**: `C:\Program Files\Adobe\Adobe After Effects <Version>\Support Files\Scripts\ScriptUI Panels\`
   - **macOS**: `/Applications/Adobe After Effects <Version>/Scripts/ScriptUI Panels/`
3. Restart After Effects.
4. Launch via **Window > AE_Advanced_Text_Wrap.jsx**.

### Method B: Run Directly
1. In After Effects, navigate to **File > Scripts > Run Script File...**
2. Choose `AE_Advanced_Text_Wrap.jsx`.

### Step-by-Step Workflow:
1. Select any **Point Text** or **Paragraph (Box) Text** layer in your active composition.
2. Adjust the **Max Text Width Constraint** (e.g., 600 px).
3. Select your desired **Wrap Method** (Balanced Paragraph, Greedy Fit, Optical Pyramid, Reverse Pyramid, or Compact Block).
4. Choose the text **Language** and toggle **Enable Multilingual Hyphenation**.
5. Click **"✨ Wrap Text into Pleasant Paragraph"**.
6. To revert at any time, simply click **"↺ Restore Original Text"**.

---

## 5. Mathematical Formulation

### Knuth-Plass Global Penalty Function
For a paragraph composed of $n$ tokens wrapped into $M$ visual lines with constraint width $W_{\text{max}}$:

$$\Delta_k = W_{\text{max}} - \text{Width}(\text{Line}_k)$$

$$P_{\text{line}}(k) = \begin{cases}
\infty & \text{if } \text{Width}(\text{Line}_k) > W_{\text{max}} \\
\Delta_k^2 + P_{\text{hyphen}} + P_{\text{ladder}} & \text{if } 1 \le k < M \\
P_{\text{widow}} & \text{if } k = M
\end{cases}$$

Where:
- $P_{\text{hyphen}} = 8000$ (discourages unnecessary hyphenation).
- $P_{\text{ladder}} = 20000$ (prevents consecutive line-ending hyphens).
- $P_{\text{widow}} = 50000$ if $\text{Width}(\text{Line}_M) < 0.25 \times W_{\text{max}}$ or if the line contains only 1 word; $0$ otherwise.

$$\text{Optimal Breaks} = \arg\min \sum_{k=1}^M P_{\text{line}}(k)$$

---

## 6. Related Repositories

- **[AE_JS_builder](https://github.com/work2survive/AE_JS_builder)**: Comprehensive After Effects JSX and ScriptUI web emulator.
- **[AE-Text-Highlight](https://github.com/work2survive/AE-Text-Highlight)**: Procedural sub-pixel vector typography highlighter.
- **[AE-replace-color-emojis](https://github.com/work2survive/AE-replace-color-emojis)**: Replaces monochrome font emojis with native color vector shapes.
- **[AE-Align-AnchorPoint](https://github.com/work2survive/AE-Align-AnchorPoint)**: Zero-shift parametric anchor point aligner with state storage.

---

## 7. License

Released under the **MIT License**. Free for commercial and non-commercial motion graphics and automation workflows.
