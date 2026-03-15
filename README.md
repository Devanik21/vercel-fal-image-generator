# Vercel Fal Image Generator

![Language](https://img.shields.io/badge/Language-TypeScript-3178C6?style=flat-square) ![Stars](https://img.shields.io/github/stars/Devanik21/vercel-fal-image-generator?style=flat-square&color=yellow) ![Forks](https://img.shields.io/github/forks/Devanik21/vercel-fal-image-generator?style=flat-square&color=blue) ![Author](https://img.shields.io/badge/Author-Devanik21-black?style=flat-square&logo=github) ![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)

> Serverless AI image generation at zero infrastructure cost — Vercel Fal Image Generator deployed on Vercel with fal.ai GPU backend.

---

**Topics:** `api` · `diffusion-models` · `edge-inference` · `fal-ai` · `generative-ai` · `text-to-image` · `vercel` · `web-app` · `fast-generation` · `real-time-image-generation`

## Overview

Vercel Fal Image Generator is a Next.js web application that exposes a polished image generation interface, deployed on Vercel's edge network, with all GPU-intensive inference offloaded to fal.ai's serverless infrastructure. The result is a production-grade generative AI application with no GPU management, no server maintenance, and sub-second cold-start times.

The frontend is built with React and Tailwind CSS, providing a clean prompt input panel with controls for negative prompt, image size, number of inference steps, guidance scale, and model selection. Generation requests are sent to a Next.js API route, which proxies them to the fal.ai endpoint using the fal-js SDK. Results are streamed back to the client progressively as generation proceeds.

The application includes a prompt history gallery persisted in localStorage, a prompt enhancement feature that uses an LLM to expand terse prompts into detailed, style-rich descriptions, and a side-by-side comparison mode for evaluating the same prompt across different models or parameter settings.

---

## Motivation

The barrier to deploying a functional AI image generator has dropped dramatically with fal.ai's serverless GPU API and Vercel's frontend hosting. This project demonstrates how to build a fully production-ready generative image application without managing a single server or paying for idle GPU time — the compute bill is strictly pay-per-generation.

---

## Architecture

```
React Frontend (Next.js, Tailwind)
        │
  Next.js API Route (/api/generate)
        │
  fal.ai SDK → Serverless GPU (FLUX / SDXL)
        │
  Image URL streamed back to client
        │
  Gallery display + localStorage persistence
```

Vercel handles CDN, SSL, and edge routing. fal.ai handles GPU scheduling and model execution. The Next.js API route is the only server-side code, and it is stateless.

---

## Features

### Prompt Input with Enhancement
Text area for the main prompt with an optional 'Enhance' button that sends the terse input to an LLM (GPT-4o-mini or Gemini Flash) for expansion into a detailed, style-rich generation prompt.

### Negative Prompt Support
Collapsible negative prompt field for specifying elements to exclude from the generated image — essential for controlling quality and style.

### Model and Parameter Controls
Select from available fal.ai models (FLUX.1-dev, FLUX.1-schnell, SDXL), configure image dimensions (512px to 1024px), inference steps (1–50), and guidance scale.

### Real-Time Generation Progress
WebSocket-based progress streaming shows intermediate latent images or a progress bar during multi-step diffusion inference.

### Prompt History Gallery
All generated images and their prompts are persisted in localStorage and displayed in a scrollable gallery with copy-prompt and regenerate buttons.

### Side-by-Side Comparison Mode
Generate the same prompt with two different models or parameter sets simultaneously, rendering results in a split-view for direct comparison.

### One-Click Vercel Deployment
A Deploy button in the README links to a Vercel deployment template pre-configured with required environment variables.

### Mobile-Responsive Layout
Tailwind CSS responsive design works cleanly on mobile browsers, tablet, and desktop without separate mobile builds.

---

## Tech Stack

| Library / Tool | Role | Why This Choice |
|---|---|---|
| **Next.js 14** | React framework | App Router, API Routes, Server Components |
| **React** | UI library | Client-side interactivity and component model |
| **Tailwind CSS** | Styling | Utility-first responsive design system |
| **fal.ai SDK (@fal-ai/client)** | AI backend | Serverless GPU inference for FLUX/SDXL models |
| **TypeScript** | Type safety | End-to-end typed Next.js application |
| **Vercel** | Hosting & deployment | Edge CDN, automatic HTTPS, CI/CD from GitHub |
| **OpenAI SDK (optional)** | Prompt enhancement | GPT-4o-mini for terse→detailed prompt expansion |

> **Key packages detected in this repo:** `@ai-sdk/fal` · `@radix-ui/react-collapsible` · `@radix-ui/react-label` · `@radix-ui/react-scroll-area` · `@radix-ui/react-select` · `@radix-ui/react-slot` · `@radix-ui/react-toast` · `@radix-ui/react-toggle` · `@radix-ui/react-tooltip` · `@vercel/analytics`

---

## Getting Started

### Prerequisites

- Python 3.9+ (or Node.js 18+ for TypeScript/JS projects)
- `pip` or `npm` package manager
- Relevant API keys (see Configuration section)

### Installation

```bash
git clone https://github.com/Devanik21/vercel-fal-image-generator.git
cd vercel-fal-image-generator
npm install

# Create .env.local
echo 'FAL_KEY=your_fal_api_key' > .env.local
echo 'OPENAI_API_KEY=your_openai_key' >> .env.local  # optional

# Run development server
npm run dev
# Open http://localhost:3000

# Deploy to Vercel
npx vercel --prod
```

---

## Usage

```bash
// pages/api/generate.ts — core generation route
import { fal } from '@fal-ai/client';

export default async function handler(req, res) {
  const { prompt, negativePrompt, steps, guidance } = req.body;
  const result = await fal.run('fal-ai/flux/dev', {
    input: { prompt, negative_prompt: negativePrompt,
             num_inference_steps: steps, guidance_scale: guidance }
  });
  res.json({ imageUrl: result.images[0].url });
}
```

---

## Configuration

| Variable | Default | Description |
|---|---|---|
| `FAL_KEY` | `(required)` | fal.ai API key from fal.ai/dashboard |
| `OPENAI_API_KEY` | `(optional)` | OpenAI API key for prompt enhancement feature |
| `DEFAULT_MODEL` | `fal-ai/flux/schnell` | Default fal.ai model endpoint |
| `DEFAULT_STEPS` | `4` | Default number of inference steps |

> Copy `.env.example` to `.env` and populate all required values before running.

---

## Project Structure

```
vercel-fal-image-generator/
├── README.md
├── .eslintrc.json
├── components.json
├── package.json
└── ...
```

---

## Roadmap

- [ ] Image-to-image transformation mode with uploaded reference image
- [ ] Style preset library with one-click prompt templates (photorealistic, anime, oil painting, etc.)
- [ ] User accounts with cloud-persisted generation history (Supabase or Clerk)
- [ ] ControlNet integration for pose-guided and edge-guided generation
- [ ] Batch generation mode: run the same prompt N times with different seeds for variation sampling

---

## Contributing

Contributions, issues, and feature requests are welcome. Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'feat: add your feature'`)
4. Push to your branch (`git push origin feature/your-feature`)
5. Open a Pull Request

Please follow conventional commit messages and ensure any new code is documented.

---

## Notes

A fal.ai API key is required for generation. Free-tier fal.ai keys have rate limits. Image generation costs scale with model choice and inference steps.

---

## Author

**Devanik Debnath**  
B.Tech, Electronics & Communication Engineering  
National Institute of Technology Agartala

[![GitHub](https://img.shields.io/badge/GitHub-Devanik21-black?style=flat-square&logo=github)](https://github.com/Devanik21)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-devanik-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/devanik/)

---

## License

This project is open source and available under the [MIT License](LICENSE).

---

*Crafted with curiosity, precision, and a belief that good software is worth building well.*
