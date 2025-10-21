# AI Interview Prep

Practice interviews with an AI that knows your resume and the job you're applying for.

![Landing Page](D:\upivot_assignment\interview-prep-app\images\WhatsApp Image 2025-10-22 at 01.49.38.jpeg)

## What it does

Upload your resume + job description → AI asks you interview questions → Get instant feedback with scores.

## Screenshots

<table>
  <tr>
    <td><img src="interview-prep-app\images\WhatsApp Image 2025-10-22 at 01.49.38.jpeg" alt="Landing" width="400"/></td>
    <td><img src="interview-prep-app\images\WhatsApp Image 2025-10-22 at 01.50.09.jpeg" alt="Upload" width="400"/></td>
  </tr>
</table>

## How it works

```mermaid
flowchart LR
    A[Upload Resume & JD] --> B[AI Extracts Text]
    B --> C[Generate Embeddings]
    C --> D[AI Asks Questions]
    D --> E[You Answer]
    E --> F[AI Finds Relevant Resume Parts]
    F --> G[Get Score & Feedback]
```

## Quick Start

```bash
git clone https://github.com/Swayanshu18/interview_ai_resumeparser.git
cd interview-prep-app

# Backend
cd backend
npm install
cp .env.example .env
# Add your MongoDB URI and OpenAI key
npm run dev

# Frontend (new terminal)
cd frontend
npm install
npm run dev
```

Visit `http://localhost:3000`

## Tech Stack

**Frontend:** React + Tailwind + Vite
**Backend:** Node.js + Express + MongoDB
**AI:** OpenAI (embeddings + chat)
**Storage:** AWS S3

## Folder Structure

```
interview-prep-app/
├── backend/
│   ├── models/           # User, Document, Chat schemas
│   ├── routes/           # API endpoints
│   ├── utils/            # AI & S3 helpers
│   └── server.js
├── frontend/
│   ├── src/
│   │   ├── pages/        # Landing, Upload, Chat
│   │   ├── components/   # Reusable UI
│   │   └── services/     # API calls
│   └── vite.config.js
└── README.md
```

## Features

- 🔐 JWT auth with bcrypt
- 📄 PDF upload & parsing
- 🤖 AI question generation from job description
- 📊 Scored feedback (1-10) with citations
- 🎯 RAG - AI references your actual resume in feedback
- ⚡ Sequential questions (one at a time)
- 💬 End-of-interview comprehensive evaluation

## API Flow

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant Backend
    participant MongoDB
    participant OpenAI
    participant S3

    User->>Frontend: Upload Resume
    Frontend->>Backend: POST /api/documents/upload
    Backend->>S3: Store PDF
    Backend->>OpenAI: Generate embeddings
    Backend->>MongoDB: Save doc with embeddings
    Backend->>Frontend: Success

    User->>Frontend: Start Interview
    Frontend->>Backend: POST /api/chat/start
    Backend->>OpenAI: Generate first question
    Backend->>MongoDB: Create chat session
    Backend->>Frontend: Return question

    User->>Frontend: Answer
    Frontend->>Backend: POST /api/chat/query
    Backend->>OpenAI: Embed answer
    Backend->>MongoDB: Find similar resume chunks
    Backend->>OpenAI: Evaluate answer with context
    Backend->>MongoDB: Save message
    Backend->>Frontend: Score + Feedback + Next Q
```

## Environment Variables

**Backend:**
```env
MONGODB_URI=mongodb+srv://...
JWT_SECRET=your-secret
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_S3_BUCKET_NAME=interview-docs
OPENAI_API_KEY=sk-...
```

**Frontend:**
```env
VITE_API_URL=http://localhost:5000/api
```

## Deploy on Vercel

**Backend:**
```bash
cd backend
vercel --prod
# Add env vars in Vercel dashboard
```

**Frontend:**
```bash
cd frontend
vercel --prod
```

Check [FINAL_DEPLOYMENT_GUIDE.md](./FINAL_DEPLOYMENT_GUIDE.md) for detailed steps.

## System Architecture

```mermaid
graph TB
    subgraph Client
        A[React App]
    end

    subgraph Backend
        B[Express API]
        C[Auth Middleware]
        D[Document Routes]
        E[Chat Routes]
    end

    subgraph Storage
        F[(MongoDB)]
        G[AWS S3]
    end

    subgraph AI
        H[OpenAI Embeddings]
        I[OpenAI Chat]
    end

    A -->|REST API| B
    B --> C
    C --> D
    C --> E
    D --> F
    D --> G
    D --> H
    E --> F
    E --> H
    E --> I
```

## License

MIT

---

Built for interview prep. Made by [Swayanshu Rout](https://github.com/Swayanshu18)
