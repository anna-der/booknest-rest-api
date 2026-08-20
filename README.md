# BookNest REST API

## 1. Project Overview

**BookNest** is an online book catalogue that provides a RESTful API for managing and browsing books, authors, and categories.

The API allows users to search and filter books, view detailed information, and manage books depending on their permissions.

The API uses REST principles and JSON format for requests and responses.

---

## 2. Functional Requirements

The API must allow users to:

* view a list of books;
* view information about a specific book;
* search books by title;
* filter books by category and author;
* sort books by title, price, or publication date;
* use pagination when viewing large collections;
* view authors;
* view categories;
* create new books;
* update existing books;
* delete books;
* authenticate users;
* restrict book management operations to administrators.

---

## 3. Non-Functional Requirements

* The API must use HTTPS.
* The API must return data in JSON format.
* The API must use standard HTTP methods.
* The API must use versioning: `/api/v1`.
* Large collections must support pagination.
* GET requests must support caching.
* Authentication must use JWT.
* Errors must have a consistent JSON structure.
* The API should provide reasonable response times.
* Passwords must never be returned by the API.

---

# 4. Data Model

## Book

| Field             | Type    | Description            |
| ----------------- | ------- | ---------------------- |
| `id`              | integer | Unique book identifier |
| `title`           | string  | Book title             |
| `description`     | string  | Book description       |
| `authorId`        | integer | Author identifier      |
| `categoryId`      | integer | Category identifier    |
| `price`           | number  | Book price             |
| `publicationDate` | date    | Publication date       |
| `coverUrl`        | string  | URL of the book cover  |

Example:

```json
{
  "id": 1,
  "title": "Clean Code",
  "description": "A book about writing clean and maintainable code.",
  "authorId": 1,
  "categoryId": 3,
  "price": 29.99,
  "publicationDate": "2008-08-01",
  "coverUrl": "https://example.com/covers/clean-code.jpg"
}
```

## Author

| Field  | Type    | Description              |
| ------ | ------- | ------------------------ |
| `id`   | integer | Unique author identifier |
| `name` | string  | Author name              |
| `bio`  | string  | Author biography         |

## Category

| Field  | Type    | Description                |
| ------ | ------- | -------------------------- |
| `id`   | integer | Unique category identifier |
| `name` | string  | Category name              |

## User

| Field   | Type    | Description            |
| ------- | ------- | ---------------------- |
| `id`    | integer | Unique user identifier |
| `name`  | string  | User name              |
| `email` | string  | User email             |
| `role`  | string  | `user` or `admin`      |

---

# 5. Relationships

* One author can have many books.
* One category can contain many books.
* Each book has one author.
* Each book belongs to one category.

```text
Author 1 -------- * Book * -------- 1 Category
```

---

# 6. REST API

Base URL:

```text
/api/v1
```

## 6.1 Get All Books

```http
GET /api/v1/books
```

Returns a paginated collection of books.

Example:

```http
GET /api/v1/books?page=1&limit=10
```

Response:

```json
{
  "data": [
    {
      "id": 1,
      "title": "Clean Code",
      "authorId": 1,
      "categoryId": 3,
      "price": 29.99
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "totalItems": 25,
    "totalPages": 3
  }
}
```

Status codes:

* `200 OK` — books were successfully returned.
* `400 Bad Request` — invalid pagination or filter parameters.
* `500 Internal Server Error` — unexpected server error.

---

## 6.2 Get a Book by ID

```http
GET /api/v1/books/{id}
```

Example:

```http
GET /api/v1/books/1
```

Response:

```json
{
  "id": 1,
  "title": "Clean Code",
  "description": "A book about clean code.",
  "authorId": 1,
  "categoryId": 3,
  "price": 29.99,
  "_links": {
    "self": {
      "href": "/api/v1/books/1"
    },
    "author": {
      "href": "/api/v1/authors/1"
    },
    "category": {
      "href": "/api/v1/categories/3"
    }
  }
}
```

Status codes:

* `200 OK` — book was found.
* `404 Not Found` — book does not exist.
* `500 Internal Server Error` — server error.

---

## 6.3 Create a Book

```http
POST /api/v1/books
```

This operation requires an administrator JWT token.

Request:

```json
{
  "title": "Clean Code",
  "description": "A book about clean code.",
  "authorId": 1,
  "categoryId": 3,
  "price": 29.99,
  "publicationDate": "2008-08-01",
  "coverUrl": "https://example.com/cover.jpg"
}
```

Status codes:

* `201 Created` — book was successfully created.
* `400 Bad Request` — invalid request data.
* `401 Unauthorized` — authentication is required.
* `403 Forbidden` — user does not have administrator permissions.
* `409 Conflict` — conflicting book data.
* `500 Internal Server Error` — server error.

---

## 6.4 Update a Book

```http
PUT /api/v1/books/{id}
```

This operation requires administrator permissions.

Status codes:

* `200 OK` — book was successfully updated.
* `400 Bad Request` — invalid data.
* `401 Unauthorized` — authentication is required.
* `403 Forbidden` — user does not have sufficient permissions.
* `404 Not Found` — book does not exist.
* `500 Internal Server Error` — server error.

---

## 6.5 Delete a Book

```http
DELETE /api/v1/books/{id}
```

This operation requires administrator permissions.

Status codes:

* `204 No Content` — book was successfully deleted.
* `401 Unauthorized` — authentication is required.
* `403 Forbidden` — user does not have sufficient permissions.
* `404 Not Found` — book does not exist.
* `500 Internal Server Error` — server error.

---

# 7. Authors

## Get Authors

```http
GET /api/v1/authors?page=1&limit=20
```

Returns a paginated list of authors.

Status codes:

* `200 OK` — authors were successfully returned.
* `400 Bad Request` — invalid pagination parameters.
* `500 Internal Server Error` — server error.

## Get an Author

```http
GET /api/v1/authors/{id}
```

Status codes:

* `200 OK` — author was found.
* `404 Not Found` — author does not exist.
* `500 Internal Server Error` — server error.

---

# 8. Categories

## Get Categories

```http
GET /api/v1/categories?page=1&limit=20
```

Returns a paginated list of categories.

Status codes:

* `200 OK` — categories were successfully returned.
* `400 Bad Request` — invalid pagination parameters.
* `500 Internal Server Error` — server error.

## Get a Category

```http
GET /api/v1/categories/{id}
```

Status codes:

* `200 OK` — category was found.
* `404 Not Found` — category does not exist.
* `500 Internal Server Error` — server error.

---

# 9. Searching, Filtering and Sorting

The `/books` collection supports searching, filtering and sorting.

### Search by title

```http
GET /api/v1/books?search=clean
```

### Filter by category

```http
GET /api/v1/books?categoryId=3
```

### Filter by author

```http
GET /api/v1/books?authorId=1
```

### Sort books by price

```http
GET /api/v1/books?sort=price&order=asc
```

Supported sorting fields:

* `title`
* `price`
* `publicationDate`

Several parameters can be combined:

```http
GET /api/v1/books?categoryId=3&search=clean&page=1&limit=10
```

---

# 10. Pagination

All collection endpoints that can return a large amount of data use pagination.

The API uses the following parameters:

* `page` — page number;
* `limit` — number of items per page.

Example:

```http
GET /api/v1/books?page=2&limit=10
```

The maximum value of `limit` is **100**.

Example response:

```json
{
  "data": [],
  "pagination": {
    "page": 2,
    "limit": 10,
    "totalItems": 56,
    "totalPages": 6
  }
}
```

Pagination is used for:

* books;
* authors;
* categories.

---

# 11. Authentication and Authorization

The API uses **JWT (JSON Web Token)** authentication.

## Login

```http
POST /api/v1/auth/login
```

Request:

```json
{
  "email": "admin@example.com",
  "password": "password"
}
```

Response:

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "tokenType": "Bearer",
  "expiresIn": 3600
}
```

Protected requests must contain the following header:

```http
Authorization: Bearer <accessToken>
```

There are two roles:

* `user` — can view books, authors and categories;
* `admin` — can view and manage books.

Authentication errors:

* `401 Unauthorized` — token is missing, invalid or expired.
* `403 Forbidden` — user is authenticated but does not have sufficient permissions.

---

# 12. Error Handling

All API errors use the same JSON structure:

```json
{
  "error": {
    "code": "BOOK_NOT_FOUND",
    "message": "Book with id 15 was not found"
  }
}
```

## 400 Bad Request

Used when the request contains invalid parameters or data.

```json
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "The page parameter must be a positive integer"
  }
}
```

## 401 Unauthorized

Used when authentication is required but the user is not authenticated.

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication is required"
  }
}
```

## 403 Forbidden

Used when the user is authenticated but does not have the required permissions.

```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Administrator permissions are required"
  }
}
```

## 404 Not Found

Used when the requested resource does not exist.

```json
{
  "error": {
    "code": "BOOK_NOT_FOUND",
    "message": "Book with id 15 was not found"
  }
}
```

## 409 Conflict

Used when the request conflicts with existing data.

## 500 Internal Server Error

Used for unexpected errors on the server.

---

# 13. HTTP Status Codes

| Status Code                 | Description                               |
| --------------------------- | ----------------------------------------- |
| `200 OK`                    | Request was successful                    |
| `201 Created`               | Resource was successfully created         |
| `204 No Content`            | Resource was successfully deleted         |
| `400 Bad Request`           | Request contains invalid data             |
| `401 Unauthorized`          | Authentication is required or invalid     |
| `403 Forbidden`             | User does not have sufficient permissions |
| `404 Not Found`             | Requested resource does not exist         |
| `409 Conflict`              | Request conflicts with existing data      |
| `500 Internal Server Error` | Unexpected server error                   |

---

# 14. Caching

GET requests are cacheable because they do not modify server data.

The following endpoints support caching:

```text
GET /api/v1/books
GET /api/v1/books/{id}
GET /api/v1/authors
GET /api/v1/authors/{id}
GET /api/v1/categories
GET /api/v1/categories/{id}
```

Example headers:

```http
Cache-Control: public, max-age=300
ETag: "book-1-v2"
```

The cache lifetime for catalogue data is **5 minutes**.

`POST`, `PUT` and `DELETE` requests are not cached because they modify data.

When a book is updated or deleted, its cached representation must be invalidated.

---

# 15. Richardson Maturity Model

The BookNest API follows **Level 3 of the Richardson Maturity Model**.

## Level 1 — Resources

The API uses separate URIs for different resources:

```text
/api/v1/books
/api/v1/books/1
/api/v1/authors
/api/v1/categories
```

## Level 2 — HTTP Methods

The API uses HTTP methods according to their purpose:

* `GET` — retrieve resources;
* `POST` — create resources;
* `PUT` — update resources;
* `DELETE` — delete resources.

## Level 3 — HATEOAS

API responses contain links to related resources.

Example:

```json
{
  "id": 1,
  "title": "Clean Code",
  "_links": {
    "self": {
      "href": "/api/v1/books/1"
    },
    "author": {
      "href": "/api/v1/authors/1"
    },
    "category": {
      "href": "/api/v1/categories/3"
    }
  }
}
```

HATEOAS allows the client to discover related resources through links provided by the API.

---

# 16. API Summary

| Method | Endpoint                  | Authentication | Description       |
| ------ | ------------------------- | -------------- | ----------------- |
| GET    | `/api/v1/books`           | No             | Get books         |
| GET    | `/api/v1/books/{id}`      | No             | Get a book        |
| POST   | `/api/v1/books`           | Admin          | Create a book     |
| PUT    | `/api/v1/books/{id}`      | Admin          | Update a book     |
| DELETE | `/api/v1/books/{id}`      | Admin          | Delete a book     |
| GET    | `/api/v1/authors`         | No             | Get authors       |
| GET    | `/api/v1/authors/{id}`    | No             | Get an author     |
| GET    | `/api/v1/categories`      | No             | Get categories    |
| GET    | `/api/v1/categories/{id}` | No             | Get a category    |
| POST   | `/api/v1/auth/login`      | No             | Authenticate user |

---

# 17. Conclusion

BookNest provides a RESTful API for an online book catalogue.

The API includes:

* functional and non-functional requirements;
* a complete data model;
* CRUD operations for books;
* authors and categories;
* searching and filtering;
* pagination;
* JWT authentication and authorization;
* meaningful HTTP status codes;
* consistent error handling;
* caching;
* HATEOAS and Richardson Maturity Model Level 3.

This design provides a clear and scalable foundation for implementing the BookNest online book catalogue.
