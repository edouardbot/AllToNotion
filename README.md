# AllToNotion

A Vibe CLI project with custom skills for posting content to Notion.

## Skills

### youtube-dutch-summary
Fetch YouTube videos, generate Dutch summaries, and post to Notion.

**Usage:**
```
cd projects/AllToNotion
vibe --trust  # Trust the directory for this session
```

Then ask: "Summarize https://youtu.be/VIDEO_ID in Dutch and post to Notion"

## Setup

### Prerequisites
- [Vibe CLI](https://github.com/mistralai/vibe) installed
- `yt-dlp` installed: `brew install yt-dlp` or `pip install yt-dlp`
- Notion integration API key configured in `~/.config/notion/api_key`

### Notion Integration
1. Create an integration at https://notion.so/my-integrations
2. Copy the API key (starts with `secret_`)
3. Store it:
   ```bash
   mkdir -p ~/.config/notion
   echo "secret_your_key_here" > ~/.config/notion/api_key
   ```
4. Share your target Notion pages with the integration

## Project Structure
```
AllToNotion/
  .git/
  .vibe/
    skills/
      youtube-dutch-summary/
        SKILL.md      # Skill definition
        scripts/      # Supporting scripts (optional)
  README.md
```

## Adding New Skills
1. Create a directory in `.vibe/skills/<skill-name>/`
2. Add a `SKILL.md` file with YAML frontmatter
3. Load with `/reload` in Vibe CLI
