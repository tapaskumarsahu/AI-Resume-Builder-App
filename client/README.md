
# Resume Builder — React Client

A modern, responsive React front-end for the Resume Builder application. This client is built with Vite, React, Tailwind CSS and integrates with a Node/Express backend to create, edit and preview resumes using multiple templates.

**Key features**
- Dynamic resume builder UI with form sections for personal info, education, experience, projects, skills and summary
- Live preview with multiple templates (Classic, Minimal, Modern, Minimal with Image)
- Template selector and color picker for styling
- PDF generation / preview support and image upload integration via the backend
- Client-side state management using Redux Toolkit
- Built with Vite for fast local development and optimized production builds

## Tech stack
- React 19
- Vite
- Tailwind CSS
- Redux Toolkit + React Redux
- React Router
- Axios for HTTP client
- ESLint for linting

## Prerequisites
- Node.js (v18 or later recommended)
- npm or yarn
- Running backend API (see server folder) when using real API endpoints

## Getting started (local development)
1. Clone the repository and change to the client folder:

```bash
git clone <repo-url>
cd resume-builder/client
```

2. Install dependencies:

```bash
npm install
# or
# yarn
```

3. Create a `.env` file at the project root (client/) with required environment variables (see below).

4. Start the dev server:

```bash
npm run dev
# or
# yarn dev
```

Open http://localhost:5173 (Vite default) in your browser.

## Environment variables
The client reads configuration from Vite environment variables. Create a `.env` file (or use your environment) and set:

```bash
VITE_BASE_URL=http://localhost:5000/api
```

- `VITE_BASE_URL`: Base URL for the backend API used by the client. Update the host/port to match your running server.

Notes:
- Vite requires env variables to be prefixed with `VITE_` to be exposed to the client.

## Available scripts
Run these from the `client` folder.

- `npm run dev` — Start the Vite development server
- `npm run build` — Produce an optimized production build (output: `dist/`)
- `npm run preview` — Locally preview the production build
- `npm run lint` — Run ESLint across the source

Example:

```bash
npm install
npm run dev
```

## Building and deploying
1. Build the static assets:

```bash
npm run build
```

2. The build output will be in the `dist/` directory. Serve these files using any static hosting (Netlify, Vercel, GitHub Pages, or serve them behind your Node/Express server).

Deployment tips:
- If you serve the frontend from the same domain as the backend, set `VITE_BASE_URL` accordingly to avoid CORS issues.
- Ensure your hosting provider supports single-page apps routing (redirect 404s to `index.html`).

## Project structure (client)

- `index.html` — Vite entry
- `src/main.jsx` — App bootstrap
- `src/App.jsx` — Top-level routes and layout
- `src/app/store.js` — Redux store
- `src/components/` — Reusable components (forms, preview, templates, UI)
- `src/pages/` — Page-level components (Home, Dashboard, Builder, Preview, Login)
- `src/configs/api.js` — Axios instance (uses `VITE_BASE_URL`)
- `src/assets/` — Static assets

## Integrations & Notes
- The client expects the API to provide endpoints for authentication and resume CRUD operations — see the `server` folder for matching server routes.
- File upload and image handling are performed via the backend; ensure your server config (ImageKit / Multer) is running and accessible.

## Linting
The project includes ESLint configuration. Run:

```bash
npm run lint
```

Fix linting issues manually or with your editor integrations.

## Troubleshooting
- If requests fail: confirm `VITE_BASE_URL` matches your backend and the server is running.
- Port conflicts: Vite will suggest an alternative port; you can force one using `--port` flag or `vite.config.js`.

## Contributing
- Fork the repo, create a branch with a descriptive name, implement your change, write clear commit messages and open a PR.
- Keep changes focused and add documentation for any public API or UX changes.

## License
This repository's license should be specified at the project root. Add or update a `LICENSE` file as needed.

## Contact
For questions or support, open an issue in the main repository or contact the maintainers listed in the repository metadata.

---

This README focuses on the `client` application. For full-stack setup, see the server folder README or server documentation in the project root.
