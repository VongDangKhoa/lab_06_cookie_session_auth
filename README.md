# lab_06_cookie_session_auth

Simple Node.js/Express example demonstrating cookie-based session authentication using `express-session` and `connect-mongo`.

This project shows a minimal authentication flow with:

- User registration (stores hashed password with `bcryptjs`)
- Login which creates a server-side session and sets a cookie (`connect.sid`)
- Protected profile endpoint that requires a valid session
- Logout which destroys the session and clears the cookie

The example uses MongoDB (local) to store user data and session store.

## Project layout

- `app.js` - Express server and session setup
- `routes/auth.js` - Authentication routes (register, login, profile, logout)
- `models/User.js` - Mongoose user model with password hashing
- `package.json` - dependencies

## Requirements

- Node.js (>= 16 recommended)
- npm (or yarn)
- MongoDB running locally on `mongodb://127.0.0.1:27017`

The code expects a MongoDB database named `sessionAuth` (created automatically on first use).

## Install

From the `cookie_session_auth` folder run:

```powershell
npm install
```

## Run

Start the server:

```powershell
node app.js
```

The app listens on http://localhost:3000 by default.

## API Endpoints

Base path: `/auth`

- POST `/auth/register`
  - Body: `{ "username": "<username>", "password": "<password>" }`
  - Response: JSON message on success or error details

- POST `/auth/login`
  - Body: `{ "username": "<username>", "password": "<password>" }`
  - On success, the server creates a session and the response includes a `connect.sid` cookie. Use the cookie for subsequent requests.

- GET `/auth/profile`
  - Requires a valid session (cookie). Returns the user object (without password).

- GET `/auth/logout`
  - Destroys the session on the server and clears the cookie.

## Example using curl (login and access profile)

1) Register:

```powershell
curl -X POST http://localhost:3000/auth/register -H "Content-Type: application/json" -d '{"username":"alice","password":"pass123"}'
```

2) Login (save cookies to a file):

```powershell
curl -c cookies.txt -X POST http://localhost:3000/auth/login -H "Content-Type: application/json" -d '{"username":"alice","password":"pass123"}'
```

3) Access protected profile using saved cookie:

```powershell
curl -b cookies.txt http://localhost:3000/auth/profile
```

4) Logout:

```powershell
curl -b cookies.txt http://localhost:3000/auth/logout
```

> Note: Windows `curl` (PowerShell) handles quoting slightly differently. If you run into issues, consider using a REST client like Postman or Insomnia.

## Configuration notes

- The MongoDB connection string and session secret are currently hard-coded in `app.js`:
  - MongoDB: `mongodb://127.0.0.1:27017/sessionAuth`
  - Session secret: `mysecretkey`

- For production, move sensitive values to environment variables and enable `cookie.secure` when serving over HTTPS.

## Security considerations

- Passwords are hashed with `bcryptjs` before saving.
- Sessions are stored in MongoDB via `connect-mongo`.
- `cookie.httpOnly` is enabled to prevent client-side JS from reading the session cookie.
- `cookie.secure` is currently `false` to allow local HTTP development. Set it to `true` in production with HTTPS.

## Next steps / improvements

- Add environment variable support (e.g., via `dotenv`) for MongoDB URL and session secret.
- Add input validation and rate-limiting on auth endpoints.
- Add CSRF protection for state-changing requests in a browser context.

## License

ISC
