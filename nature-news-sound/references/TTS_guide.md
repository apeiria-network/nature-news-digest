# Nature News TTS Reference

This reference is for English TTS generation used by the `nature-news-sound` skill.

## TTS Guidelines

### Local Python Environment
- Use a working local Python runtime that already has the required audio dependencies when available.
- If the current runtime is missing required dependencies, install them into that same runtime before generating audio.
- Keep the audio-generation setup local to the current working runtime, and avoid changing unrelated Python environments.

### Text Preparation for TTS
1. Remove markdown formatting (*, **, #, etc.)
2. Remove credit lines ("Credit: ...")
3. Remove image references
4. Expand abbreviations on first mention in spoken form
5. Clean up special characters that TTS engines may mispronounce

### TTS Engine Selection

| Engine | Priority | Network | Quality | Notes |
|--------|----------|---------|---------|-------|
| **gTTS** | Primary (1st) | Needs Google access | Medium | Fast, simple; fails in China without VPN |
| **edge-tts** | Fallback (2nd) | No VPN needed | Good | Microsoft Edge neural voices; works in China |

**Auto-fallback logic:** Try gTTS first -> if it fails -> automatically switch to edge-tts.

### gTTS Usage (Primary)

```python
from gtts import gTTS
tts = gTTS(text=news_text, lang='en', slow=False)
tts.save('output.mp3')
```

- `lang='en'` for English
- `lang='zh-CN'` for Mandarin Chinese
- `slow=False` for natural reading speed
- Typical output: ~1.7 MB for a 3-4 minute news item

### edge-tts Usage (Fallback)

```python
import edge_tts
import asyncio

VOICE_MAP = {
    'en': 'en-US-AvaNeural',        # Female, US English
    'en-GB': 'en-GB-SoniaNeural',   # Female, British English
    'zh-CN': 'zh-CN-XiaoxiaoNeural', # Female, Mandarin Chinese
}

async def generate(text, output_path, voice='en-US-AvaNeural'):
    communicate = edge_tts.Communicate(text, voice)
    await communicate.save(output_path)

asyncio.run(generate(cleaned_text, 'output.mp3'))
```

- List available voices: `edge-tts --list-voices`
- Typical output: ~2-3 MB for a 3-4 minute news item
- Advantage: No Google dependency, works in mainland China

### Using the Unified Interface

```python
from scripts.nature_digest import generate_tts_audio

# Auto mode: gTTS -> edge-tts fallback
success, engine = generate_tts_audio(text, 'output.mp3', lang='en', engine='auto')

# Force specific engine
success, engine = generate_tts_audio(text, 'output.mp3', lang='en', engine='edge-tts')
success, engine = generate_tts_audio(text, 'output.mp3', lang='en', engine='gtts')
```

- `generate_tts_audio(...)` prepares the text and tries gTTS first, then edge-tts if needed
- It uses the current working Python runtime for audio generation
- Reuse the same working runtime throughout the audio step whenever possible

