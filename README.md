# CIKIRE - Appointment Management System

Monolithic web application developed for the comprehensive kinesiology clinic CIKIRE.
CIKIRE digitalizes the management of patients, practitioners, availabilities,
services/packages, and appointments for a kinesiology and rehabilitation center.

## Current Status

The current version is a functional academic MVP with:

* Express REST API.

* MongoDB via Mongoose.

* Pug views and client scripts using Fetch.

* JWT authentication in an `HttpOnly` cookie.

* Password hashing with `bcrypt`.

* Role-based authorization.

* Socket.IO and an informational assistant connected to Groq.

* Institutional landing page.

## Technologies

| Component | Technology | 
| ----- | ----- | 
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

```
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

```

## Features

* Public patient registration.

* Login and logout.

* Signed JWT in an `HttpOnly` cookie.

* Opportunistic migration of legacy passwords to `bcrypt`.

* Roles: `patient`, `receptionist`, `practitioner`, `administrative staff`, `coordinator`, and `director`.

* Patient management.

* Practitioner management and active/inactive status.

* Availability management and overlap detection upon creation.

* Service management and session balance tracking.

* Booking with validation for service ownership, balance, availability, and scheduling conflicts.

* Cancellation with session refund.

* Appointment fulfillment by practitioners.

* Creation and status management of internal users with hierarchy.

* Institutional chat with a maximum history of 10 messages and a limit of 8 requests per minute per connection.

* Institutional landing page with a simulated contact form in the browser.

## Domain Model

The main entities are `Usuario` (User), `Paciente` (Patient), `Profesional` (Practitioner),
`Disponibilidad` (Availability), `Servicio` (Service), and `Turno` (Appointment). Relationships use MongoDB
`ObjectId` references.

## Installation and Execution

### Requirements

* Node.js

* Local MongoDB or MongoDB Atlas

* Groq API key to enable the chat

### Setup

1. Install dependencies:

```
npm install

```

2. Create `.env` from `.env.example`:

```
MONGODB_URI=mongodb://localhost:27017/cikire
PORT=3000
JWT_SECRET=replace-with-a-long-random-secret
JWT_EXPIRES_IN=1h
NODE_ENV=development
GROQ_API_KEY=replace-with-your-groq-api-key

```

3. Start in development mode:

```
npm run dev

```

Or start without `nodemon`:

```
npm start

```

By default, the application is available at `http://localhost:3000`.

## Authentication and Authorization

Login validates an active user, compares the password with `bcrypt`, and issues a JWT. The middleware
[`middlewares/loginHandler.js`](middlewares/loginHandler.js) loads the token, protects routes, and checks allowed roles.

The cookie uses `HttpOnly`, `SameSite=Lax`, and `Secure` in production. Logout removes the cookie from the browser. There are no refresh tokens or revocation list; a copied token remains valid until it expires. User status and role are also not revalidated against MongoDB on every request.

Role-based authorization is implemented at the endpoint level.

## Endpoints

### Login

| Method | Route | Description | 
| ----- | ----- | ----- | 
| `GET` | `/login` | Login form | 
| `POST` | `/login` | Authenticate and issue JWT | 
| `GET` | `/login/registro` | Registration form | 
| `POST` | `/login/registro` | Register patient or authorized account creation | 
| `GET` | `/login/logout` | Clear cookie and redirect | 

### API

| Resource | Routes | 
| ----- | ----- | 
| Patients | CRUD at `/api/pacientes` | 
| Practitioners | CRUD at `/api/profesionales` | 
| Availability | CRUD at `/api/disponibilidad` | 
| Services | CRUD at `/api/servicios`; status at `/api/servicios/estado/:pacienteId` | 
| Appointments | CRUD at `/api/turnos`; `/cancelar`, `/atender`, queries by patient/practitioner | 
| Users | `GET`/`POST` `/api/usuarios`; `PATCH` `/api/usuarios/:id/estado` | 

### Views

* `/`

* `/vista/turnos`

* `/vista/pacientes`

* `/vista/profesionales`

* `/vista/usuarios`

## Chat and Landing Page

Socket.IO is initialized alongside the HTTP server. The assistant uses institutional information from [`data/informacionCIKIRE.js`](data/informacionCIKIRE.js) and queries Groq from [`sockets/chatSocket.js`](sockets/chatSocket.js).

The landing page contact form is currently a frontend simulation: it displays confirmation, but does not persist or send the message.

## Testing and Quality

The deliverable documents manual tests with Postman against the local environment and the deployment. The repository contains no automated tests, linting, build steps, or CI. The JavaScript syntax of tracked files can be checked with:

```
node --check index.js

```

For a thorough evaluation, adding tests for authorization, booking rules, concurrency, validations, and security is recommended.

## Security and Known Technical Debt

* Incomplete resource-ownership authorization on some appointments and services.

* Booking and balance updates without a MongoDB transaction.

* Dates and times stored as strings without centralized validation.

* Open Socket.IO CORS and unauthenticated chat.

* Lack of CSRF protection, security headers, and global HTTP rate limiting.

* Use of `innerHTML` with external data in frontend scripts.

* Non-persistent billing for private (out-of-pocket) consultations.

* Contact form without a backend.

* No health checks, metrics, auditing, or backups in the repository.