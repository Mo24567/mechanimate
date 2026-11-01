# Mechanimate

**Winner — Best Use of Gemma 4**  
Hacktoberfest Hack Day Loughborough 2026 · DigiTech × MLH · Team of two

Mechanimate turns photographed or typed A-level mechanics questions into worked solutions and interactive animations. Students can inspect the motion, change physical parameters and practise individual steps with feedback and hints.

React · TypeScript · Express · Gemma 4 · Zod · SVG · KaTeX

[Run locally](#run-locally) · [Architecture](#architecture) · [Limitations](#limitations) · [Original team repository](https://github.com/krizhnx/mechanimate)

## What it does

- Reads a question and diagram from an image, or accepts typed text
- Produces worked solutions linked to animation steps and force highlights
- Supports projectile motion, straight-line forces, pulleys and inclined planes
- Recalculates motion and graphs when speed, angle, mass or friction changes
- Provides exam practice with numerical checks, feedback and progressive hints
- Checks supported numerical answers against a separate mechanics engine and can ask the model to correct inconsistencies

## Architecture

The React frontend sends a question to an Express API. Gemma returns a structured SceneSpec containing the topic, physical parameters, working and numerical answers. Zod validates that structure before a shared physics engine checks supported quantities. The frontend uses the same scene data to render an SVG animation.

| Route | Purpose |
|---|---|
| POST /api/solve | Interpret the question and return a validated scene and solution |
| POST /api/check | Give feedback on a practice attempt or generate a hint |
| POST /api/ask | Answer follow-up questions with the current scene as context |

The model handles interpretation and explanation. Local equations drive the animation and numerical comparisons. Changing a simulation parameter does not require a model call; generating revised written working uses the Re-solve action.

## Run locally

Use Node.js 22 or later, as specified by the original project. Live AI features require a Gemini API key.

```bash
git clone https://github.com/Mo24567/mechanimate.git
cd mechanimate
npm install
cp .env.example .env
# Set GEMINI_API_KEY in .env
npm run dev
```

Open http://localhost:5173. The API runs on port 8787. Keep API keys on the server and out of Git.

The bundled example questions work without a key. To try a live solve, upload an image or enter a question with a key configured.

```bash
npm test              # Physics regression checks
npm run typecheck     # TypeScript checks
npm run build         # Build the frontend
npm start             # Serve the production build and API
```

## Code guide

| File | Responsibility |
|---|---|
| src/shared/types.ts | SceneSpec contract shared by the model output, engine and UI |
| src/shared/physics.ts | Motion calculations and numerical verification |
| server/gemma.ts | Prompts, model calls, output repair, marking and tutor responses |
| server/schema.ts | Runtime validation and structured-output schema |
| src/components/scenes/ | SVG animation templates |
| src/examCheck.ts | Local numerical checks for practice attempts |
| scripts/test-physics.ts | Regression cases based on example mechanics problems |

## Limitations

This is a hackathon prototype. Physics checks establish consistency with the extracted parameters for supported quantities; they do not prove that the model interpreted the original question correctly. Unsupported quantities remain unchecked, and a correction attempt may leave mismatches.

The engine uses idealised A-level mechanics assumptions. Local answer checking compares numbers and does not fully validate a student’s reasoning or units. PDF import and phone QR scanning are preview interfaces, not completed integrations.

Presentation mode at `/?demo=1` replays a prepared projectile example for image uploads. It is not a live solve. Use `/?demo=0` to disable it; the setting persists in the browser.

## Team and development

Built by Mohamed Mohamud and Krishna Raman in a team of two using a shared laptop and extensive AI-assisted code generation with Claude. Our work focused on the idea, product behaviour and directing the implementation. This fork preserves the original team repository and commit history.

## License

Application code is available under the [MIT license](LICENSE). Model usage is governed separately by the provider’s terms.
