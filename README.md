# AI Resume Builder App

Professional, production-ready documentation for the full-stack Resume Builder (MERN) application.

## 🚀 Project Overview
A full-stack Resume Builder that lets users create, edit, store and share resumes with a live preview and multiple templates. The app integrates AI for resume parsing and enhancement, image uploads via ImageKit, and user authentication using JWT.

## 🧰 Tech Stack
- MongoDB (Mongoose)
- Express.js
- React (Vite)
- Node.js
- ImageKit for image hosting
- OpenAI for AI-powered resume parsing/enhancement

## ✨ Key Features
- User registration & JWT authentication
- Create, update, delete, and publish resumes
- Image upload and optional background removal via ImageKit
- AI-powered resume parsing and content enhancement
- Multiple resume templates and color customization
- Live preview and PDF generation support

## 📦 Installation
Follow these steps to run the app locally.

### 1) Clone the repository
```bash
git clone <repo-url>
cd resume-builder
```

### 2) Setup Backend
```bash
cd server
npm install
# development (watch)
npm run server
# production
npm start
```

### 3) Setup Frontend
```bash
cd client
npm install
# development
npm run dev
# build for production
npm run build
```

Open the frontend at the Vite dev server URL (by default http://localhost:5173). The client expects API base URL configured via `VITE_BASE_URL` (see Environment variables).

## 🔐 Environment Variables
Create a `.env` file inside the `server` folder (and a `.env` or `.env.local` for the client when needed). Do NOT commit secrets.

Server `.env` (required):
```
PORT=3000
MONGODB_URI=your_mongo_connection_base
JWT_SECRET=your_jwt_secret
OPENAI_API_KEY=sk-xxxx
OPENAI_BASE_URL=
OPENAI_MODEL=gpt-4o-mini
IMAGEKIT_PRIVATE_KEY=ik_private_xxx
```

Client environment (Vite): create `client/.env` or `client/.env.local` with:
```
VITE_BASE_URL=http://localhost:3000/api
```

## 🔧 Project Structure (high level)
- `server/` — Express API, controllers, models, routes, configs
- `client/` — React app (Vite), components, templates

## 🧭 API Summary
Auth: send token in the `Authorization` header. The server middleware expects the token in `req.headers.authorization`.

Main route groups:
- `/api/users` — register, login, user data, user resumes
- `/api/resumes` — create, update (multipart upload), delete, get, public view
- `/api/ai` — AI enhancement endpoints and resume parsing

Refer to `server/readme.md` for a full API reference and implementation notes.

## 📦 Deployment Recommendations
- Frontend: Deploy the `client` build to Vercel, Netlify, or similar.
- Backend: Deploy the `server` to Render, Heroku, Fly.io, or a container platform (AWS ECS, GCP Cloud Run).
- Database: Use MongoDB Atlas for a managed production database.
- Secrets: Store secrets in the platform's environment settings or a secrets manager.

Example quick-deploy plan:
- Build the client and serve static `dist/` from Vercel (or from the same domain as the API for simplified CORS).
- Deploy the server as a Node service on Render with environment variables set.

## ⚙️ Production Hardening Checklist
- Use HTTPS (terminate TLS at your load balancer)
- Add `helmet`, configure `cors` with explicit origins
- Use rate limiting for AI endpoints (`express-rate-limit`)
- Add logging/monitoring (Sentry, Datadog, or similar)
- Use a process manager (`pm2`) or container orchestration
- Store secrets in a vault (AWS Secrets Manager, Render env, etc.)
- Add request validation (e.g., `zod` / `express-validator`)

## 🐳 Docker (optional)
Create a lightweight production image for the API and deploy it to your container registry.

## 🧪 Testing & Local Workflows
- Use Postman or HTTPie to exercise API endpoints during development.
- Use the client dev server to test integrations with local API by setting `VITE_BASE_URL`.

## 🤝 Contributing
- Fork the repo, create a branch, add tests/changes, open a PR.
- Keep PRs small and focused. Add docs for any API or behavior changes.

## 📄 License & Contact
Add your LICENSE and author/maintainer information at the project root.

---
I can also:
- add a `server/.env.example` with the variables listed above,
- create a `docker-compose.yml` for local full-stack development, or
- apply the controller fixes I recommended earlier to make routes/controllers consistent.

Which of these would you like me to do next?
