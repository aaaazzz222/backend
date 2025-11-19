# Portfolio & Blog API Backend

A RESTful API backend for Portfolio & Blog application built with Node.js, Express, and MongoDB.

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Running the Server](#running-the-server)
- [API Endpoints](#api-endpoints)
  - [User Authentication](#user-authentication)
  - [Projects](#projects)
  - [Blog Posts](#blog-posts)
  - [Comments](#comments)
  - [Contact](#contact)
- [Authentication](#authentication)
- [Error Handling](#error-handling)
- [Database Models](#database-models)

## Features

- User authentication with JWT
- Password hashing with bcrypt
- RESTful API design
- MVC architecture
- MongoDB with Mongoose ODM
- Secure HTTP headers with Helmet
- CORS enabled
- Comprehensive error handling

## Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js 4.18.2
- **Database**: MongoDB with Mongoose 8.0
- **Authentication**: JWT (jsonwebtoken 9.0.2)
- **Password Encryption**: bcryptjs 2.4.3
- **Security**: Helmet 7.1.0
- **CORS**: cors 2.8.5
- **Environment Variables**: dotenv 16.3.1

## Installation

```bash
# Install dependencies
npm install
```

## Environment Variables

Create a `.env` file in the backend root directory:

```env
PORT=4000
MONGO_URI=mongodb://localhost:27017/portfolio_blog
JWT_SECRET=your_jwt_secret_key_here
NODE_ENV=development
```

### Environment Variable Descriptions

| Variable | Description | Example |
|----------|-------------|---------|
| `PORT` | Server port number | `4000` |
| `MONGO_URI` | MongoDB connection string | `mongodb://localhost:27017/portfolio_blog` |
| `JWT_SECRET` | Secret key for JWT token generation | `your_secret_key` |
| `NODE_ENV` | Environment mode | `development` or `production` |

## Running the Server

### Development Mode (with nodemon)
```bash
npm run dev
```

### Production Mode
```bash
npm start
```

The server will run on `http://localhost:4000` (or the port specified in `.env`).

---

## API Endpoints

Base URL: `http://localhost:4000/api`

### User Authentication

#### 1. Register User

**Endpoint:** `POST /api/users/register`

**Description:** Register a new user account

**Access:** Public

**Request Body:**
```json
{
  "username": "string (required, unique)",
  "email": "string (required, unique, valid email format)",
  "password": "string (required, min 6 characters)"
}
```

**Example Request:**
```json
{
  "username": "johndoe",
  "email": "johndoe@example.com",
  "password": "password123"
}
```

**Success Response (201):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "username": "johndoe",
  "email": "johndoe@example.com",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Error Responses:**
- `400 Bad Request` - User already exists or invalid data
- `500 Internal Server Error` - Server error

---

#### 2. Login User

**Endpoint:** `POST /api/users/login`

**Description:** Authenticate user and get JWT token

**Access:** Public

**Request Body:**
```json
{
  "email": "string (required, valid email format)",
  "password": "string (required)"
}
```

**Example Request:**
```json
{
  "email": "johndoe@example.com",
  "password": "password123"
}
```

**Success Response (200):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "username": "johndoe",
  "email": "johndoe@example.com",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Error Responses:**
- `401 Unauthorized` - Invalid email or password
- `500 Internal Server Error` - Server error

---

### Projects

#### 3. Get All Projects

**Endpoint:** `GET /api/projects`

**Description:** Retrieve all projects

**Access:** Public

**Request Parameters:** None

**Success Response (200):**
```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "title": "Project Title",
    "description": "Project description",
    "imageUrl": "https://example.com/image.jpg",
    "repoUrl": "https://github.com/user/repo",
    "liveUrl": "https://project.com",
    "user": {
      "_id": "507f191e810c19729de860ea",
      "username": "johndoe",
      "email": "johndoe@example.com"
    },
    "createdAt": "2025-11-19T10:30:00.000Z",
    "updatedAt": "2025-11-19T10:30:00.000Z"
  }
]
```

**Error Responses:**
- `500 Internal Server Error` - Server error

---

#### 4. Get Single Project

**Endpoint:** `GET /api/projects/:id`

**Description:** Retrieve a single project by ID

**Access:** Public

**URL Parameters:**
- `id` - MongoDB ObjectId of the project

**Example:** `GET /api/projects/507f1f77bcf86cd799439011`

**Success Response (200):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "Project Title",
  "description": "Project description",
  "imageUrl": "https://example.com/image.jpg",
  "repoUrl": "https://github.com/user/repo",
  "liveUrl": "https://project.com",
  "user": {
    "_id": "507f191e810c19729de860ea",
    "username": "johndoe",
    "email": "johndoe@example.com"
  },
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T10:30:00.000Z"
}
```

**Error Responses:**
- `404 Not Found` - Project not found
- `500 Internal Server Error` - Server error

---

#### 5. Create Project

**Endpoint:** `POST /api/projects`

**Description:** Create a new project

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**Request Body:**
```json
{
  "title": "string (required)",
  "description": "string (required)",
  "imageUrl": "string (optional, valid URL)",
  "repoUrl": "string (optional, valid URL)",
  "liveUrl": "string (optional, valid URL)"
}
```

**Example Request:**
```json
{
  "title": "My Awesome Project",
  "description": "A detailed description of the project",
  "imageUrl": "https://example.com/project-image.jpg",
  "repoUrl": "https://github.com/johndoe/awesome-project",
  "liveUrl": "https://awesome-project.com"
}
```

**Success Response (201):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "My Awesome Project",
  "description": "A detailed description of the project",
  "imageUrl": "https://example.com/project-image.jpg",
  "repoUrl": "https://github.com/johndoe/awesome-project",
  "liveUrl": "https://awesome-project.com",
  "user": "507f191e810c19729de860ea",
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T10:30:00.000Z"
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `400 Bad Request` - Invalid data
- `500 Internal Server Error` - Server error

---

#### 6. Update Project

**Endpoint:** `PUT /api/projects/:id`

**Description:** Update an existing project

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**URL Parameters:**
- `id` - MongoDB ObjectId of the project

**Request Body:** (All fields are optional, only send fields you want to update)
```json
{
  "title": "string (optional)",
  "description": "string (optional)",
  "imageUrl": "string (optional)",
  "repoUrl": "string (optional)",
  "liveUrl": "string (optional)"
}
```

**Example Request:**
```json
{
  "title": "Updated Project Title",
  "description": "Updated description"
}
```

**Success Response (200):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "Updated Project Title",
  "description": "Updated description",
  "imageUrl": "https://example.com/project-image.jpg",
  "repoUrl": "https://github.com/johndoe/awesome-project",
  "liveUrl": "https://awesome-project.com",
  "user": "507f191e810c19729de860ea",
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T12:00:00.000Z"
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `404 Not Found` - Project not found
- `500 Internal Server Error` - Server error

---

#### 7. Delete Project

**Endpoint:** `DELETE /api/projects/:id`

**Description:** Delete a project

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**URL Parameters:**
- `id` - MongoDB ObjectId of the project

**Example:** `DELETE /api/projects/507f1f77bcf86cd799439011`

**Success Response (200):**
```json
{
  "message": "Project removed"
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `404 Not Found` - Project not found
- `500 Internal Server Error` - Server error

---

### Blog Posts

#### 8. Get All Blog Posts

**Endpoint:** `GET /api/blog`

**Description:** Retrieve all blog posts

**Access:** Public

**Request Parameters:** None

**Success Response (200):**
```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "title": "Blog Post Title",
    "content": "Blog post content...",
    "author": {
      "_id": "507f191e810c19729de860ea",
      "username": "johndoe",
      "email": "johndoe@example.com"
    },
    "createdAt": "2025-11-19T10:30:00.000Z",
    "updatedAt": "2025-11-19T10:30:00.000Z"
  }
]
```

**Error Responses:**
- `500 Internal Server Error` - Server error

---

#### 9. Get Single Blog Post

**Endpoint:** `GET /api/blog/:id`

**Description:** Retrieve a single blog post with all comments

**Access:** Public

**URL Parameters:**
- `id` - MongoDB ObjectId of the blog post

**Example:** `GET /api/blog/507f1f77bcf86cd799439011`

**Success Response (200):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "Blog Post Title",
  "content": "Blog post content...",
  "author": {
    "_id": "507f191e810c19729de860ea",
    "username": "johndoe",
    "email": "johndoe@example.com"
  },
  "comments": [
    {
      "_id": "607f1f77bcf86cd799439022",
      "body": "Great post!",
      "author": {
        "_id": "507f191e810c19729de860eb",
        "username": "janedoe",
        "email": "janedoe@example.com"
      },
      "createdAt": "2025-11-19T11:00:00.000Z"
    }
  ],
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T10:30:00.000Z"
}
```

**Error Responses:**
- `404 Not Found` - Blog post not found
- `500 Internal Server Error` - Server error

---

#### 10. Create Blog Post

**Endpoint:** `POST /api/blog`

**Description:** Create a new blog post

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**Request Body:**
```json
{
  "title": "string (required)",
  "content": "string (required)"
}
```

**Example Request:**
```json
{
  "title": "My First Blog Post",
  "content": "This is the content of my first blog post. It can be very long..."
}
```

**Success Response (201):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "My First Blog Post",
  "content": "This is the content of my first blog post. It can be very long...",
  "author": {
    "_id": "507f191e810c19729de860ea",
    "username": "johndoe",
    "email": "johndoe@example.com"
  },
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T10:30:00.000Z"
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `400 Bad Request` - Invalid data
- `500 Internal Server Error` - Server error

---

#### 11. Update Blog Post

**Endpoint:** `PUT /api/blog/:id`

**Description:** Update an existing blog post (author only)

**Access:** Protected (JWT Token Required, Author Only)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**URL Parameters:**
- `id` - MongoDB ObjectId of the blog post

**Request Body:** (All fields are optional)
```json
{
  "title": "string (optional)",
  "content": "string (optional)"
}
```

**Example Request:**
```json
{
  "title": "Updated Blog Post Title",
  "content": "Updated content..."
}
```

**Success Response (200):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "Updated Blog Post Title",
  "content": "Updated content...",
  "author": {
    "_id": "507f191e810c19729de860ea",
    "username": "johndoe",
    "email": "johndoe@example.com"
  },
  "createdAt": "2025-11-19T10:30:00.000Z",
  "updatedAt": "2025-11-19T12:00:00.000Z"
}
```

**Error Responses:**
- `401 Unauthorized` - No token, invalid token, or not the author
- `404 Not Found` - Blog post not found
- `500 Internal Server Error` - Server error

---

#### 12. Delete Blog Post

**Endpoint:** `DELETE /api/blog/:id`

**Description:** Delete a blog post (author only)

**Access:** Protected (JWT Token Required, Author Only)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**URL Parameters:**
- `id` - MongoDB ObjectId of the blog post

**Example:** `DELETE /api/blog/507f1f77bcf86cd799439011`

**Success Response (200):**
```json
{
  "message": "Blog post removed"
}
```

**Error Responses:**
- `401 Unauthorized` - No token, invalid token, or not the author
- `404 Not Found` - Blog post not found
- `500 Internal Server Error` - Server error

---

### Comments

#### 13. Get Comments for a Blog Post

**Endpoint:** `GET /api/blog/:postId/comments`

**Description:** Retrieve all comments for a specific blog post

**Access:** Public

**URL Parameters:**
- `postId` - MongoDB ObjectId of the blog post

**Example:** `GET /api/blog/507f1f77bcf86cd799439011/comments`

**Success Response (200):**
```json
[
  {
    "_id": "607f1f77bcf86cd799439022",
    "body": "Great post!",
    "author": {
      "_id": "507f191e810c19729de860eb",
      "username": "janedoe",
      "email": "janedoe@example.com"
    },
    "post": "507f1f77bcf86cd799439011",
    "createdAt": "2025-11-19T11:00:00.000Z",
    "updatedAt": "2025-11-19T11:00:00.000Z"
  }
]
```

**Error Responses:**
- `500 Internal Server Error` - Server error

---

#### 14. Create Comment

**Endpoint:** `POST /api/blog/:postId/comments`

**Description:** Add a comment to a blog post

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**URL Parameters:**
- `postId` - MongoDB ObjectId of the blog post

**Request Body:**
```json
{
  "body": "string (required, comment text)"
}
```

**Example Request:**
```json
{
  "body": "This is a great article! Thanks for sharing."
}
```

**Success Response (201):**
```json
{
  "_id": "607f1f77bcf86cd799439022",
  "body": "This is a great article! Thanks for sharing.",
  "author": {
    "_id": "507f191e810c19729de860ea",
    "username": "johndoe",
    "email": "johndoe@example.com"
  },
  "post": "507f1f77bcf86cd799439011",
  "createdAt": "2025-11-19T11:00:00.000Z",
  "updatedAt": "2025-11-19T11:00:00.000Z"
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `404 Not Found` - Blog post not found
- `400 Bad Request` - Invalid data
- `500 Internal Server Error` - Server error

---

### Contact

#### 15. Submit Contact Form

**Endpoint:** `POST /api/contact`

**Description:** Submit a contact form message

**Access:** Public

**Request Body:**
```json
{
  "name": "string (required)",
  "email": "string (required, valid email format)",
  "message": "string (required)"
}
```

**Example Request:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "message": "I would like to discuss a potential collaboration."
}
```

**Success Response (201):**
```json
{
  "message": "Message sent successfully",
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john@example.com",
    "message": "I would like to discuss a potential collaboration.",
    "createdAt": "2025-11-19T10:30:00.000Z",
    "updatedAt": "2025-11-19T10:30:00.000Z"
  }
}
```

**Error Responses:**
- `400 Bad Request` - Invalid data
- `500 Internal Server Error` - Server error

---

#### 16. Get All Contact Messages

**Endpoint:** `GET /api/contact`

**Description:** Retrieve all contact form submissions (admin only)

**Access:** Protected (JWT Token Required)

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
```

**Success Response (200):**
```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john@example.com",
    "message": "I would like to discuss a potential collaboration.",
    "createdAt": "2025-11-19T10:30:00.000Z",
    "updatedAt": "2025-11-19T10:30:00.000Z"
  }
]
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `500 Internal Server Error` - Server error

---

## Authentication

This API uses JWT (JSON Web Tokens) for authentication. Protected routes require a valid JWT token to be included in the request headers.

### Getting a Token

1. Register a new user or login with existing credentials
2. The API will return a JWT token in the response
3. Include this token in subsequent requests to protected endpoints

### Using the Token

Include the token in the `Authorization` header with the `Bearer` scheme:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Token Expiration

Tokens are valid for 30 days from the time of issuance. After expiration, users must login again to obtain a new token.

## Error Handling

The API uses standard HTTP status codes and returns error responses in JSON format:

```json
{
  "message": "Error description"
}
```

### Common Status Codes

| Status Code | Meaning |
|-------------|---------|
| `200` | OK - Request successful |
| `201` | Created - Resource created successfully |
| `400` | Bad Request - Invalid request data |
| `401` | Unauthorized - Missing or invalid authentication |
| `404` | Not Found - Resource not found |
| `500` | Internal Server Error - Server error |

## Database Models

### User Model
```javascript
{
  username: String (required, unique),
  email: String (required, unique),
  password: String (required, hashed),
  createdAt: Date,
  updatedAt: Date
}
```

### Project Model
```javascript
{
  title: String (required),
  description: String (required),
  imageUrl: String,
  repoUrl: String,
  liveUrl: String,
  user: ObjectId (ref: User),
  createdAt: Date,
  updatedAt: Date
}
```

### BlogPost Model
```javascript
{
  title: String (required),
  content: String (required),
  author: ObjectId (ref: User),
  comments: [ObjectId] (ref: Comment),
  createdAt: Date,
  updatedAt: Date
}
```

### Comment Model
```javascript
{
  body: String (required),
  author: ObjectId (ref: User),
  post: ObjectId (ref: BlogPost),
  createdAt: Date,
  updatedAt: Date
}
```

### Contact Model
```javascript
{
  name: String (required),
  email: String (required),
  message: String (required),
  createdAt: Date,
  updatedAt: Date
}
```

## Project Structure

```
backend/
├── config/
│   └── db.js                 # Database connection
├── controllers/
│   ├── userController.js     # User authentication logic
│   ├── projectController.js  # Project CRUD operations
│   ├── blogController.js     # Blog CRUD operations
│   ├── commentController.js  # Comment operations
│   └── contactController.js  # Contact form operations
├── middleware/
│   ├── authMiddleware.js     # JWT verification
│   └── errorMiddleware.js    # Error handling
├── models/
│   ├── User.js              # User schema
│   ├── Project.js           # Project schema
│   ├── BlogPost.js          # Blog post schema
│   ├── Comment.js           # Comment schema
│   └── Contact.js           # Contact schema
├── routes/
│   ├── userRoutes.js        # User routes
│   ├── projectRoutes.js     # Project routes
│   ├── blogRoutes.js        # Blog routes
│   └── contactRoutes.js     # Contact routes
├── utils/
│   └── generateToken.js     # JWT token generation utility
├── .env                     # Environment variables
├── .gitignore              # Git ignore file
├── package.json            # NPM dependencies
└── server.js               # Application entry point
```

## Testing

### Testing with cURL

**Register a user:**
```bash
curl -X POST http://localhost:4000/api/users/register \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","password":"123456"}'
```

**Login:**
```bash
curl -X POST http://localhost:4000/api/users/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"123456"}'
```

**Create a project (with token):**
```bash
curl -X POST http://localhost:4000/api/projects \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{"title":"Test Project","description":"A test project"}'
```

### Testing with Postman

1. Import the API endpoints into Postman
2. Set the base URL to `http://localhost:4000`
3. For protected routes, add the JWT token to the Authorization header
4. Set Authorization Type to "Bearer Token"

## Security Features

- **Password Hashing**: Passwords are hashed using bcrypt (10 salt rounds)
- **JWT Authentication**: Secure token-based authentication
- **HTTP Security Headers**: Helmet middleware for secure headers
- **CORS**: Cross-Origin Resource Sharing enabled
- **Environment Variables**: Sensitive data stored in .env file
- **Input Validation**: Request data validation before processing

## Deployment

### Deploying to Render/Railway/Heroku

1. Create a new web service on your platform of choice
2. Connect your GitHub repository
3. Set environment variables:
   - `MONGO_URI`: Your MongoDB Atlas connection string
   - `JWT_SECRET`: A secure random string
   - `NODE_ENV`: `production`
   - `PORT`: `4000` (or platform default)
4. Deploy the application

### MongoDB Atlas Setup

1. Create a MongoDB Atlas account
2. Create a new cluster (free tier available)
3. Create a database user
4. Whitelist all IPs (0.0.0.0/0) or your server IP
5. Get the connection string and add to `MONGO_URI`

## License

This project is open source and available under the MIT License.

## Support

For issues or questions, please create an issue in the repository.

---

**Last Updated:** 2025-11-19
**API Version:** 1.0.0
