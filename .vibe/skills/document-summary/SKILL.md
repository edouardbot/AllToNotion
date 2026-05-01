---
name: document-summary
description: Upload a PDF or Word document, generate a summary, and post it to Notion.
user-invocable: true
allowed-tools: bash web_search notion_API-post-page
triggers:
  - "samenvatting document"
  - "pdf naar notion"
  - "word naar notion"
  - "docx naar notion"
  - "bestand samenvatten"
  - "upload document"
  - ".pdf"
  - ".docx"
  - ".doc"
---

# Document → Summary → Notion

Automatically process uploaded PDF or Word documents, extract text, generate a summary, and post it to a Notion page.

## Usage

When user uploads a document file (PDF, DOCX, DOC), follow this workflow:

### Step 1: Accept Document Upload
User can provide:
- A file path to a local document
- Upload a file directly in the chat
- A URL to a document (if publicly accessible)

Supported formats:
- `.pdf` (PDF documents)
- `.docx` (Word documents)
- `.doc` (Legacy Word documents)

### Step 2: Extract Text from Document

#### For PDF files:
Use `pdftotext` (from poppler-utils) or `pdfminer.six`:
```bash
# Using pdftotext (preferred)
pdftotext -layout input.pdf output.txt 2>/dev/null && cat output.txt

# Using python with pdfminer.six
python3 -c "
import sys
from pdfminer.high_level import extract_text
text = extract_text(sys.argv[1])
print(text)
" input.pdf
```

#### For DOCX files:
Use `python-docx` or `pandoc`:
```bash
# Using python-docx
python3 -c "
from docx import Document
import sys
doc = Document(sys.argv[1])
text = '\n'.join([para.text for para in doc.paragraphs])
print(text)
" input.docx

# Using pandoc
pandoc -f docx -t plain input.docx
```

#### For DOC files:
Use `antiword` or `catdoc`:
```bash
# Using antiword
antiword input.doc

# Using catdoc
catdoc input.doc
```

### Step 3: Generate Summary
Summarize the extracted text:
- Preserve structure (headings, lists)
- Identify key points, main arguments, conclusions
- Remove boilerplate, repetitions, and irrelevant details
- Generate a concise summary (default: ~25% of original length)

### Step 4: Post to Notion
Create a new Notion page with:
- **Title**: `Samenvatting: <Document Naam>`
- **Content**: 
  - Document name and type
  - Summary text
  - Optional: Key points as bullet list
  - Optional: Full text (if `include_full_text: true`)
- **Parent**: User-specified Notion page ID or default workspace root

## Required Parameters
- `file_path` or `file_url`: Path to document file (required)
- `notion_page_id`: Parent Notion page ID (optional)

## Example Invocation

User: "Maak een samenvatting van mijn_rapport.pdf en post naar Notion"

Agent:
1. Accept the uploaded file or path
2. Extract text from PDF
3. Generate summary
4. Create Notion page with title `Samenvatting: mijn_rapport.pdf`
5. Return the created page URL

## Dependencies

### Required Tools (at least one per format):
- **PDF**: `pdftotext` (poppler-utils) OR `pdfminer.six` (pip install pdfminer.six)
- **DOCX**: `python-docx` (pip install python-docx) OR `pandoc`
- **DOC**: `antiword` OR `catdoc`

### Installation:
```bash
# For PDF support (recommended)
sudo apt-get install poppler-utils  # Debian/Ubuntu
brew install poppler                # macOS

# For DOCX support
pip install python-docx

# For DOC support
sudo apt-get install antiword      # Debian/Ubuntu
brew install antiword              # macOS
```

## Error Handling
- **Unsupported format**: Report error and list supported formats
- **File not found**: Ask user to verify file path
- **Text extraction failed**: Try alternative tools, then report error
- **Empty document**: Report that no text was found
- **Notion write failure**: Retry once, then report the error

## Customization Options
- `summary_length`: short (10%), medium (25%), long (50%) of original (default: medium)
- `include_full_text`: boolean to include full extracted text (default: false)
- `target_language`: override with another language (default: Dutch)
- `extract_tables`: boolean to extract and format tables from document (default: false)
