# Nature News Digest - Conversation Output Template

## Article Structure Template

```
Article {N}: {English Title}

Title: {English title}
标题： {Chinese title}

Author: {Author name}
作者： {Author name}

Publication Date: {Date in English}
发布日期： {Date in Chinese}

Source: {URL}
来源： {URL}

Summary / 摘要
{English summary sentence}
{Chinese translation of summary sentence}

Full Text / 正文
{English paragraph line 1}
{Chinese translation line 1}

{English paragraph line 2}
{Chinese translation line 2}

...

{English Sub-heading} / {Chinese Sub-heading}
{English paragraph}
{Chinese translation}
```

## Delivery Mode

- Deliver the bilingual result directly in the conversation
- Do not write a bilingual markdown file
- TTS audio (English): `Nature_Article{N}_English.mp3`

## Output Checklist

- [ ] Search Nature latest news (top 3)
- [ ] Fetch full article content for each
- [ ] Translate each line (EN -> CN)
- [ ] Present the bilingual result directly in the conversation
- [ ] Generate TTS audio for each article (English)
- [ ] Verify generated audio files exist and are non-empty
