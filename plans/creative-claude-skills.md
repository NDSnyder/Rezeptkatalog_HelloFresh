# Notes: Creative Claude Code skills (3D, video, motion)

> Status: researched and code reviewed, nothing installed or run yet.
> Research date: 2026-11-10. Both projects came from posts on X; the claims were checked against the repositories themselves.

---

## Project 1: Cutaway — one image to a 3D simulator that takes itself apart

**Source:** a post by @Nyxera0x (2026-11-06) linking an X article, "How I Turn One Image Into a 3D Simulator That Takes Itself Apart".
**Repo:** https://github.com/Nyxera0x/cutaway (MIT)
**Install:** `npx skills add Nyxera0x/cutaway -g -a claude-code`

### What it does
- Starts from one reference image, for example a robotic animal made with ChatGPT.
- Claude rebuilds the object entirely in three.js code. It never imports the image.
- Claude compares the build against the image, grades a checklist, and measures proportions and how dark the image is.
- The result is a web page with a button for each part, plus a 60 fps video that shows the object coming apart part by part, with a synced, procedurally generated soundtrack.
- Three finished examples: tarantula, piranha and wasp, each as a full film plus a 10-second short.

### The method (from `SKILL.md` / `docs/`)
1. Reference image (a prompt for it is in `docs/PROMPTS.md`)
2. Measure the image: proportions, dark-pixel share
3. Write a checklist of 10–12 lines for this image
4. Still-image loop: build, render, compare side by side, grade. **Stop after round 2 and get human approval.**
5. Film: copy the closest example's choreography and swap in the new body
6. Its own short version (10 s)
7. Render and add the soundtrack

Key rules: start from the image, never from imagination; measure instead of guessing; give each part its own camera; review early (after round 2).

### Code review (done)
- Contents: a three.js kit, Playwright frame capture, a Python sound generator, and the method docs.
- Programs it starts: only `ffmpeg` and `python3`. None of its scripts make network calls.
- Install hooks: only esbuild (standard); fsevents runs on macOS only.
- Dependencies: three, esbuild, playwright, fonts.

### Requirements
- Node.js, Python 3, ffmpeg, Chromium. All are already in the cloud session.
- A GPU is optional, but without one video capture is noticeably slower.

### Status
`npm ci` was blocked by the session's safety check (installing code downloaded from outside). It needs explicit approval.

### Next step
Approve the install → build `piranha-demo` as a check → choose your own object (possibly recipe-themed, such as a robotic kitchen gadget).

---

## Project 2: A list of "11 free Claude skills" for video, motion, 3D and audio

**Source:** a post by @aifindr (2026-11-09; small account with about 110 followers, promotional list) with a 17-second video
"made entirely by Claude Code". It quotes an article by @dyfwsammy, "Design Engineering: Build a Motion Studio Around Opus 5.5".

### Fact check of the 11 repos
All were cloned and inspected, not installed. GitHub stars could not be retrieved (the API is blocked in the session).

| Skill | Last commit | Size | License | Verdict |
|---|---|---|---|---|
| [OpenMontage](https://github.com/OpenMontageInc/OpenMontage) | 2026-11-08 | ~186k lines of code, 141 skill files | **AGPL** | Real and big, but many features need **paid APIs** (HeyGen, ElevenLabs, Kling, …) |
| [ffmpeg-skill](https://github.com/kajisho5/ffmpeg-skill) | 2026-11-07 | ~65k lines, tests, MCP server | MIT | Real and solid. Recommended |
| [anything2explainer](https://github.com/Vincentwei1021/anything2explainer) | 2026-11-09 | ~9k lines | Own license | **Non-commercial use only** |
| [universal-modder](https://github.com/rehan-remade/universal-modder) | 2026-11-06 | ~21k lines, 39 skill files | MIT | Real and substantial (game modding) |
| [shader-skills](https://github.com/gokayfem/shader-skills) | 2026-10-23 | ~10k lines | MIT | Real; browser shaders that run in the cloud session. Recommended |
| [sonic-match-mcp](https://github.com/js713-lab/sonic-match-mcp) | 2026-10-21 | ~8.5k lines | MIT | An MCP server, not a skill; needs **API keys** (Gemini, Freesound, its own) |
| [cc-blender-skill](https://github.com/RobLe3/cc-blender-skill) | 2026-10-12 | ~4k lines of code, ~25k lines of docs | MIT | Mostly instructions; **needs Blender** |
| [blender-game-skills](https://github.com/majidmanzarpour/blender-game-skills) | 2026-09-23 | ~2.6k lines | MIT | Small; **needs Blender** |
| [product-launch-motion](https://github.com/AbubakrChan/product-launch-motion) | 2026-10-21 | ~1.3k lines of code | MIT | Small, mainly a recipe plus templates |
| [manim-skill](https://github.com/Yusuke710/manim-skill) | 2026-10-07 | ~800 lines | MIT | Thin: a good prompt with some helpers |
| [claude-motion-design](https://github.com/howseen-ai/claude-motion-design) | 2026-10-02 | ~770 lines | MIT | Thin: a good prompt with some helpers |

### Verdict
- **True:** all repos exist and are current; ten of the eleven are open source; several are serious engineering projects; the core claim (video, motion and 3D made through code) is plausible.
- **Overstated:** "free" only partly holds (OpenMontage and sonic-match depend on paid services); "no editor" hides the rounds of iteration and human judgement behind a polished clip; quality varies a lot between the skills.
- **The quoted article** is the more useful idea: write motion rules in a `MOTION.md`, store them as code tokens, add a linter in CI, and have Claude review each component, with the human making the final calls on taste. It is aimed at app interfaces.

### Before installing any of them
Review each repo individually (programs it starts, network calls, install hooks), as was done for cutaway.

---

## Recommendation

| Goal | Skill |
|---|---|
| 3D object that comes apart, plus a video | **cutaway** |
| Video editing, conversion, cutting | **ffmpeg-skill** |
| Shaders and visual effects in the browser | **shader-skills** |
| Short promo video, e.g. for the recipe catalogue | product-launch-motion or OpenMontage (watch the licence and paid APIs) |
| Real 3D assets (models, game assets) | Blender skills, which need Blender installed first |

Pick one concrete goal first, then the matching skill; don't install a dozen at once.
