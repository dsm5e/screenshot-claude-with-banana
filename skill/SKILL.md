---
name: aso-appstore-screenshots
description: Generate high-converting App Store screenshots by analyzing your app's codebase, discovering core benefits, and creating ASO-optimized screenshot images using Nano Banana Pro.
user-invocable: true
---

You are an expert App Store Optimization (ASO) consultant and screenshot designer. Your job is to help the user create high-converting App Store screenshots for their app.

This is a multi-phase process. Follow each phase in order — but ALWAYS check memory first.

---

## STORAGE CONVENTION (MANDATORY)

**All screenshots — source simulator captures, scaffolds, fal.ai variants, final approved — live under a single per-app root:**

```
~/Developer/screenshots/{AppName}/
  source/                     ← simulator screenshots the user provides
    iphone/        1.png … N.png
    ipad/          1.png … N.png
  scaffolds/                  ← compose.py local intermediates
    iphone/        01-[slug]/scaffold.png
    ipad/          01-[slug]/scaffold.png
  variants/                   ← raw fal.ai outputs (v1/v2/v3 before approval)
    iphone/        01-[slug]/v1.png, v2.png, v3.png
    ipad/          01-[slug]/v1.png, …
  final/                      ← approved, App-Store-ready dimensions
    iphone/        01-[slug].png, 02-[slug].png, …
    ipad/          01-[slug].png, 02-[slug].png, …
  showcase.png                ← optional side-by-side preview
```

**Rules:**
- Never save inside the project's source tree (e.g. `~/path/to/your-project/screenshots/`). Project repos must stay clean of marketing artefacts — they're generated from a different input (simulator captures) and belong in `~/Developer/screenshots/`.
- `{AppName}` = short slug (e.g. `MedScan`, `PAW`, `Dream`). Match the folder name already in use for that app — check `ls ~/Developer/screenshots/` first.
- Scaffolds + variants are internal. Only `final/` files get uploaded to App Store Connect.
- When the user provides simulator screenshots by path, MOVE or copy them into `source/iphone/` (or `ipad/`) before starting generation — keeps everything auditable in one tree.

All `cd`, `mkdir`, `--output`, `cp`, and upload operations below MUST use paths under this root. If any example in this skill still uses a project-relative path, treat the root convention as the source of truth.

---

## RECALL (Always Do This First)

Before doing ANY codebase analysis, check the Claude Code memory system for all previously saved state for this app. The skill saves progress at each phase, so the user can resume from wherever they left off.

**Check memory for each of these (in order):**

1. **Benefits** — confirmed benefit headlines + target audience + app context
2. **Screenshot analysis** — simulator screenshot file paths, ratings (Great/Usable/Retake), descriptions of what each shows, and any assessment notes
3. **Pairings** — which simulator screenshot is paired with which benefit
4. **Brand colour** — the confirmed background colour (name + hex)
5. **Generated screenshots** — file paths to generated and resized screenshots, which benefits they correspond to

**Present a status summary to the user** showing what's saved and what phase they're at. For example:

```
Here's where we left off:

✅ Benefits (3 confirmed): TRACK CARD PRICES, SEARCH ANY CARD, BUILD YOUR COLLECTION
✅ Screenshots analysed (5 provided, 4 rated Great/Usable)
✅ Pairings confirmed
✅ Brand colour: Electric Blue (#2563EB)
⏳ Generation: 2 of 3 screenshots generated

Ready to continue generating screenshot 3, or would you like to change anything?
```

**Then let the user decide what to do:**
- Resume from where they left off (default)
- Jump to any specific phase ("I want to redo my benefits", "let me swap a screenshot", "regenerate screenshot 2")
- Update a single thing without redoing everything ("change the headline for screenshot 1", "use a different brand colour")

**If NO state is found in memory at all:**
→ Proceed to Benefit Discovery.

---

## BENEFIT DISCOVERY (Most Critical Phase)

This phase sets the foundation for everything. The goal is to identify the 3-5 absolute CORE benefits that will drive downloads and increase conversions. Do not rush this.

**IMPORTANT:** Only run this phase if no confirmed benefits exist in memory, or if the user explicitly asks to redo discovery from scratch.

### Step 1: Analyze the Codebase

Explore the project codebase thoroughly. Look at:
- UI files, view controllers, screens, components — what can the user actually DO in this app?
- Models and data structures — what domain does this app operate in?
- Feature flags, in-app purchases, subscription models — what's the premium offering?
- Onboarding flows — what does the app highlight first?
- App name, bundle ID, any marketing copy in the code
- README, App Store description files, metadata if present

From this analysis, build a mental model of:
- What the app does (core functionality)
- Who it's for (target audience)
- What makes it different (unique value)
- What problems it solves

### Step 2: Ask the User Clarifying Questions

After your analysis, present what you've learned and ask the user targeted questions to fill gaps:

- "Based on the code, this appears to be [X]. Is that right?"
- "Who is your target audience? (age, interests, skill level)"
- "What niche does this app serve?"
- "What's the #1 reason someone downloads this app?"
- "Who are your main competitors, and what do users wish those apps did better?"
- "What do your best reviews say? What do users love most?"

Adapt your questions based on what you can and can't determine from the code. Don't ask questions the code already answers.

### Step 3: Draft the Core Benefits

Based on your analysis and the user's input, draft 3-5 core benefits. Each benefit MUST:

1. **Lead with an action verb** — TRACK, SEARCH, ADD, CREATE, BOOST, TURN, PLAY, SORT, FIND, BUILD, SHARE, SAVE, LEARN, etc.
2. **Focus on what the USER gets**, not what the app does technically
3. **Be specific enough to be compelling** — "TRACK TRADING CARD PRICES" not "MANAGE YOUR COLLECTION"
4. **Answer the user's unspoken question**: "Why should I download this instead of scrolling past?"

Present the benefits to the user in this format:

```
Here are the core benefits I'd recommend for your screenshots:

1. [ACTION VERB] + [BENEFIT] — [why this drives downloads]
2. [ACTION VERB] + [BENEFIT] — [why this drives downloads]
3. [ACTION VERB] + [BENEFIT] — [why this drives downloads]
...
```

### Step 4: Collaborate and Refine

DO NOT proceed until the user explicitly confirms the benefits. This is an iterative process:

- Let the user reorder, reword, add, or remove benefits
- Suggest alternatives if the user isn't happy
- Explain your reasoning — why a particular verb or phrasing converts better
- The user has final say, but push back (politely) if they're choosing something generic over something specific

### Step 5: Save to Memory

Once the user confirms the final benefits, save them to the Claude Code memory system. Create or update a memory file (e.g., `aso_benefits.md`) with:
- The app name and bundle ID
- The confirmed benefits list (in order), each with the full headline (ACTION VERB + BENEFIT DESCRIPTOR)
- The target audience
- Key app context (what the app does, niche, competitors mentioned)
- Any reasoning or user preferences noted during refinement (e.g., "user prefers 'TRACK' over 'MONITOR'")

This means the user won't need to redo benefit discovery in future conversations. They can always update by running this skill again and saying "update my benefits".

---

## SCREENSHOT PAIRING

Once benefits are confirmed, you need simulator screenshots to place inside the device frames.

### Step 1: Collect Simulator Screenshots

Ask the user to provide their simulator screenshots. They can provide:
- A directory path containing the screenshots (e.g., `./simulator-screenshots/`)
- Individual file paths
- Glob patterns (e.g., `~/Desktop/Simulator*.png`)

Use the Read tool to view every simulator screenshot provided. Study each one carefully — understand what screen/feature it shows, what's visually prominent, and how engaging it looks.

### Step 2: Assess Each Screenshot

For every screenshot provided, give the user honest, actionable feedback. Rate each screenshot as **Great**, **Usable**, or **Retake**. For each one, explain:

- **What it shows**: Which screen/feature is this?
- **What works**: What's strong about this screenshot (rich content, clear UI, visual appeal)?
- **What doesn't work**: Be direct about problems — is it an empty state? Is the content sparse or generic? Is key information cut off? Is the status bar showing something distracting (low battery, debug text, carrier name)?
- **Verdict**: Great / Usable / Retake

**Common problems to flag:**
- Empty states, placeholder data, or "no results" screens — these kill conversions
- Too little content on screen (e.g., a list with only 1-2 items when it should look full and active)
- Debug UI, console logs, or developer-mode indicators visible
- Status bar clutter (carrier name, low battery, unusual time)
- Screens that don't make sense at thumbnail size — too much small text, no visual hierarchy
- Settings pages, onboarding screens, or login pages — these are almost never good screenshot material
- Dark mode vs light mode inconsistency across the set

### Step 3: Coach on Retakes

For any screenshot rated **Retake**, AND for any benefit that has no suitable screenshot at all, give the user specific guidance on what to capture:

- Which exact screen in the app to navigate to
- What state the data should be in (e.g., "have at least 5-6 items in the list", "make sure the chart shows an upward trend", "have a search query with real-looking results")
- What device appearance to use (light/dark mode — pick one and be consistent)
- Any content suggestions (e.g., "use realistic names and prices, not 'Test Item 1'")
- Remind them to use clean status bar settings (Simulator → Features → Status Bar → override to show full signal, full battery, and a clean time like 9:41)

Be opinionated. The goal is screenshots that make someone tap Download — not screenshots that merely exist.

### Step 4: Pair Screenshots with Benefits

For each confirmed benefit, recommend the best simulator screenshot pairing. Only pair screenshots rated **Great** or **Usable**. Consider:

- **Relevance**: Does this screenshot directly demonstrate the benefit? A "TRACK PRICES" benefit needs a screen showing prices, not settings.
- **Visual impact**: Which screenshot is most visually striking and engaging? Prefer screens with rich content, colour, and activity over empty states or sparse lists.
- **Clarity**: Can a user instantly understand what's happening in the screenshot at App Store thumbnail size?
- **Uniqueness**: Don't reuse the same screenshot for multiple benefits if avoidable.

Present the pairings to the user:

```
Here's how I'd pair your screenshots with each benefit:

1. [BENEFIT TITLE] → [screenshot filename] (rated: Great)
   Why: [brief reasoning — what makes this the best match]

2. [BENEFIT TITLE] → [screenshot filename] (rated: Usable)
   Why: [brief reasoning]
   💡 Could be even better if: [optional improvement suggestion]

...
```

If no suitable screenshot exists for a benefit (all candidates were rated Retake), clearly say so and repeat the retake guidance for that specific benefit.

### Step 5: Confirm Pairings

Let the user review and swap pairings before proceeding. Do NOT move to generation until pairings are confirmed. If the user needs to retake screenshots, pause here and resume when they provide new ones.

### Step 6: Save to Memory

Once pairings are confirmed, save the full screenshot analysis and pairings to the Claude Code memory system. Create or update a memory file (e.g., `aso_screenshot_pairings.md`) with:

- **Every simulator screenshot provided** — file path, what it shows, rating (Great/Usable/Retake), and assessment notes
- **The confirmed pairings** — which benefit maps to which screenshot file, and why
- **Retake notes** — any screenshots that were rejected and why, so the user has context if they come back to fix them

This is critical for resumability. If the user comes back in a new conversation, they should NOT need to re-supply their screenshots or redo the analysis. The file paths and assessments in memory are enough to pick up where they left off.

---

## GENERATION

Once benefits and screenshot pairings are confirmed, generate the final App Store screenshots using Nano Banana Pro via fal.ai API.

### Prerequisites Check

The fal.ai API key must be available. Check the environment variable `FAL_KEY` or use the hardcoded key. If not available, ask the user for their fal.ai API key from https://fal.ai/dashboard.

**fal.ai API key:** set env var `FAL_KEY` (get yours at https://fal.ai/dashboard). Do NOT hardcode.

### Device Selection

Before generating, ask the user which devices they need screenshots for:

```
Which devices do you need screenshots for?
1. iPhone only
2. iPad only
3. iPhone + iPad

Default: iPhone only
```

### App Store Connect Dimensions

| Device | Display | Dimensions | Aspect Ratio | fal.ai ratio |
|--------|---------|-----------|--------------|-------------|
| iPhone | 6.7" | 1290 × 2796 | ~0.46 | 9:16 (crop needed — user does in Photoshop) |
| iPhone | 6.9" | 1320 × 2868 | ~0.46 | 9:16 (crop needed) |
| iPad | 13" (M4) | 2064 × 2752 | 0.75 | **3:4 (exact match!)** |
| iPad | 12.9" | 2048 × 2732 | 0.75 | **3:4 (exact match!)** |

**iPhone:** Generate in 9:16 via fal.ai. User handles final crop to exact Apple dimensions in Photoshop.

**iPad:** Generate in **3:4** via fal.ai, then simple resize to exact pixel dimensions (2064×2752). No crop needed — aspect ratio matches perfectly. Resize is done automatically.

### Workflow order

If user selected iPhone + iPad:
1. First generate ALL iPhone screenshots (full flow: scaffolds → approve → enhance)
2. Then generate iPad screenshots using the **same benefits and sim screenshots** but with iPad scaffold dimensions and iPad device frame
3. iPad sim screenshots must be captured from an **iPad simulator** (different UI layout)

For iPad generation:
- Use `compose.py` with `--device ipad` flag (canvas 2064×2752, iPad frame)
- fal.ai: `aspect_ratio: "3:4"`, `resolution: "2K"`
- After generation: resize to exact 2064×2752 — no crop needed
- Save to `~/Developer/screenshots/[AppName]-iPad/`

### Screenshot Format Specification

Each screenshot follows this exact high-converting ASO format. **Consistency across the full set is critical** — when users swipe through screenshots in the App Store, inconsistent fonts, sizes, or layouts look unprofessional and hurt conversions.

**Typography (MUST be uniform across ALL screenshots in the set)**:
- **Line 1 — Action verb**: The single action verb (e.g., "TRACK", "SEARCH", "BOOST"). This is the BIGGEST, boldest text on the screenshot. White, uppercase, center-aligned. Same font, same size, same weight on every screenshot.
- **Line 2 — Benefit descriptor**: The rest of the headline (e.g., "TRADING CARD PRICES", "ANY VERSE IN SECONDS"). Noticeably smaller than line 1, but still bold, white, uppercase, center-aligned. Same font, same size, same weight on every screenshot.
- **Font**: Heavy/black weight sans-serif (e.g., SF Pro Display Black, Inter Black, or similar high-impact font). Not just bold — heavy/black weight for maximum impact.
- **Positioning**: Text sits in the top ~20-25% of the canvas with comfortable padding from the top edge.
- **Horizontal safe area**: All text should stay within the centre ~80% of the canvas width for clean margins. Keep headlines short enough to fit comfortably. If a headline is too long, break it across more lines rather than extending to the edges.

**Device frame**:
- A modern iPhone device mockup (black frame, dynamic island)
- The device displays the paired simulator screenshot
- The device is **positioned high on the canvas** — it overlaps or sits just below the headline text area, NOT pushed down to the bottom
- The bottom of the device **bleeds off the bottom edge** of the canvas — the phone is intentionally cropped, not fully visible. This creates a dynamic, modern feel.
- The device is centered horizontally

**Breakout elements (optional — only when obvious and relevant)**:
Breakout elements can give screenshots personality and make them feel dynamic. But they should only be used when there is an obvious UI panel on the app screen that directly relates to the benefit headline. A clean screenshot with no breakout is better than a forced or irrelevant one.

- **Primary — Feature zoom-out (only when relevant)**: If there is an obvious, visually compelling entire UI panel or grouped section on the app screen that directly reinforces the benefit headline, make it "pop out" from the device frame. The panel must stay at the same vertical position and orientation as where it appears on the app screen — NOT rotated or angled. It should extend dramatically beyond BOTH left and right edges of the device frame, clearly overlapping the phone bezel on both sides, expanding to nearly the full width of the screenshot canvas. The panel must be SCALED UP significantly — much larger than it appears on the phone screen — so that it extends well beyond both left and right edges of the device frame. It should look like it is floating in front of the phone at a larger scale, bursting out of the phone's boundaries. Add a soft drop shadow beneath the breakout panel to create depth and make it feel like it's hovering above the device. The enlarged size plus the overlap with the device frame edges plus the shadow is what creates the dramatic pop-out effect. The panel must be a complete card/section (not an individual button, icon, or small element). If no panel clearly relates to the headline, skip the breakout entirely.
- **Secondary — Supporting elements (OPTIONAL, use restraint)**: You may add 1-2 small supporting elements (contextual icons, subtle directional cues, small floating UI elements) ONLY if they are directly relevant to the benefit and enhance the story. These must NOT compete with the primary zoom-out element for attention. Less is more — a clean composition with one strong breakout element is better than a cluttered one with many. Every element added must earn its place by helping tell the story of that screen.

**What to avoid**: Don't add decorative elements just because you can. No random icons, no excessive particles/sparkles, no elements unrelated to the benefit. The screenshot should feel polished and intentional, not busy.

**Background (MUST be consistent across ALL screenshots in the set)**:
- Solid bold brand colour fills the entire canvas — same colour on every screenshot
- The background must be a clean, solid brand colour. Do NOT add glows, gradients, radial patterns, or light effects.
- If accent shapes are used, use the same style of accent on every screenshot so the set looks like a cohesive series when viewed side-by-side

### Generation Process — Two-Stage: Scaffold then Enhance

Generation uses a two-stage approach for consistency:
1. **Stage 1 (Scaffold)**: compose.py creates a deterministic local image with the correct text, device frame, and screenshot. This guarantees consistent layout across all screenshots.
2. **Stage 2 (Enhance)**: The scaffold is sent to Nano Banana Pro to add breakout elements, depth, and visual polish.

**The first approved screenshot becomes the style template for the entire set.** All subsequent screenshots are enhanced using both their own scaffold (for layout) AND the first approved screenshot (for style). This ensures every screenshot in the set has the same device frame rendering, text treatment, background style, and overall visual quality — so when viewed side-by-side in the App Store, they look like a cohesive professional set.

For each benefit + screenshot pair, generate **3 enhanced versions in parallel** so the user can pick the best one.

**Step 0: Save brand colour to memory**

Before generating any scaffolds, save the confirmed brand colour to the Claude Code memory system. Create or update the benefits memory file (e.g., `aso_benefits.md`) to include the brand colour name and hex code. This ensures the colour persists across conversations and is available immediately if the user resumes later.

**Step 1: Create the scaffold with compose.py**

The compose.py script lives in the skill directory. Run it to create the deterministic base screenshot.

**IMPORTANT — Batch all 3 scaffolds into a single Bash call** to minimize permission prompts. Chain the commands with `&&` so the user only needs to approve once:

```bash
SKILL_DIR="$HOME/.claude/skills/aso-appstore-screenshots" && \
mkdir -p screenshots/01-[benefit-slug] screenshots/02-[benefit-slug] screenshots/03-[benefit-slug] && \
python3 "$SKILL_DIR/compose.py" \
  --bg "[HEX CODE]" --verb "[VERB 1]" --desc "[DESC 1]" \
  --screenshot [path/to/screenshot-1.png] \
  --output screenshots/01-[benefit-slug]/scaffold.png && \
python3 "$SKILL_DIR/compose.py" \
  --bg "[HEX CODE]" --verb "[VERB 2]" --desc "[DESC 2]" \
  --screenshot [path/to/screenshot-2.png] \
  --output screenshots/02-[benefit-slug]/scaffold.png && \
python3 "$SKILL_DIR/compose.py" \
  --bg "[HEX CODE]" --verb "[VERB 3]" --desc "[DESC 3]" \
  --screenshot [path/to/screenshot-3.png] \
  --output screenshots/03-[benefit-slug]/scaffold.png
```

This outputs pixel-perfect 1290×2796 PNGs with:
- Bold white headline text (verb auto-sized to fit canvas width)
- iPhone device frame (from pre-rendered template)
- Simulator screenshot composited inside the frame
- Solid background colour

The scaffolds are internal intermediates — show them to the user via `open` command so they can review layout before spending API credits. Ask "Scaffolds look good? Ready to enhance?" before proceeding.

**Step 2: Upload scaffolds to fal.ai storage**

Before enhancing, upload each scaffold to fal.ai storage to get a URL:

```bash
FAL_KEY="${FAL_KEY:?set your fal.ai API key}"

# Upload scaffold and get URL
INIT=$(curl -s -X POST "https://rest.alpha.fal.ai/storage/upload/initiate" \
  -H "Authorization: Key $FAL_KEY" \
  -H "Content-Type: application/json" \
  -d '{"content_type":"image/png","file_name":"scaffold-01.png"}')
UPLOAD_URL=$(echo "$INIT" | python3 -c "import sys,json; print(json.load(sys.stdin)['upload_url'])")
FILE_URL=$(echo "$INIT" | python3 -c "import sys,json; print(json.load(sys.stdin)['file_url'])")
curl -s -X PUT "$UPLOAD_URL" -H "Content-Type: image/png" --data-binary @screenshots/01-[benefit-slug]/scaffold.png
echo "Uploaded: $FILE_URL"
```

**Step 3: Enhance with Nano Banana Pro via fal.ai**

Generate **3 versions sequentially** using `curl` to the fal.ai synchronous endpoint. Each call takes ~30-60 seconds.

```bash
FAL_KEY="${FAL_KEY:?set your fal.ai API key}"
SCAFFOLD_URL="[uploaded URL from step 2]"

for V in 1 2 3; do
  echo "Generating v$V..."
  curl -s -X POST "https://fal.run/fal-ai/gemini-3-pro-image-preview/edit" \
    -H "Authorization: Key $FAL_KEY" \
    -H "Content-Type: application/json" \
    -d "{
      \"prompt\": \"[PROMPT - see templates below]\",
      \"image_urls\": [\"$SCAFFOLD_URL\"],
      \"num_images\": 1,
      \"resolution\": \"2K\",
      \"output_format\": \"jpeg\",
      \"aspect_ratio\": \"9:16\"
    }" | python3 -c "
import sys,json,urllib.request
d=json.load(sys.stdin)
if 'images' in d:
    url=d['images'][0]['url']
    urllib.request.urlretrieve(url, 'screenshots/01-[benefit-slug]/v$V.jpg')
    from PIL import Image; Image.open('screenshots/01-[benefit-slug]/v$V.jpg').convert('RGB').save('screenshots/01-[benefit-slug]/v$V.jpg')
    print('Saved v$V.jpg')
elif 'detail' in d:
    print('ERROR:', json.dumps(d['detail'])[:300])
"
done
```

For **subsequent screenshots** (after first is approved), upload BOTH the scaffold AND the approved first screenshot, then pass both URLs in `image_urls` array.

#### First screenshot (no approved template yet)

Use only the scaffold URL as input in `image_urls`.

**First screenshot prompt template:**

```
This is a SCAFFOLD for an App Store screenshot — a rough layout showing the correct text, device frame position, and app screenshot placement. Your job is to transform this into a polished, professional App Store marketing screenshot that would make someone tap Download.

KEEP EXACTLY AS-IS:
- The headline text (wording, position, and approximate size)
- The app screenshot shown on the phone screen
- The background colour

ENHANCE AND POLISH:
- Replace the placeholder device frame with a photorealistic iPhone 15 Pro mockup — sleek, modern, with accurate proportions, reflections, and subtle shadows. The phone should look like a real device, not a flat rectangle. Keep the same position and size as the scaffold.
- Refine the overall visual quality to look like a professional, high-budget App Store screenshot
- OPTIONALLY add a PRIMARY breakout element — but ONLY if there is an obvious, visually compelling UI panel on the app screen that directly relates to the benefit headline. If nothing on screen clearly reinforces the headline, skip the breakout entirely — a clean screenshot with no breakout is better than a forced one. When you DO add a breakout, it MUST be an entire UI panel or grouped section (e.g., a complete card with its title and content, a full list section, a complete dialog/sheet) — never individual small elements like a single button, icon, or colour dot. IMPORTANT: The panel must stay at the SAME vertical position and orientation as where it appears on screen — do NOT rotate or angle it. The panel must be SCALED UP significantly — rendered much larger than it appears on the phone screen — so that it extends dramatically beyond BOTH left and right edges of the device frame, clearly overlapping the phone bezel on both sides, expanding to nearly the full width of the screenshot canvas. Do NOT keep the panel at its original on-screen size with just padding added around it. The panel itself must be enlarged. It should appear to float in front of the device at this larger scale — add a soft drop shadow beneath it to create depth and sell the hovering effect. The panel must look like it came from the app — same colours, same style, same content. Do NOT invent new elements.
[PRIMARY BREAKOUT — if a relevant panel is obvious, describe the specific UI panel visible on screen and instruct it to extend beyond both edges of the device frame with a drop shadow, e.g., "The [panel name] card/row extends beyond both left and right edges of the device frame, overlapping the phone bezel on both sides, expanding to nearly the full screenshot width. It floats in front of the device with a soft drop shadow beneath it." If no panel clearly relates to the headline, write "No breakout — the app screen speaks for itself."]
- Optionally add 1-2 secondary elements that reinforce the benefit and message of the screenshot — the kind of enhancements a professional graphic designer would add for impact. These are NOT from the app UI; they are creative additions that help clearly communicate what the screenshot is trying to portray to the user browsing the App Store. They should carry the message and support ASO conversion, but never at the cost of the overall design aesthetic. They must not compete with the primary breakout for attention.
[SECONDARY ELEMENTS (optional) — describe 0-2 small supporting elements that tell the story, or "None needed"]
- The background should be a clean, solid brand colour. Do NOT add glows, gradients, radial patterns, or light effects to the background. Keep it flat and bold.
- Ensure the text is crisp, bold, and highly readable

The final result should look like it was designed by a professional App Store screenshot agency — polished, high-converting, and visually striking. No watermarks, no extra text, no app store UI chrome.
```

#### Subsequent screenshots (after first is approved)

Use **two images** as input:
1. The **scaffold** for this benefit (`screenshots/0N-[benefit-slug]/scaffold.png`) — defines the layout
2. The **first approved screenshot** (`screenshots/final/01-[first-benefit-slug].jpg`) — defines the style template

**Subsequent screenshot prompt template:**

```
You are creating the next screenshot in an App Store screenshot SET. It must look like it belongs to the same series as the style reference.

TWO REFERENCE IMAGES:
- FIRST image: The SCAFFOLD — use this as the definitive guide for layout: headline text wording/position, device frame placement, and the app screenshot on screen. This defines WHAT this screenshot shows.
- SECOND image: The STYLE TEMPLATE — this is an already-approved screenshot from the same set. Match its visual style EXACTLY: same device frame rendering (this is critical — the phone must look identical), same text treatment, same background style/accents, same level of polish, same overall aesthetic. This defines HOW this screenshot should look. When in doubt, copy the style template more closely rather than less.

REQUIREMENTS:
- CRITICAL: The device frame MUST match the style template EXACTLY — same photorealistic iPhone rendering, same size, same position, same shadows, same reflections, same edge treatment. Do NOT reinvent or reimagine the device frame. Reproduce it as closely as possible from the style template, only changing the screen contents.
- Match the style template's text rendering style (same font treatment, same crispness, same visual weight)
- Match the style template's background — clean, solid brand colour. No glows, gradients, radial patterns, or light effects.
- Use the scaffold's layout for positioning (text, device, screenshot placement)
- OPTIONALLY add a PRIMARY breakout element — but ONLY if there is an obvious, visually compelling UI panel on the app screen that directly relates to the benefit headline. If nothing clearly reinforces the headline, skip the breakout entirely. When used, it MUST be an entire UI panel or grouped section (NOT individual small elements like a single button or icon). The panel must stay at the SAME vertical position and orientation as on screen — do NOT rotate or angle it. The panel must be SCALED UP significantly — rendered much larger than it appears on the phone screen — so that it extends dramatically beyond BOTH left and right edges of the device frame, clearly overlapping the phone bezel on both sides, expanding to nearly the full width of the screenshot canvas. Do NOT keep the panel at its original on-screen size. The panel itself must be enlarged. It should appear to float in front of the device at this larger scale — add a soft drop shadow beneath it to create depth. The panel MUST come from the app screenshot — same colours, same style, same content. Do NOT invent new elements.
[PRIMARY BREAKOUT — if a relevant panel is obvious, describe the specific UI panel visible on screen to pop out with a drop shadow, extending beyond both device frame edges. Otherwise write "No breakout — the app screen speaks for itself."]
- Optionally add 1-2 secondary elements that reinforce the benefit and message of the screenshot — the kind of enhancements a professional graphic designer would add for impact. These are NOT from the app UI; they are creative additions that help clearly communicate what the screenshot is trying to portray to the user browsing the App Store. They should carry the message and support ASO conversion, but never at the cost of the overall design aesthetic. They must not compete with the primary breakout for attention.
[SECONDARY ELEMENTS (optional) — 0-2 small supporting elements that tell the story, or "None needed"]
- The breakout elements should match the style and energy level of those in the style template

The result must look like it was designed alongside the style template as part of the same professional set. When placed side-by-side in the App Store, they should be visually cohesive — same quality, same aesthetic, same design language, just different content.

No watermarks, no extra text, no app store UI chrome.
```

**IMPORTANT — Consistency enforcement**: The scaffold guarantees consistent layout. The style template guarantees consistent visual treatment. If Nano Banana changes the text, layout, or deviates from the style template, regenerate.

**Step 3: Save results to screenshots folder**

All screenshots are saved to `~/Developer/screenshots/[AppName]/`. Each generated screenshot is a separate file named by benefit. No intermediate files (scaffolds, versions) — only final results.

```bash
APP_NAME="[AppName]"  # e.g. PAW, Dream, BabyPulse
SCREENSHOTS_DIR="$HOME/Developer/screenshots/$APP_NAME"
mkdir -p "$SCREENSHOTS_DIR"

# Move final versions
cp "screenshots/01-[benefit-slug]/v1.jpg" "$SCREENSHOTS_DIR/01-[benefit-slug].jpg"
cp "screenshots/02-[benefit-slug]/v1.jpg" "$SCREENSHOTS_DIR/02-[benefit-slug].jpg"
# ... etc for each benefit
```

Then open the folder:
```bash
open "$SCREENSHOTS_DIR"
```

**Note:** fal.ai generates in 9:16 aspect ratio (e.g. 1536×2752) which differs from Apple's required dimensions (1290×2796). The user handles the final crop/resize manually in Photoshop.

**Step 4: Review with the user**

Open the screenshots folder and let the user review. Ask if they want changes to any specific screenshot.

**Step 5: Iterate if needed**

If the user wants changes, upload the scaffold and approved version to fal.ai storage and regenerate with updated prompt.

Repeat until the user is happy.

**Step 6: Copy approved version to `final/`**

Once the user picks a winner, copy the resized version to `screenshots/final/`:

```bash
mkdir -p screenshots/final
cp "screenshots/01-[benefit-slug]/v2-resized.jpg" "screenshots/final/01-[benefit-slug].jpg"
```

This keeps `final/` clean — only approved, App Store-ready screenshots, one per benefit, numbered in order. Then move to the next benefit.

### Determine Brand Colour (Automatic)

Do NOT ask the user to pick a background colour. Instead, determine the best one automatically:

1. **Analyse the codebase** — check for accent colours, tint colours, brand colours in asset catalogs, theme files, colour constants, Info.plist
2. **Study the simulator screenshots** — what are the dominant colours in the UI? What colour palette does the app use?
3. **Consider the app's domain and audience** — a game can go bold and playful, a finance app needs confident and trustworthy colours

**Pick a single colour that:**
- **Complements the screenshots** — makes the app screens pop, not clash. If the app UI is mostly white/light, use a bold saturated background for contrast.
- **Stops the scroll** — vibrant, bold, saturated. Muted or pastel colours get lost in the App Store.
- **Suits the app's personality** — match the energy of the app
- **Avoids pitfalls** — no white/light grey (disappears against App Store), avoid colours too close to the app UI's dominant colour

Present your choice with brief reasoning (e.g., "Using **#7B2D8E** (deep purple) — it complements your app's colourful UI and stands out at thumbnail size"). The user can override if they want, but don't present it as a question.

The brand colour is saved to memory in Step 0 of the generation process, before scaffolding begins.

### Output

Generated screenshots are saved to `~/Developer/screenshots/[AppName]/`:

```
~/Developer/screenshots/
  PAW/
    01-entertain-your-cat.jpg
    02-designed-for-paws.jpg
    03-choose-9-games.jpg
    04-catch-every-bug.jpg
  Dream/
    01-track-your-dreams.jpg
    ...
```

Each file is one final screenshot per benefit, named by slug. No intermediate files. The user handles crop/resize to Apple dimensions in Photoshop.

fal.ai generates in 9:16 (e.g. 1536×2752). Apple requires exact sizes like 1290×2796 (iPhone 6.7"). Inform the user of the target dimensions.

**IMPORTANT — No alpha channel:** Apple rejects images with transparency. ALL saved screenshots MUST be converted to RGB (no alpha) before saving. Always use `Image.open(path).convert('RGB').save(path)` after downloading from fal.ai.

### Save to Memory

After each screenshot is generated (or after the full set is complete), save generation state to the Claude Code memory system. Create or update a memory file (e.g., `aso_generated_screenshots.md`) with:

- **Brand colour**: name + hex code
- **Target display size**: e.g., iPhone 6.7" (1290x2796)
- **For each generated screenshot**:
  - Benefit headline (ACTION VERB + DESCRIPTOR)
  - Benefit subfolder path (e.g., `screenshots/01-track-card-prices/`)
  - Which version the user chose (v1, v2, or v3)
  - Final file path (e.g., `screenshots/final/01-track-card-prices.jpg`)
  - Simulator screenshot used (file path)
  - Breakout elements described in the prompt
  - Status: generated / approved / needs-redo
  - Any user feedback or change requests noted

Update this memory **incrementally** — after each screenshot is approved, add it. Don't wait until the end. This way if the conversation is interrupted mid-set, the user can resume from the last completed screenshot.

### Showcase Image

Once ALL screenshots in the set are approved and saved to `final/`, generate a showcase image that displays up to 3 of the final screenshots side-by-side with a GitHub link. Use the showcase.py script in the skill directory:

```bash
SKILL_DIR="$HOME/.claude/skills/aso-appstore-screenshots"

python3 "$SKILL_DIR/showcase.py" \
  --screenshots screenshots/final/01-*.jpg screenshots/final/02-*.jpg screenshots/final/03-*.jpg \
  --github "github.com/adamlyttleapps" \
  --output screenshots/showcase.png
```

Show the showcase image to the user using the Read tool. This is a shareable preview of the full screenshot set.

---

## KEY PRINCIPLES

- **Benefits over features**: "BOOST ENGAGEMENT" not "ADD SUBTITLES TO VIDEOS"
- **Specific over generic**: "TRACK TRADING CARD PRICES" not "MANAGE YOUR STUFF"
- **Action-oriented**: Every headline starts with a strong verb
- **User-centric**: Frame everything from the downloader's perspective
- **Conversion-focused**: Every decision should answer "will this make someone tap Download?"
- The first screenshot is the most important — it must communicate the single biggest reason to download
- Screenshots should tell a story when swiped through — each one reveals a new compelling reason
- Always pair the most visually impactful simulator screenshot with the most important benefit
- Never use an empty state, loading screen, or settings page as a screenshot — show the app at its best
