# Markdown Meeting Notes → Google Doc Converter

A Python script (Google Colab notebook) that converts markdown-formatted meeting notes into a professionally formatted Google Doc using the Google Docs API.

## Features

| Requirement | Implementation |
|---|---|
| **Heading 1** | Main title ("Product Team Sync") styled as `HEADING_1` |
| **Heading 2** | Section headers ("Attendees", "Agenda", etc.) styled as `HEADING_2` |
| **Heading 3** | Sub-section headers (under Agenda) styled as `HEADING_3` |
| **Nested bullets** | Indentation preserved via `indentStart` / `indentFirstLine` |
| **Checkboxes** | `- [ ]` items converted to native Google Docs checklist bullets |
| **@mentions** | Styled **bold** with a distinct blue color (#0060BF) |
| **Footer** | "Meeting recorded by" / "Duration" lines rendered in *italic gray* |
| **Horizontal rule** | `---` rendered as a thin gray separator line |

## Architecture

```
Markdown text
    │
    ▼
┌──────────────────┐
│  parse_markdown() │  →  list of Token objects (HeadingToken, BulletToken, …)
└──────────────────┘
    │
    ▼
┌──────────────────────┐
│  GoogleDocBuilder     │  →  list of Google Docs API batchUpdate requests
│  .add_tokens()        │
└──────────────────────┘
    │
    ▼
┌──────────────────────┐
│  Google Docs API      │  →  Formatted Google Doc
│  batchUpdate()        │
└──────────────────────┘
```

## Required Dependencies

All dependencies are **pre-installed** in Google Colab. No extra `pip install` is needed beyond what the notebook already handles.

| Package | Purpose |
|---|---|
| `google-auth` | Google authentication |
| `google-auth-oauthlib` | OAuth2 flow |
| `google-api-python-client` | Google Docs API client |

## How to Run in Google Colab

### Option 1 — Open directly from GitHub

1. Go to [Google Colab](https://colab.research.google.com/).
2. Click **File → Open notebook → GitHub**.
3. Paste this repository URL and select `meeting_notes_to_gdoc.ipynb`.

### Option 2 — Upload manually

1. Download `meeting_notes_to_gdoc.ipynb` from this repo.
2. Go to [Google Colab](https://colab.research.google.com/).
3. Click **File → Upload notebook** and select the downloaded file.

### Running the notebook

1. **Cell 1 (Auth):** Run the first code cell. A Google sign-in popup will appear — authorize access to your Google Drive / Docs.
2. **Cell 2 (Markdown):** Review the meeting notes (edit if desired).
3. **Cell 3 (Parser):** Run to parse the markdown — verify the token list in the output.
4. **Cell 4 (Builder):** Run to define the `GoogleDocBuilder` class.
5. **Cell 5 (Create Doc):** Run to create and format the Google Doc. The output will contain a direct link.
6. **Cell 6 (Open):** Click the rendered link to open your new document.

> **Tip:** You can also run all cells at once with **Runtime → Run all**.

## Setup Instructions (Local Development)

If you want to run this outside of Colab (e.g. for testing the parser locally):

```bash
pip install google-auth google-auth-oauthlib google-api-python-client
```

Note: The `google.colab.auth` module is only available inside Colab. For local use you would need to set up OAuth2 credentials manually via the Google Cloud Console.

## Project Structure

```
.
├── README.md                       # This file
└── meeting_notes_to_gdoc.ipynb     # The Colab notebook (main deliverable)
```

## Error Handling

The notebook includes error handling at every stage:

- **Authentication failure** — caught with a clear message directing the user to re-run the auth cell.
- **Document creation failure** — `HttpError` caught with status code and response body logged.
- **Batch update failure** — partial progress is preserved; the (incomplete) doc URL is printed so the user can inspect what was applied.
- **Empty input** — a `ValueError` is raised if the parsed markdown yields zero tokens.

## License

MIT
