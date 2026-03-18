# Next AI: PDF Q&A (React + Express + LangChain)

This project provides a simple "upload a PDF, then ask questions" experience by combining:

- **Frontend:** React (Create React App) + Ant Design
- **Backend:** Express + Multer for PDF uploads
- **AI:** LangChain with OpenAI embeddings + a chat model (retrieval-style QA)

When you ask a question, the server loads the uploaded PDF, splits it into text chunks, embeds the chunks, retrieves relevant context, and returns a concise answer (the prompt limits answers to ~3 sentences and instructs the model to say "I don't know" when appropriate).

## Demo flow

1. Open the web app at `http://localhost:3000`
2. Upload a PDF via the drag-and-drop area
3. Type a question and click **Ask**
4. The answer is generated using the uploaded PDF as the knowledge source

## Prerequisites

- Node.js (LTS recommended)
- An OpenAI API key

## Setup

1. Install dependencies:
   - `npm install`
   - `cd server && npm install`
2. Configure environment variables:
   - Create `server/.env` (it is gitignored) with:
     - `REACT_APP_OPENAI_API_KEY=<your_key_here>`
3. Ensure the upload folder exists (used by the backend's Multer storage):
   - `server/uploads/`

## Run locally

From the repo root:

- `npm run dev`

This starts:

- Frontend (CRA) on `http://localhost:3000`
- Backend (Express) on `http://localhost:5001`

## API endpoints (backend)

The Express server in `server/` exposes:

- `POST /upload`
  - Body: `multipart/form-data` with a single file field named `file`
  - Stores the uploaded PDF path for subsequent `/chat` calls

- `GET /chat?question=<text>`
  - Returns the generated answer as plain text (`response.text`)

## Key implementation notes

- The backend keeps the "current PDF" in memory (`filePath`) after the most recent upload; uploading a new PDF replaces it.
- For each `/chat` request, the server rebuilds the in-memory vector store from the PDF (embeddings + retrieval). This is simple but can be slow for large PDFs or frequent questions.
- The frontend currently calls the backend at a hard-coded `http://localhost:5001` (`DOMAIN` in `src/components/PdfUploader.js` and `src/components/ChatComponent.js`); if you change ports/hosts, update those constants.
