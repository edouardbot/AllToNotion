---
name: youtube-dutch-summary
description: Fetch a YouTube video, generate a Dutch summary, and post it to Notion.
user-invocable: true
allowed-tools: bash web_search notion_API-post-page
triggers:
  - "youtube samenvatting"
  - "samenvatting video"
  - "youtube naar notion"
  - "verslag van youtube"
  - "youtu.be"
  - "youtube.com/watch"
---

# YouTube → Dutch Summary → Notion

Automatically fetch a YouTube video, generate a Dutch summary, and post it to a Notion page.

## Usage

When user provides a YouTube URL, follow this workflow:

### Step 1: Extract Video ID
Extract the video ID from the URL:
- `https://youtu.be/VIDEO_ID` → `VIDEO_ID`
- `https://youtube.com/watch?v=VIDEO_ID` → `VIDEO_ID`

### Step 2: Get Video Info
Use `yt-dlp` to fetch the video title:
```bash
yt-dlp --get-title --get-id "https://youtube.com/watch?v=VIDEO_ID"
```

### Step 3: Fetch Transcript (Preferred)
Try to get the transcript first:
```bash
yt-dlp --write-auto-subs --skip-download --sub-lang "en,nl" "https://youtube.com/watch?v=VIDEO_ID" -o "transcript"
```
Read the transcript file and use it as source for summarization.

If no transcript is available, search the web for a summary.

### Step 4: Generate Dutch Summary
Summarize the transcript or video content and translate to Dutch. Preserve structure:
- Maintain headings
- Keep bullet points
- Use clear, natural Dutch language

### Step 5: Post to Notion
Create a new Notion page with:
- **Title**: `Samenvatting: <Video Titel> (<video_id>)`
- **Content**: The Dutch summary
- **Parent**: User-specified Notion page ID or default workspace root

Use the Notion API tools available in this environment.

## Required Parameters
- `url`: YouTube video URL (required)
- `notion_page_id`: Parent Notion page ID (optional)

## Example Invocation

User: "Summarize https://youtu.be/dxq7WtWxi44 in Dutch and post to Notion"

Agent:
1. Extract: Video ID = `dxq7WtWxi44`
2. Fetch title and transcript
3. Generate Dutch summary
4. Create Notion page with title `Samenvatting: <Title> (dxq7WtWxi44)`
5. Return the created page URL

## Dependencies
- `yt-dlp` must be installed and in PATH
- Notion integration must be configured with proper API key
- Notion parent page must be shared with the integration

## Error Handling
- **Invalid URL**: Ask user to correct the YouTube URL
- **No transcript/summary found**: Use video title + description as fallback content
- **Notion write failure**: Retry once, then report the error to user
- **yt-dlp not found**: Instruct user to install it (`brew install yt-dlp` or `pip install yt-dlp`)

## Customization Options
- `summary_length`: short, medium, or long (default: medium)
- `include_transcript`: boolean to include full transcript (default: false)
- `target_language`: override Dutch with another language
