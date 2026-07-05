# Workspace

## Overview

pnpm workspace monorepo using TypeScript. This is a comprehensive Bible Study Companion app called "Lumen - Your Spiritual Companion".

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)
- **AI**: OpenAI via Replit AI Integrations (gpt-5.2)
- **Bible API**: bible-api.com (free public API, KJV and other translations)
- **Frontend**: React + Vite, Tailwind, shadcn/ui, framer-motion, react-markdown

## App Features (18 Pages)

1. **Home Dashboard** - Personalized greeting for Carson Kirkpatrick, verse of the day, stats panel, 16 quick action cards
2. **Bible Reader** - Read passages by reference, search, KJV/NIV/ESV; hover any verse for Explain/Cross-Refs/Save actions
3. **AI Guide (Chat)** - AI Bible scholar chat powered by GPT-5.2 with conversation history
4. **Prayer Journal** - Add/edit/delete prayers, mark answered, AI prayer generation
5. **Study Notes** - Create and manage Bible study notes linked to references/tags
6. **Devotional Generator** - AI-generated devotionals by topic or verse (streaming)
7. **Bible Quiz** - AI-generated multiple-choice quizzes with scoring, explanations; Easy/Medium/Hard
8. **Word Study** - Deep biblical word studies with Hebrew/Greek origins and cross-references (streaming)
9. **Topic Explorer** - Comprehensive topical Bible studies with OT/NT coverage (streaming)
10. **Reading Plans** - 4 curated plans (365-day Bible, 90-day NT, 30-day Psalms/Proverbs, 21-day Jesus); progress tracking
11. **Sermon Notes** - Record sermons with preacher/date/scripture; AI outline generation
12. **Faith Journal** - Personal spiritual journal with mood tags and daily AI reflection prompts
13. **Favorites** - Bookmarked verses saved from Bible Reader; search and share
14. **Hymnal** - 12 classic hymns (Amazing Grace, How Great Thou Art, etc.) with full lyrics, themes, authors

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server
│   │   └── src/routes/     # bible, prayers, notes, favorites, sermons, journal, reading-plans, hymns, stats
│   └── bible-companion/    # React + Vite frontend (Lumen app)
│       └── src/pages/      # 14 pages
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   ├── db/                 # Drizzle ORM schema + DB connection
│   ├── integrations-openai-ai-server/  # OpenAI server-side client
│   └── integrations-openai-ai-react/   # OpenAI React hooks
├── scripts/                # Utility scripts
├── pnpm-workspace.yaml     # pnpm workspace
├── tsconfig.base.json      # Shared TS options
├── tsconfig.json           # Root TS project references
└── package.json            # Root package
```

## Database Tables

- `conversations` - AI chat conversation sessions
- `messages` - AI chat messages per conversation
- `prayers` - Prayer journal entries (title, content, category, answered status)
- `study_notes` - Bible study notes (title, content, reference, tags)
- `favorites` - Bookmarked Bible verses (reference, text, translation, note)
- `sermons` - Sermon notes (title, preacher, date, scripture, key points, content, application)
- `journal_entries` - Faith journal entries (title, content, mood, scripture, tags)
- `reading_progress` - Reading plan progress (plan_id, day, reference, completedAt)

## API Routes

All routes under `/api`:
- `GET /api/healthz` - Health check
- `GET /api/stats` - User statistics
- `GET /api/bible/verse-of-day` - Daily verse
- `GET /api/bible/search?q=&translation=` - Search Bible
- `GET /api/bible/passage?reference=&translation=` - Get passage
- `POST /api/bible/explain` - AI verse explanation (SSE)
- `POST /api/bible/devotional` - AI devotional generation (SSE)
- `POST /api/bible/quiz` - AI Bible quiz generation (SSE, JSON array)
- `POST /api/bible/word-study` - AI word study (SSE)
- `POST /api/bible/topic-study` - AI topic study (SSE)
- `POST /api/bible/cross-references` - AI cross-references (SSE)
- `GET/POST /api/openai/conversations` - AI chat conversations
- `GET/DELETE /api/openai/conversations/:id` - Conversation management
- `GET/POST /api/openai/conversations/:id/messages` - Chat messages (SSE for POST)
- `GET/POST /api/prayers` - Prayer journal
- `PUT/DELETE /api/prayers/:id` - Prayer management
- `POST /api/prayers/generate` - AI prayer generation (SSE)
- `GET/POST /api/notes` - Study notes
- `PUT/DELETE /api/notes/:id` - Note management
- `GET/POST /api/favorites` - Bookmarked verses
- `DELETE /api/favorites/:id` - Remove bookmark
- `GET/POST /api/sermons` - Sermon notes
- `PUT/DELETE /api/sermons/:id` - Sermon management
- `POST /api/sermons/:id/outline` - AI sermon outline (SSE)
- `GET/POST /api/journal` - Faith journal entries
- `PUT/DELETE /api/journal/:id` - Journal management
- `POST /api/journal/prompt` - AI journal prompt (SSE)
- `GET /api/reading-plans` - Reading plans with user progress
- `GET /api/reading-plans/progress` - User reading progress
- `POST /api/reading-plans/progress` - Mark reading complete
- `GET /api/hymns?q=` - List/search hymns
- `GET /api/hymns/:id` - Get hymn with full lyrics

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. The root `tsconfig.json` lists all packages as project references.

- **Always typecheck from the root** — run `pnpm run typecheck`
- Root Scripts:
  - `pnpm run build` — runs `typecheck` first, then recursively runs `build` in all packages
  - `pnpm run typecheck` — runs `tsc --build --emitDeclarationOnly` using project references
