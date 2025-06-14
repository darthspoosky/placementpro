# PlacementPro API Specifications

This document outlines the primary REST endpoints for the PlacementPro platform. Each endpoint uses JSON for both requests and responses.

## Authentication

### POST `/api/v1/auth/login`
Authenticate a user and receive a JWT token.

**Request**
```json
{
  "email": "user@example.com",
  "password": "your-password"
}
```

**Response**
```json
{
  "token": "<jwt-token>"
}
```

### POST `/api/v1/auth/register`
Create a new user account.

**Request**
```json
{
  "email": "user@example.com",
  "password": "your-password",
  "firstName": "Jane",
  "lastName": "Doe"
}
```

**Response**
```json
{
  "id": "user-id",
  "email": "user@example.com"
}
```

## Students

### GET `/api/v1/students`
Retrieve a list of student profiles. Supports pagination with `page` and `size` query parameters.

### GET `/api/v1/students/{id}`
Retrieve details for a single student by ID.

## Jobs

### GET `/api/v1/jobs`
List all active job postings with optional filtering by location or job type.

### POST `/api/v1/jobs/{id}/apply`
Submit an application for a specific job posting.

**Request**
```json
{
  "studentId": "student-id",
  "resumeUrl": "https://.../resume.pdf",
  "coverLetter": "..."
}
```

**Response**
```json
{
  "applicationId": "application-id",
  "status": "APPLIED"
}
```

Additional endpoints and detailed error codes will be defined as the implementation progresses.
