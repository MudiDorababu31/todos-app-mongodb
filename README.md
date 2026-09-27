# 📝 Todo App — Node.js, Express.js, MongoDB & JWT

A backend Todo application built using **Node.js, Express.js, MongoDB, Mongoose, and JSON Web Tokens (JWT)**.

The application provides user authentication through signup/signin and protects Todo-related routes using JWT-based authentication. MongoDB is used for storing user and Todo data through Mongoose models.

---

## 🚀 Features

* 👤 User Signup
* 🔐 User Signin
* 🎟️ JWT-based authentication
* 🛡️ Protected Todo routes
* ➕ Create Todo
* 📋 Get user's Todos
* 🗑️ Delete Todo
* 🍃 MongoDB integration using Mongoose
* ⚡ REST API architecture
* 📦 Modular authentication middleware
* 🔑 User-specific Todo access

---

## 🛠️ Tech Stack

| Technology           | Purpose               |
| -------------------- | --------------------- |
| Node.js              | JavaScript runtime    |
| Express.js           | Backend web framework |
| MongoDB              | Database              |
| Mongoose             | MongoDB ODM           |
| JSON Web Token (JWT) | Authentication        |
| JavaScript           | Programming language  |
| Postman              | API testing           |

---

# 🏗️ Project Architecture

The application follows a simple backend architecture:

```text
                    ┌─────────────────────┐
                    │      Client         │
                    │ Postman / Frontend  │
                    └──────────┬──────────┘
                               │
                               │ HTTP Request
                               ▼
                    ┌─────────────────────┐
                    │    Express.js      │
                    │      Server        │
                    └──────────┬──────────┘
                               │
                ┌──────────────┴──────────────┐
                │                             │
                ▼                             ▼
       ┌─────────────────┐          ┌─────────────────┐
       │ Authentication  │          │   Todo Routes   │
       │    Middleware   │          │                 │
       └────────┬────────┘          └────────┬────────┘
                │                            │
                │ JWT Verification           │
                ▼                            ▼
       ┌─────────────────┐          ┌─────────────────┐
       │  JWT Token      │          │ Todo Operations │
       │  Validation     │          │ Create / Get /  │
       └─────────────────┘          │ Delete          │
                                    └────────┬────────┘
                                             │
                                             ▼
                                  ┌─────────────────────┐
                                  │      MongoDB        │
                                  │                     │
                                  │ Users / Todos       │
                                  └─────────────────────┘
```

---

# 📂 Project Structure

```text
todo-app/
│
├── index.js
│
├── middleware.js
│
├── models.js
│
├── package.json
│
├── package-lock.json
│
└── .gitignore
```

---

# 📌 File Responsibilities

## `index.js`

This is the main Express server.

It is responsible for:

* Starting the Express application
* Parsing JSON requests
* Signup
* Signin
* Generating JWT tokens
* Creating Todos
* Retrieving Todos
* Deleting Todos
* Starting the server on port `3000`

---

## `middleware.js`

This file contains the authentication middleware.

The middleware:

1. Reads the JWT from the request header.
2. Verifies the token.
3. Extracts the user's ID.
4. Stores it in:

```javascript
req.userId
```

5. Allows the request to continue using:

```javascript
next()
```

Conceptually:

```text
Client
  │
  │ Authorization Token
  ▼
authMiddleware
  │
  ├── Invalid Token ──► 403 Response
  │
  └── Valid Token
          │
          ▼
      req.userId
          │
          ▼
      Todo Route
```

---

## `models.js`

This file contains the MongoDB/Mongoose models.

### User Schema

```javascript
const UserSchema = new mongoose.Schema({
    username: String,
    password: String
});
```

The User model stores:

* Username
* Password

### Todo Schema

```javascript
const TodoSchema = new mongoose.Schema({
    title: String,
    description: String,
    userId: mongoose.Types.ObjectId
});
```

The Todo model stores:

* Todo title
* Todo description
* ID of the user who owns the Todo

This `userId` relationship allows Todos to be associated with individual users.

---

# 🔐 Authentication Flow

The application uses JWT for authentication.

### Step 1 — Signup

The user sends:

```http
POST /signup
```

with:

```json
{
    "username": "nani",
    "password": "123456"
}
```

The server checks whether the user already exists and creates a new user.

Response:

```json
{
    "id": "USER_ID"
}
```

---

### Step 2 — Signin

The user sends:

```http
POST /signin
```

with:

```json
{
    "username": "nani",
    "password": "123456"
}
```

If the credentials are valid, the server generates a JWT.

Response:

```json
{
    "token": "JWT_TOKEN"
}
```

---

### Step 3 — Use the Token

Protected routes require the token.

The current implementation expects the token in:

```http
token: JWT_TOKEN
```

The middleware verifies the token and extracts the user ID.

---

# 🔌 API Documentation

## 1. Signup

### Endpoint

```http
POST /signup
```

### Request Body

```json
{
    "username": "nani",
    "password": "123456"
}
```

### Successful Response

```json
{
    "id": "USER_ID"
}
```

### Existing User Response

```json
{
    "message": "User with this username already exists"
}
```

---

# 2. Signin

### Endpoint

```http
POST /signin
```

### Request Body

```json
{
    "username": "nani",
    "password": "123456"
}
```

### Successful Response

```json
{
    "token": "JWT_TOKEN"
}
```

### Invalid Credentials

```json
{
    "message": "Incorrect credentials"
}
```

---

# 3. Create Todo

### Endpoint

```http
POST /todo
```

### Authentication

Required.

Header:

```http
token: JWT_TOKEN
```

### Request Body

```json
{
    "title": "Learn Node.js",
    "description": "Complete Express.js authentication"
}
```

### Response

```json
{
    "message": "Todo made"
}
```

---

# 4. Get Todos

### Endpoint

```http
GET /todos
```

### Authentication

Required.

Header:

```http
token: JWT_TOKEN
```

### Response

```json
{
    "todos": []
}
```

The API is designed to return Todos belonging to the authenticated user.

---

# 5. Delete Todo

### Endpoint

```http
DELETE /todo/:todoId
```

Example:

```http
DELETE /todo/1
```

### Authentication

Required.

Header:

```http
token: JWT_TOKEN
```

### Successful Response

```json
{
    "message": "Deleted"
}
```

---

# 🗄️ MongoDB Setup

This project uses MongoDB through Mongoose.

In `models.js`, the application connects using:

```javascript
mongoose.connect("");
```

Replace the empty string with your MongoDB connection URI.

For MongoDB Atlas, it will look similar to:

```text
mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/DATABASE_NAME
```

### Example

```javascript
mongoose.connect(
    "mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/todos"
);
```

### ⚠️ Important

Do **not** upload your real MongoDB username/password to GitHub.

A better approach is to use environment variables:

```text
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_secret
```

and access them through:

```javascript
process.env.MONGODB_URI
```

---

# ⚙️ Installation

## 1. Clone the repository

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
```

Move into the project:

```bash
cd todo-app
```

---

## 2. Initialize the project

If `package.json` already exists:

```bash
npm install
```

Otherwise:

```bash
npm init -y
```

---

## 3. Install dependencies

```bash
npm install express mongoose jsonwebtoken
```

---

## 4. Configure MongoDB

Open:

```text
models.js
```

and configure your MongoDB connection.

For production, use a `.env` file instead of directly writing credentials in the source code.

---

## 5. Start the server

```bash
node index.js
```

The server runs on:

```text
http://localhost:3000
```

---

# 🧪 Testing with Postman

You can test the APIs using Postman.

### Recommended testing order

```text
1. Signup
   │
   ▼
2. Signin
   │
   ▼
3. Copy JWT token
   │
   ▼
4. Create Todo
   │
   ▼
5. Get Todos
   │
   ▼
6. Delete Todo
```

---

# 🔄 Complete Request Flow

```text
                  SIGNUP
                    │
                    ▼
             Express Server
                    │
                    ▼
              MongoDB Users
                    │
                    ▼
              User Created


                  SIGNIN
                    │
                    ▼
             Verify Credentials
                    │
                    ▼
                JWT Token
                    │
                    ▼
                 Client


              CREATE TODO
                    │
                    ▼
              JWT Token
                    │
                    ▼
            Auth Middleware
                    │
             ┌──────┴──────┐
             │             │
          Invalid        Valid
             │             │
             ▼             ▼
          403 Error     User ID
                           │
                           ▼
                       Todo Route
```

---

# 🔒 Security Considerations

This project demonstrates JWT authentication, but several security improvements should be made before using it in production.

### 1. Never store plain-text passwords

Currently passwords are stored directly.

A production application should hash passwords using a library such as:

```text
bcrypt
```

---

### 2. Use an environment variable for JWT secret

Currently the secret is written directly in the code:

```javascript
"secret123123"
```

Instead:

```text
JWT_SECRET=your_secure_secret
```

---

### 3. Use environment variables for MongoDB credentials

Never commit:

```text
mongodb+srv://username:password@...
```

to GitHub.

Use:

```text
MONGODB_URI=...
```

inside `.env`.

---

### 4. Validate incoming data

The API should validate:

* Username
* Password
* Todo title
* Todo description
* Todo ID

before processing requests.

---

### 5. Handle invalid JWTs safely

JWT verification can throw an error when the token is malformed or expired.

Production code should handle this using `try/catch`.

---

# ⚠️ Current Implementation Notes

The current source code is a learning/demo implementation and has a few areas that should be improved.

### Todo persistence

Although a Mongoose `todoModel` is defined in `models.js`, the current Todo routes use:

```javascript
TODOS
```

instead of:

```javascript
todoModel
```

Therefore, the Todo routes are not currently using MongoDB for their CRUD operations.

A fully MongoDB-based implementation should use:

```javascript
todoModel.create()
```

for creating Todos,

```javascript
todoModel.find()
```

for retrieving Todos,

and:

```javascript
todoModel.deleteOne()
```

or:

```javascript
todoModel.findByIdAndDelete()
```

for deleting Todos.

---

### Todo ID handling

The current implementation uses:

```javascript
parseInt(req.params.todoId)
```

which suggests numeric Todo IDs.

MongoDB documents normally use MongoDB ObjectIds.

The application should consistently use MongoDB `_id` values if Todos are stored in MongoDB.

---

### Authentication ID type

The JWT stores:

```javascript
userId: userExists.id
```

while the middleware converts it using:

```javascript
parseInt(decoded.userId)
```

MongoDB user IDs are normally ObjectIds, so this conversion should be changed when using MongoDB IDs.

---

# 🚀 Future Improvements

The project can be extended with:

* 🔐 Password hashing with bcrypt
* 🔑 Secure JWT environment variables
* ⏳ JWT expiration
* 🗄️ Complete MongoDB Todo CRUD
* ✅ Update Todo
* ☑️ Todo completion status
* 📅 Due dates
* 🔎 Search Todos
* 📄 Pagination
* 👤 User profile
* 🛡️ Input validation
* 🚦 Rate limiting
* 🌐 Frontend using React
* ☁️ Deployment
* 📊 Todo statistics
* 🧪 Automated API tests

---

# 🎯 Learning Outcomes

This project demonstrates practical understanding of:

* Node.js
* Express.js
* REST APIs
* HTTP methods
* MongoDB
* Mongoose
* JWT authentication
* Middleware
* Protected routes
* Request headers
* Route parameters
* JSON request/response handling
* User-specific data access

---

# 📌 API Summary

| Method | Endpoint        | Authentication | Purpose           |
| ------ | --------------- | -------------- | ----------------- |
| POST   | `/signup`       | ❌              | Create a user     |
| POST   | `/signin`       | ❌              | Authenticate user |
| POST   | `/todo`         | ✅              | Create Todo       |
| GET    | `/todos`        | ✅              | Get user's Todos  |
| DELETE | `/todo/:todoId` | ✅              | Delete Todo       |

---

# 💡 Project Concept

The main idea of the application is:

```text
User
 │
 ├── Signup
 │
 ├── Signin
 │      │
 │      └── JWT Token
 │
 └── Authenticated Requests
        │
        ├── Create Todo
        ├── View Todos
        └── Delete Todo
```

Each authenticated request is associated with the user's identity through the JWT.

---

# 👨‍💻 Author

**MUDI DORABABU**

Full Stack Developer | Node.js | React | MongoDB

---

# ⭐ Support

If you found this project useful, consider giving the repository a ⭐ on GitHub.

---

## 📄 License

This project is created for learning and educational purposes.
