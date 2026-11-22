# CIKIRE - Appointment Management System

Monolithic web application developed for the comprehensive kinesiology clinic CIKIRE.
CIKIRE digitalizes the management of patients, practitioners, availabilities,
services/packages, and appointments for a kinesiology and rehabilitation center.

## Current Status

The current version is a functional academic MVP with:

- Express REST API.
- MongoDB via Mongoose.
- Pug views and client scripts using Fetch.
- JWT authentication in an `HttpOnly` cookie.
- Password hashing with `bcrypt`.
- Role-based authorization.
- Socket.IO and an informational assistant connected to Groq.
- Institutional landing page.

## Technologies

| Component | Technology |
|---|---|
| Runtime | Node.js |
| HTTP | Express |
| Database | MongoDB |
| ODM | Mongoose |
| Templates | Pug |
| Authentication | `jsonwebtoken` + `bcrypt` |
| Real-time | Socket.IO |
| Assistant | Groq API via OpenAI-compatible client |
| Configuration | dotenv |
| Development | nodemon |

> **Note:** The installed dependency is `bcrypt`, not `bcryptjs`.

## Architecture

The project follows a lightweight MVC pattern:

```text
config/        # MongoDB, Groq, and Socket.IO
controllers/   # Business rules and data access
data/          # Institutional information for the assistant
middlewares/   # JWT, roles, and error handling
models/        # Mongoose schemas
public/        # Browser CSS, images, and JavaScript
routes/        # Express endpoints
sockets/       # Chat Socket.IO events
utils/         # JWT utilities
views/         # Pug templates
docs/          # Technical documentation
