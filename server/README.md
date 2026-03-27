# Resume Builder — Server

Production-ready documentation for the Express.js API that powers the Resume Builder application.

**Contents**
- **Overview:** concise project description and responsibilities
- **Tech Stack:** major libraries and runtime
- **Prerequisites:** tooling and accounts required
- **Environment:** required environment variables with descriptions
- **Install & Run:** dev and production instructions
- **API Reference:** endpoints, methods, auth, and payloads
- **File Uploads & AI:** notes about image handling and OpenAI usage
- **Production Considerations:** security, logging, scaling, and deployment
- **Developer Notes / Known Issues:** implementation details and suggested fixes

**Overview**
- **Project:** Server-side API for a Resume Builder application that stores resumes, allows image uploads, integrates with an AI service to parse/enhance resumes, and provides user authentication.
- **Responsibility:** Serves REST endpoints used by the client (frontend) to manage users and resumes, handle uploads, and call AI-powered resume parsing/enhancement.

**Tech Stack**
- **Runtime:** Node.js (ESM) — see [server/package.json](server/package.json)
- **Framework:** Express.js — entrypoint [server/server.js](server/server.js)
- **Database:** MongoDB via Mongoose — config in [server/configs/db.js](server/configs/db.js)
- **Authentication:** JWT using `jsonwebtoken` and middleware in [server/middlewares/authMiddleware.js](server/middlewares/authMiddleware.js)
- **File Uploads:** `multer` with minimal disk storage — config in [server/configs/multer.js](server/configs/multer.js)
- **Image Processing / Hosting:** ImageKit SDK — [server/configs/imageKit.js](server/configs/imageKit.js)
- **AI:** OpenAI client — config in [server/configs/ai.js](server/configs/ai.js)

**Prerequisites**
- Node.js 18+ and npm/yarn
- A MongoDB connection string (Atlas or self-hosted)
- OpenAI API credentials (or compatible base URL)
- ImageKit private key (optional, required for image uploads)

**Environment Variables**
Create a `.env` file in the `server` folder (or supply env vars via your deployment system). Required variables used by this codebase:
- `PORT` — server port (default 3000)
- `MONGODB_URI` — base MongoDB URI (the app appends `/resume-builder`)
- `JWT_SECRET` — secret for signing JWT tokens
- `OPENAI_API_KEY` — OpenAI API key
- `OPENAI_BASE_URL` — optional custom OpenAI base URL
- `OPENAI_MODEL` — model name used by AI endpoints (e.g. `gpt-4o-mini`) 
- `IMAGEKIT_PRIVATE_KEY` — ImageKit private key for uploads

Example `.env` (do NOT commit to source control):

```
PORT=3000
MONGODB_URI=mongodb+srv://<user>:<pass>@cluster0.mongodb.net
JWT_SECRET=your_jwt_secret_here
OPENAI_API_KEY=sk-xxxx
OPENAI_BASE_URL=
OPENAI_MODEL=gpt-4o-mini
IMAGEKIT_PRIVATE_KEY=ik_private_xxx
```

**Install & Run**
- **Install dependencies**:

```bash
cd server
npm install
```

- **Development (watch)**:

```bash
npm run server
```

- **Production**:

```bash
npm start
```

- Consider using a process manager (e.g. `pm2`) or a container for production runs.

**NPM Scripts**
- `start`: runs `node server.js` (production)
- `server`: runs `nodemon server.js` (development)

**API Reference (summary)**
Auth: The API uses a bearer token passed in the `Authorization` header. The middleware expects `Authorization: <token>` (see [server/middlewares/authMiddleware.js](server/middlewares/authMiddleware.js)).

- **User**
	- `POST /api/users/register` — Register a new user. Body: `{ name, email, password }`.
	- `POST /api/users/login` — Login and receive a JWT. Body: `{ email, password }`.
	- `GET /api/users/data` — Get current user data. Protected.
	- `GET /api/users/resumes` — List resumes for current user. Protected.

- **Resumes**
	- `POST /api/resumes/create` — Create a new resume. Protected. Body: `{ title }`.
	- `PUT /api/resumes/update` — Update a resume. Protected. Accepts multipart `image` (field name `image`) and `resumeData` (JSON/string) in the body. Example: `FormData` with `image` and `resumeData`.
	- `DELETE /api/resumes/delete/:resumeId` — Delete resume. Protected. (See Developer Notes below about parameter handling.)
	- `GET /api/resumes/get/:resumeId` — Get resume (private). Protected. (See Developer Notes about parameter handling.)
	- `GET /api/resumes/public/:resumeId` — Get public resume by id. Public endpoint.

- **AI / Resume Parsing & Enhancements**
	- `POST /api/ai/enhance-pro-sum` — Enhance a professional summary. Protected. Body: `{ userContent }`.
	- `POST /api/ai/enhance-job-desc` — Enhance a job description. Protected. Body: `{ userContent }`.
	- `POST /api/ai/upload-resume` — Upload a raw resume text for AI extraction and persist. Protected. Body: `{ resumeText, title }`.

**Request / Response Conventions**
- All endpoints return JSON with either a `message` and/or resource-specific fields (e.g., `resume`, `token`).
- Protected endpoints require a valid JWT in `Authorization` header; the middleware decodes to `req.userId`.

**File Uploads & Image Handling**
- Image uploads use `multer` with minimal disk storage and are then uploaded to ImageKit via the server. The `update` route handles optional background removal (trigger via `removeBackground` flag in `resumeData`).
- Uploaded images are streamed to ImageKit; the server stores the resulting `response.url` in `personal_info.image` for the resume.

**OpenAI Usage**
- AI endpoints call the OpenAI chat completions API (client configured in [server/configs/ai.js](server/configs/ai.js)).
- The `upload-resume` AI flow requests the model to return a JSON object only; the controller parses that and writes to the database.

**Production Considerations & Recommendations**
- **Secrets & Config**: Use a secrets manager or environment config (do not store secrets in repo). Rotate keys regularly.
- **Input Validation**: Add request validation (e.g., `express-validator` or `zod`) before controller logic to prevent malformed data.
- **Rate Limiting & Abuse Protection**: Protect AI endpoints with rate limiting (e.g., `express-rate-limit`) and consider usage quotas per user to control costs.
- **Logging & Monitoring**: Integrate structured logging (e.g., `pino` or `winston`) and monitoring (Sentry, Datadog).
- **Security Middleware**: Add `helmet`, stricter `cors` configuration with allowed origins, and sanitize inputs to prevent injections.
- **TLS & Reverse Proxy**: Terminate TLS at a reverse proxy or load balancer in production; run Node behind a process manager.
- **Scaling**: Make sure MongoDB connection string and ImageKit usage are production-ready (connection pooling, appropriate instance sizes). Consider horizontal scaling behind a load balancer and sticky sessions not required due to stateless JWT auth.

**Docker (example)**
Use a small production image (example Dockerfile snippet):

```
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
ENV NODE_ENV=production
EXPOSE 3000
CMD [ "node", "server.js" ]
```

**Developer Notes / Known Issues**
- **Parameter mismatch (routes vs controllers):** Some routes pass `resumeId` via URL parameters (e.g., `DELETE /delete/:resumeId`, `GET /get/:resumeId`) but corresponding controllers (`deleteResume`, `getResumeById`) expect `resumeId` in the request body. Recommendation: standardize on `req.params.resumeId` for routes that include `:resumeId` or update routes to not use a URL parameter.
- **User ID casing:** `authMiddleware` sets `req.userId` but some controllers read `req.userid` (lowercase) — this will result in undefined `userId` in `getUserById` / `getUserResumes`. Fix controllers to use `req.userId` consistently.
- **Token header convention:** The middleware expects the token in `req.headers.authorization` without a `Bearer ` prefix. If clients send `Bearer <token>`, strip the `Bearer ` prefix in the middleware.

**Where to look in the code**
- Entrypoint: [server/server.js](server/server.js)
- DB config: [server/configs/db.js](server/configs/db.js)
- Routes: [server/routes](server/routes)
- Controllers: [server/controllers](server/controllers)
- Models: [server/models](server/models)

**License & Attribution**
- Adjust this file with your project's license and author information.

---
If you want, I can (choose one):
- apply suggested controller fixes for the `resumeId` and `userId` inconsistencies,
- add a ` .env.example` file with the variables listed above, or
- add a CI-friendly `Dockerfile` and `docker-compose.yml` for local testing.


