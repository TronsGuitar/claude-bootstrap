# AI Models Reference Skill

*Load with: base.md + llm-patterns.md*

## Philosophy

**Use the right model for the job.** Bigger isn't always better - match model capabilities to task requirements. Consider cost, latency, and accuracy tradeoffs.

## Model Selection Matrix

| Task | Recommended | Why |
|------|-------------|-----|
| Complex reasoning | Claude Opus, GPT-4o, Gemini Ultra | Highest accuracy |
| Fast chat/completion | Claude Haiku, GPT-4o-mini, Gemini Flash | Low latency, cheap |
| Code generation | Claude Sonnet, GPT-4o, Codestral | Good balance |
| Vision/images | Claude Sonnet, GPT-4o, Gemini Pro | Multimodal |
| Embeddings | text-embedding-3-small, Voyage | Cost-effective |
| Voice synthesis | Eleven Labs, OpenAI TTS | Natural sounding |
| Image generation | DALL-E 3, Stable Diffusion, Flux | Different styles |

---

## Anthropic (Claude)

### Documentation
- **API Docs**: https://docs.anthropic.com
- **Model Overview**: https://docs.anthropic.com/en/docs/about-claude/models
- **Pricing**: https://www.anthropic.com/pricing

### Latest Models (Dec 2024)

```typescript
// Claude model IDs
const CLAUDE_MODELS = {
  // Flagship - highest capability
  opus: 'claude-sonnet-4-20250514',

  // Balanced - best for most tasks
  sonnet: 'claude-sonnet-4-20250514',

  // Fast & cheap - high volume tasks
  haiku: 'claude-haiku-3-5-20241022',
} as const;
```

### Usage
```typescript
import Anthropic from '@anthropic-ai/sdk';

const anthropic = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});

const response = await anthropic.messages.create({
  model: 'claude-sonnet-4-20250514',
  max_tokens: 1024,
  messages: [
    { role: 'user', content: 'Hello, Claude!' }
  ],
});
```

### Model Selection
```
claude-sonnet-4-20250514 (Opus 4)
├── Best for: Complex analysis, research, nuanced writing
├── Context: 200K tokens
├── Cost: $15/$75 per 1M tokens (input/output)
└── Use when: Accuracy matters most

claude-sonnet-4-20250514 (Sonnet 4)
├── Best for: Code, general tasks, balanced performance
├── Context: 200K tokens
├── Cost: $3/$15 per 1M tokens
└── Use when: Default choice for most applications

claude-haiku-3-5-20241022 (Haiku 3.5)
├── Best for: Classification, extraction, high-volume
├── Context: 200K tokens
├── Cost: $0.25/$1.25 per 1M tokens
└── Use when: Speed and cost matter most
```

---

## OpenAI

### Documentation
- **API Docs**: https://platform.openai.com/docs
- **Models**: https://platform.openai.com/docs/models
- **Pricing**: https://openai.com/pricing

### Latest Models (Dec 2024)

```typescript
const OPENAI_MODELS = {
  // Flagship multimodal
  gpt4o: 'gpt-4o',
  gpt4oLatest: 'gpt-4o-2024-11-20',

  // Fast & cheap
  gpt4oMini: 'gpt-4o-mini',

  // Reasoning (chain of thought)
  o1: 'o1',
  o1Mini: 'o1-mini',
  o1Preview: 'o1-preview',

  // Embeddings
  embeddingSmall: 'text-embedding-3-small',
  embeddingLarge: 'text-embedding-3-large',

  // Image generation
  dalle3: 'dall-e-3',

  // Text to speech
  tts: 'tts-1',
  ttsHd: 'tts-1-hd',

  // Speech to text
  whisper: 'whisper-1',
} as const;
```

### Usage
```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});

// Chat completion
const response = await openai.chat.completions.create({
  model: 'gpt-4o',
  messages: [
    { role: 'user', content: 'Hello!' }
  ],
});

// With vision
const visionResponse = await openai.chat.completions.create({
  model: 'gpt-4o',
  messages: [
    {
      role: 'user',
      content: [
        { type: 'text', text: 'What is in this image?' },
        { type: 'image_url', image_url: { url: 'https://...' } },
      ],
    },
  ],
});

// Embeddings
const embedding = await openai.embeddings.create({
  model: 'text-embedding-3-small',
  input: 'Your text here',
});
```

### Model Selection
```
gpt-4o
├── Best for: General tasks, vision, multimodal
├── Context: 128K tokens
├── Cost: $2.50/$10 per 1M tokens
└── Use when: Need vision or balanced performance

gpt-4o-mini
├── Best for: High-volume, simple tasks
├── Context: 128K tokens
├── Cost: $0.15/$0.60 per 1M tokens
└── Use when: Cost-sensitive applications

o1 / o1-mini
├── Best for: Math, coding, complex reasoning
├── Context: 200K tokens
├── Cost: $15/$60 (o1), $3/$12 (o1-mini)
└── Use when: Multi-step reasoning required
```

---

## Google (Gemini)

### Documentation
- **API Docs**: https://ai.google.dev/docs
- **Models**: https://ai.google.dev/gemini-api/docs/models/gemini
- **Pricing**: https://ai.google.dev/pricing

### Latest Models (Dec 2024)

```typescript
const GEMINI_MODELS = {
  // Flagship
  pro: 'gemini-1.5-pro',
  proLatest: 'gemini-1.5-pro-latest',

  // Fast
  flash: 'gemini-1.5-flash',
  flashLatest: 'gemini-1.5-flash-latest',

  // Compact
  flash8b: 'gemini-1.5-flash-8b',

  // Experimental
  exp: 'gemini-exp-1206',
  thinking: 'gemini-2.0-flash-thinking-exp',
} as const;
```

### Usage
```typescript
import { GoogleGenerativeAI } from '@google/generative-ai';

const genAI = new GoogleGenerativeAI(process.env.GOOGLE_API_KEY);
const model = genAI.getGenerativeModel({ model: 'gemini-1.5-pro' });

const result = await model.generateContent('Hello!');
const response = result.response.text();

// With vision
const visionModel = genAI.getGenerativeModel({ model: 'gemini-1.5-pro' });
const imagePart = {
  inlineData: {
    data: base64Image,
    mimeType: 'image/jpeg',
  },
};
const result = await visionModel.generateContent(['Describe this:', imagePart]);
```

### Model Selection
```
gemini-1.5-pro
├── Best for: Complex tasks, long context
├── Context: 2M tokens (!)
├── Cost: $1.25/$5 per 1M tokens
└── Use when: Need massive context window

gemini-1.5-flash
├── Best for: Fast responses, general use
├── Context: 1M tokens
├── Cost: $0.075/$0.30 per 1M tokens
└── Use when: Speed and cost matter

gemini-1.5-flash-8b
├── Best for: High volume, simple tasks
├── Context: 1M tokens
├── Cost: $0.0375/$0.15 per 1M tokens
└── Use when: Lowest cost needed
```

---

## Eleven Labs (Voice)

### Documentation
- **API Docs**: https://elevenlabs.io/docs/api-reference
- **Models**: https://elevenlabs.io/docs/api-reference/models
- **Pricing**: https://elevenlabs.io/pricing

### Latest Models (Dec 2024)

```typescript
const ELEVENLABS_MODELS = {
  // Highest quality
  multilingualV2: 'eleven_multilingual_v2',

  // English optimized
  turboV2: 'eleven_turbo_v2',
  turboV2_5: 'eleven_turbo_v2_5',

  // Low latency
  flash: 'eleven_flash_v2',
  flashV2_5: 'eleven_flash_v2_5',
} as const;
```

### Usage
```typescript
import { ElevenLabsClient } from 'elevenlabs';

const elevenlabs = new ElevenLabsClient({
  apiKey: process.env.ELEVENLABS_API_KEY,
});

// Text to speech
const audio = await elevenlabs.textToSpeech.convert('voice-id', {
  text: 'Hello, world!',
  model_id: 'eleven_multilingual_v2',
  voice_settings: {
    stability: 0.5,
    similarity_boost: 0.75,
  },
});

// Stream audio
const audioStream = await elevenlabs.textToSpeech.convertAsStream('voice-id', {
  text: 'Streaming audio...',
  model_id: 'eleven_turbo_v2_5',
});
```

### Model Selection
```
eleven_multilingual_v2
├── Best for: Non-English, highest quality
├── Latency: ~1s
├── Languages: 29
└── Use when: Quality over speed

eleven_turbo_v2_5
├── Best for: English, low latency
├── Latency: ~300ms
├── Languages: 32
└── Use when: Real-time English apps

eleven_flash_v2_5
├── Best for: Lowest latency
├── Latency: ~75ms
├── Languages: 32
└── Use when: Conversational AI
```

---

## Replicate

### Documentation
- **API Docs**: https://replicate.com/docs
- **Models**: https://replicate.com/explore
- **Pricing**: https://replicate.com/pricing

### Popular Models (Dec 2024)

```typescript
const REPLICATE_MODELS = {
  // Image generation
  flux: 'black-forest-labs/flux-1.1-pro',
  fluxSchnell: 'black-forest-labs/flux-schnell',
  sdxl: 'stability-ai/sdxl',

  // Image editing
  instructPix2Pix: 'timothybrooks/instruct-pix2pix',

  // Video
  stableVideoDiffusion: 'stability-ai/stable-video-diffusion',

  // Audio
  musicgen: 'meta/musicgen',

  // LLMs
  llama: 'meta/meta-llama-3.1-405b-instruct',
  mistral: 'mistralai/mistral-7b-instruct-v0.2',
  codellama: 'meta/codellama-70b-instruct',
} as const;
```

### Usage
```typescript
import Replicate from 'replicate';

const replicate = new Replicate({
  auth: process.env.REPLICATE_API_TOKEN,
});

// Image generation with Flux
const output = await replicate.run('black-forest-labs/flux-1.1-pro', {
  input: {
    prompt: 'A serene mountain landscape at sunset',
    aspect_ratio: '16:9',
    output_format: 'webp',
  },
});

// Run any model
const prediction = await replicate.predictions.create({
  version: 'model-version-id',
  input: { /* model-specific inputs */ },
});

// Stream results
for await (const event of replicate.stream('model-id', { input })) {
  process.stdout.write(event.data);
}
```

### Model Selection
```
flux-1.1-pro
├── Best for: Highest quality images
├── Speed: ~10s
├── Cost: $0.04/image
└── Use when: Quality matters most

flux-schnell
├── Best for: Fast image generation
├── Speed: ~1s
├── Cost: $0.003/image
└── Use when: Speed over quality

stable-video-diffusion
├── Best for: Image to video
├── Speed: ~2min
├── Cost: ~$0.07/video
└── Use when: Need motion from stills
```

---

## Stability AI

### Documentation
- **API Docs**: https://platform.stability.ai/docs/api-reference
- **Models**: https://platform.stability.ai/docs/models
- **Pricing**: https://platform.stability.ai/pricing

### Latest Models (Dec 2024)

```typescript
const STABILITY_MODELS = {
  // Image generation
  sd3: 'sd3-large',
  sd3Turbo: 'sd3-large-turbo',
  sdxl: 'stable-diffusion-xl-1024-v1-0',

  // Image editing
  inpaint: 'stable-diffusion-xl-1024-v1-0', // with mask
  outpaint: 'stable-diffusion-xl-1024-v1-0',

  // Upscaling
  upscale: 'esrgan-v1-x2plus',
} as const;
```

### Usage
```typescript
const response = await fetch(
  'https://api.stability.ai/v1/generation/sd3-large/text-to-image',
  {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${process.env.STABILITY_API_KEY}`,
    },
    body: JSON.stringify({
      prompt: 'A futuristic city at night',
      output_format: 'webp',
      aspect_ratio: '16:9',
    }),
  }
);

const data = await response.json();
const imageBase64 = data.image;
```

---

## Mistral AI

### Documentation
- **API Docs**: https://docs.mistral.ai
- **Models**: https://docs.mistral.ai/getting-started/models
- **Pricing**: https://mistral.ai/technology/#pricing

### Latest Models (Dec 2024)

```typescript
const MISTRAL_MODELS = {
  // Flagship
  large: 'mistral-large-latest',

  // Balanced
  medium: 'mistral-medium-latest',

  // Fast
  small: 'mistral-small-latest',

  // Code specialized
  codestral: 'codestral-latest',

  // Open source
  nemo: 'open-mistral-nemo',
  mixtral: 'open-mixtral-8x22b',
} as const;
```

### Usage
```typescript
import MistralClient from '@mistralai/mistralai';

const client = new MistralClient(process.env.MISTRAL_API_KEY);

const response = await client.chat({
  model: 'mistral-large-latest',
  messages: [{ role: 'user', content: 'Hello!' }],
});

// Code completion with Codestral
const codeResponse = await client.chat({
  model: 'codestral-latest',
  messages: [{ role: 'user', content: 'Write a Python function to...' }],
});
```

---

## Voyage AI (Embeddings)

### Documentation
- **API Docs**: https://docs.voyageai.com
- **Models**: https://docs.voyageai.com/docs/embeddings
- **Pricing**: https://www.voyageai.com/pricing

### Latest Models (Dec 2024)

```typescript
const VOYAGE_MODELS = {
  // General purpose
  large2: 'voyage-large-2',

  // Code specialized
  code2: 'voyage-code-2',

  // Multilingual
  multilingual2: 'voyage-multilingual-2',

  // Legal/Finance
  law2: 'voyage-law-2',
  finance2: 'voyage-finance-2',
} as const;
```

### Usage
```typescript
const response = await fetch('https://api.voyageai.com/v1/embeddings', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${process.env.VOYAGE_API_KEY}`,
  },
  body: JSON.stringify({
    model: 'voyage-large-2',
    input: ['Your text to embed'],
  }),
});

const { data } = await response.json();
const embedding = data[0].embedding; // 1536 dimensions
```

---

## Quick Reference

### Cost Comparison (per 1M tokens)

| Provider | Cheap | Mid | Premium |
|----------|-------|-----|---------|
| Anthropic | $0.25 (Haiku) | $3 (Sonnet) | $15 (Opus) |
| OpenAI | $0.15 (4o-mini) | $2.50 (4o) | $15 (o1) |
| Google | $0.04 (Flash-8b) | $0.08 (Flash) | $1.25 (Pro) |
| Mistral | $0.25 (Small) | $2.70 (Medium) | $8 (Large) |

### Best For Each Task

```
Reasoning/Analysis    → Claude Opus, o1, Gemini Pro
Code Generation       → Claude Sonnet, Codestral, GPT-4o
Fast Responses        → Claude Haiku, GPT-4o-mini, Gemini Flash
Long Context          → Gemini Pro (2M), Claude (200K)
Vision                → GPT-4o, Claude Sonnet, Gemini Pro
Embeddings            → Voyage, text-embedding-3-small
Voice Synthesis       → Eleven Labs, OpenAI TTS
Image Generation      → Flux Pro, DALL-E 3, SD3
Video Generation      → Stable Video Diffusion, Runway
```

### Environment Variables Template
```bash
# .env.example (NEVER commit actual keys)

# LLMs
ANTHROPIC_API_KEY=sk-ant-...
OPENAI_API_KEY=sk-...
GOOGLE_API_KEY=AI...
MISTRAL_API_KEY=...

# Media
ELEVENLABS_API_KEY=...
REPLICATE_API_TOKEN=r8_...
STABILITY_API_KEY=sk-...

# Embeddings
VOYAGE_API_KEY=pa-...
```

### Model Update Checklist
```
When models update:
□ Check official changelog/blog
□ Update model ID strings
□ Test with existing prompts
□ Compare output quality
□ Check pricing changes
□ Update context limits if changed
```
