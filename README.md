# AI Cold Email Generator

A full-stack application for generating personalized cold emails, LinkedIn messages, and follow-up emails from a prompt. Users can register, verify their email, sign in, and view their generated-email history.

## Features

- User registration with email OTP verification
- Login with JWT-based authentication
- AI-generated email subject and body, LinkedIn DM, and follow-up email
- Per-user generation history
- React frontend and Express API in one repository

## Tech Stack

- **Frontend:** React, Vite, Tailwind CSS
- **Backend:** Node.js, Express
- **Database:** MongoDB with Mongoose
- **Authentication:** JWT and bcrypt
- **AI:** Groq API
- **Email:** Nodemailer (Gmail)

## Prerequisites

- Node.js and npm (use a Node.js version supported by Vite 8)
- A MongoDB database
- A Groq API key
- Gmail credentials for sending verification emails

## Run Locally

Run these commands from the repository root.

### 1. Install dependencies

```bash
npm run install-all
```

This installs dependencies in the root, `server`, and `client` folders.

### 2. Configure environment variables

Create `server/.env`:

```env
PORT=5000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_long_random_secret
GROQ_API_KEY=your_groq_api_key
EMAIL_USER=your_gmail_address
EMAIL_PASS=your_gmail_app_password
FRONTEND_URL=http://localhost:5173
```

Create `client/.env`:

```env
VITE_API_URL=http://localhost:5000/api
```

Keep these files private; do not commit them or put server secrets in the client environment file. `PORT` defaults to `5000` if it is not set. Email credentials are needed for OTP email delivery.

### 3. Start the app

```bash
npm run dev
```

- Frontend: <http://localhost:5173>
- API: <http://localhost:5000>

## Build and Start the Production App Locally

From the repository root:

```bash
npm run build
npm start
```

The build command creates the frontend files in `client/dist`. The Express server serves those files as well as the API. Open <http://localhost:5000>.

## API Endpoints

| Method | Endpoint | Purpose | Authentication |
| --- | --- | --- | --- |
| `POST` | `/api/auth/register` | Register a user and send a verification OTP | No |
| `POST` | `/api/auth/verify-otp` | Verify the user's email OTP | No |
| `POST` | `/api/auth/login` | Sign in and receive a JWT | No |
| `POST` | `/api/ai/generate-email` | Generate email content and save it to history | Bearer token |
| `GET` | `/api/ai/history` | Retrieve the signed-in user's history | Bearer token |

Protected endpoints expect the token in the `Authorization` header:

```text
Authorization: Bearer <token>
```

The generation endpoint accepts a JSON body such as:

```json
{
  "prompt": "Write a cold email for a backend engineering role"
}
```

## Deploy to Render

The Express server serves both the built frontend and API, so deploy this repository as **one Render Web Service**.

1. Push the repository to GitHub and connect it to a new Render Web Service.
2. Leave the service's **Root Directory** blank (the repository root).
3. Configure the service:

   **Build Command**

   ```bash
   npm install && npm --prefix server install && npm --prefix client install && npm --prefix client run build
   ```

   **Start Command**

   ```bash
   cd server && npm start
   ```

4. Add these environment variables in Render:
   - `MONGODB_URI`
   - `JWT_SECRET`
   - `GROQ_API_KEY`
   - `EMAIL_USER` and `EMAIL_PASS` for OTP email delivery
   - `FRONTEND_URL` set to the deployed service URL, for example `https://your-service.onrender.com`

   Render provides `PORT`; the server uses it automatically. Do not copy secret values into this README or commit them to the repository.

For this single-service deployment, `client/.env` is local-only and should not be committed. The frontend's `/api` fallback sends requests to the same Render service. You do not need to configure `VITE_API_URL` in Render unless you later host the frontend and backend separately.

## Notes

- The root `.gitignore` excludes environment files, `node_modules`, and the frontend build output.
- The project does not currently include Docker or a GitHub Actions workflow.
