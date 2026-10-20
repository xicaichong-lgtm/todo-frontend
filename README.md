# TODO App - Frontend

Web interface for a TODO app with user accounts. Installable as a PWA.

**Live demo:** https://todo-frontend-rzw4.onrender.com

> Hosted on free tiers: the first load can take up to a minute while the backend wakes up. After that it is fast.

**Tech stack:** Vue 3, Vuetify, Vue Router, TypeScript, Vite, vite-plugin-pwa

## Features

- Register and log in
- Add, tick off and delete your own TODOs
- Stays logged in until the login token expires
- Installable on phone or desktop as a PWA

## Run locally

1. Start the backend first: https://github.com/xicaichong-lgtm/todo-backend
2. Install dependencies and start the frontend:

```
npm install
npm run dev
```

3. Open http://localhost:3001 in your browser.

By default the frontend talks to the backend at http://localhost:3000. To use a different backend, set the `VITE_API_URL` environment variable.

## Deployment


Hosted as a Render Static Site.

- Build command: `npm install && npm run build`
- Publish directory: `dist`
- Environment variable: `VITE_API_URL` (the backend URL)
- Rewrite rule: `/*` to `/index.html`, so Vue Router works on direct links