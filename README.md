# TechEase Backend

REST API for TechEase, a quiz portal with JWT and Google sign-in, quiz management, image uploads and AI-generated quizzes.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)
![Cloudinary](https://img.shields.io/badge/Cloudinary-3448C5?style=for-the-badge&logo=cloudinary&logoColor=white)
![Google Gemini](https://img.shields.io/badge/Google_Gemini-8E75B2?style=for-the-badge&logo=googlegemini&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)

## About

TechEase Backend is the Express + MongoDB API behind a quiz platform. Admins create quizzes (by hand or with Google Gemini), users take them, and in-progress attempts are tracked per user. Authentication supports email/password and Google sign-in through Firebase, with role-specific JWTs stored in HTTP-only cookies.

## Features

- **Authentication**
  - Email/password sign-up and sign-in (bcrypt-hashed passwords)
  - Google login: verifies a Firebase ID token and creates or links the user
  - Separate `user` and `admin` roles with role-specific JWT secrets and cookies (`usertoken` / `admintoken`)
  - Profile and logout endpoints
- **Quiz management**
  - Create, edit and delete quizzes with category (Aptitude, Miscellaneous, Core) and difficulty (Easy, Medium, Hard)
  - Add questions one at a time (with an optional image uploaded to Cloudinary) or in bulk
  - Question types: MCQ, MSQ and Text
  - Paginated quiz listing with title search
- **AI quiz generation** with Google Gemini (`gemini-1.5-flash`), from a topic, total marks, question count and difficulty
- **Attempts**: fetching a quiz for a user creates or resumes an unsubmitted solution record; a submit endpoint stores responses
- **University lookup**: proxies the Hipolabs universities API for Indian universities by name
- **CI/CD**: a GitHub Actions workflow deploys to an EC2 instance on push to `main` (git pull, npm install, PM2 restart)

## Tech Stack

- Node.js (ES modules), Express 4
- MongoDB with Mongoose 8
- Firebase Admin SDK, jsonwebtoken, bcryptjs, cookie-parser
- Cloudinary + Multer (`multer-storage-cloudinary`) for image uploads
- Google Generative AI SDK (Gemini)
- Axios, Morgan, CORS, dotenv
- GitHub Actions, PM2 on AWS EC2

## Project Structure

```
app/
  app.js           # Express app, middleware, route mounting
  config/          # CORS, DB connection, cookie options, Firebase credentials
  routes/          # auth, quiz, solution routers
  controllers/     # auth, quiz, quiz generation, solution handlers
  service/         # Gemini quiz generation
  middlewares/     # Firebase/JWT auth, Cloudinary upload, error handler
  models/          # User, Quiz, Solution schemas
  validators/      # Email, password, quiz/question/response validators
  utils/           # Response and error helpers
.github/workflows/main.yml   # Deploy to EC2
```

## Getting Started

**Prerequisites:** Node.js 18+, a MongoDB instance, a Firebase project (service account), a Cloudinary account and a Gemini API key.

```bash
cd app
npm install
npm run dev        # nodemon app.js
# or
npm start
```

The server listens on `PORT` (default `3000`).

### Environment Variables

Create `app/.env` with:

| Name | Description |
| --- | --- |
| `MONGODB_URI` | MongoDB connection string |
| `CORS_ORIGIN` | Allowed frontend origin |
| `GEMINI_API_KEY` | Google Gemini API key |
| `PORT` | Optional, defaults to `3000` |
| `NODE_ENV` | Optional, defaults to `DEVELOPMENT` |

## API Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/api/v1/auth/signup` | Register with email and password |
| POST | `/api/v1/auth/signin` | Sign in and set the auth cookie |
| POST | `/api/v1/auth/google-login` | Sign in with a Firebase Google ID token |
| GET | `/api/v1/auth/profile` | Current user profile (auth required) |
| POST | `/api/v1/auth/logout` | Clear auth cookies |
| POST | `/api/v1/quiz/add/` | Create a quiz |
| POST | `/api/v1/quiz/add/question` | Add a question (multipart, optional `image`) |
| POST | `/api/v1/quiz/add/questions` | Add questions in bulk |
| GET | `/api/v1/quiz/get/` | List quizzes (`page`, `limit`, `search`) |
| GET | `/api/v1/quiz/:id` | Get a quiz with its questions |
| POST | `/api/v1/quiz/edit/:id` | Edit a quiz |
| GET | `/api/v1/quiz/del/:id` | Delete a quiz |
| GET | `/api/v1/quiz/question/del` | Delete a question |
| GET | `/api/v1/quiz/generate/` | Generate a quiz with Gemini (`topic`, `marks`, `noOfQuestions`, `difficulty`) |
| GET | `/api/v1/quiz/solution/:id` | Get the quiz and the user's in-progress attempt (`userId` query) |
| POST | `/api/v1/solution/user/submit/` | Submit quiz responses |
| GET | `/university/data/:name/` | Search Indian universities by name |

## Author

**Aryan Bhandari**: [GitHub](https://github.com/aryanbha123)
