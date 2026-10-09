# NexusProxy — Version 3

Responsive proxy subscription storefront with original branding, Express + PostgreSQL backend, account registration/login, admin overview, Stripe card checkout, and Coinbase Commerce crypto checkout scaffolding.

## Important limitations before launch

- This is **not a working proxy network**. Connect a legitimate proxy infrastructure provider to fulfil subscriptions.
- Stripe card checkout has a signed webhook for marking paid 30-day access. It is a one-time payment, not automatic recurring billing.
- Crypto checkout creates Coinbase Commerce charges, but crypto payment activation is **not implemented**. Add and verify Coinbase Commerce webhook signatures before activating crypto-paid subscriptions.
- Plans and metrics are examples. Confirm capacity, policies, taxes, refunds, and legal compliance before accepting live money.
- Before production, add CSRF protection, email verification, password reset, account deletion, audit logging, cancellation/refund handling, monitoring, and a stricter Content Security Policy.

## Local setup

1. Install Node.js 20+ and PostgreSQL.
2. Copy `.env.example` to `.env`.
3. Set `DATABASE_URL` and a `SESSION_SECRET` at least 32 characters long.
4. Run `npm install` then `npm start`.
5. Open `http://localhost:3000`.

Set `ADMIN_EMAIL` and `ADMIN_PASSWORD` before first start to seed an admin user. Existing accounts are not automatically promoted.

## Deploy from Android with GitHub + Render

1. Download and extract this ZIP on your phone.
2. On GitHub, create a repository named `nexusproxy`.
3. Upload the **contents** of the extracted folder to the repository root. `server.js`, `package.json`, `render.yaml`, and `public/` should be directly at root.
4. Never upload `.env` or secret API keys.
5. On Render, use **New → Blueprint** and connect this repository so it can read `render.yaml`. Review the web service and PostgreSQL database.
6. If creating the web service manually, set Runtime **Node**, Build command `npm install`, Start command `npm start`, Health check `/api/health`; create a PostgreSQL database separately.
7. Set Render environment variables: `NODE_ENV=production`, `BASE_URL=https://YOUR-SERVICE.onrender.com`, `DATABASE_URL` (Render PostgreSQL connection string), `SESSION_SECRET` (random 32+ characters), `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, and `COINBASE_COMMERCE_API_KEY`.
8. Deploy and visit `https://YOUR-SERVICE.onrender.com/api/health`; expect `{"ok":true,"service":"NexusProxy"}`.
9. In Stripe, add webhook endpoint `https://YOUR-SERVICE.onrender.com/api/webhooks/stripe` and subscribe to `checkout.session.completed`. Save its signing secret as `STRIPE_WEBHOOK_SECRET` in Render, then redeploy.
10. Test provider sandbox/test mode first. Switch to live payment keys only after verified payments and fulfilment work correctly.

Render docs: https://render.com/docs/deploy-node-express-app and https://render.com/docs/web-services.

## API routes

`GET /api/health`, `GET /api/plans`, `POST /api/register`, `POST /api/login`, `POST /api/logout`, `GET /api/me`, `POST /api/checkout/card`, `POST /api/checkout/crypto`, `POST /api/webhooks/stripe`, `GET /api/admin/overview`.

## Security included

bcrypt password hashing, login/register rate limits, PostgreSQL-backed sessions, HTTP-only/SameSite cookies, secure cookies in production, Helmet headers, basic input validation, signed Stripe webhook verification, and parameterized SQL queries.
