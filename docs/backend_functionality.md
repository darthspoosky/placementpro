# PlacementPro Backend Functionality

This document outlines the backend functionality required to support each screen in the PlacementPro platform.

## Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [API Services](#api-services)
3. [Screen-specific Backend Requirements](#screen-specific-backend-requirements)
4. [Database Interactions](#database-interactions)
5. [External Integrations](#external-integrations)

## Architecture Overview

PlacementPro follows a microservices architecture with the following core services:

### Core Services
- **Authentication Service**: Manages user authentication, authorization, and session management
- **User Service**: Handles user profiles and account management
- **Student Service**: Manages student-specific functionality
- **College Service**: Handles college administration features
- **Company Service**: Manages company recruiters and job postings
- **Job Service**: Handles job listings and applications
- **Notification Service**: Manages all system notifications
- **Analytics Service**: Processes and serves analytics data
- **Search Service**: Provides search functionality across the platform
- **File Service**: Manages file uploads and media storage

### Communication Patterns
- **Synchronous**: REST APIs for direct client-server communication
- **Asynchronous**: Kafka/RabbitMQ for event-driven inter-service communication
- **WebSockets**: For real-time features like notifications and chat

## API Services

Each service exposes its own REST API endpoints:

| Service | Base Endpoint | Primary Responsibilities |
|---------|--------------|--------------------------|
| Authentication | `/api/v1/auth` | Login, registration, password reset, token management |
| User | `/api/v1/users` | Profile management, account settings |
| Student | `/api/v1/students` | Student profiles, skills, career tools |
| College | `/api/v1/colleges` | College profiles, admin features, analytics |
| Company | `/api/v1/companies` | Company profiles, recruiter management |
| Job | `/api/v1/jobs` | Job postings, applications, interviews |
| Notification | `/api/v1/notifications` | User notifications, preferences |
| Analytics | `/api/v1/analytics` | Reports, dashboards, metrics |
| Search | `/api/v1/search` | Global search functionality |
| File | `/api/v1/files` | File uploads, media management |

## Screen-specific Backend Requirements

### Public/Guest Screens

#### Landing Page (/)
- **API Endpoints**:
  - `GET /api/v1/content/homepage` - Retrieve homepage content
  - `GET /api/v1/testimonials` - Fetch success stories
- **Services**: Content Service
- **Database Tables**: `content_blocks`, `testimonials`

#### Pricing Page (/pricing)
- **API Endpoints**:
  - `GET /api/v1/pricing/plans` - Retrieve pricing tiers and features
  - `GET /api/v1/pricing/comparisons` - Feature comparison data
- **Services**: Pricing Service
- **Database Tables**: `subscription_plans`, `plan_features`

#### Demo Booking (/demo)
- **API Endpoints**:
  - `GET /api/v1/booking/available-slots` - Get available demo slots
  - `POST /api/v1/booking/schedule` - Book a demonstration
- **Services**: Booking Service, Notification Service
- **Database Tables**: `demo_bookings`, `availability_slots`
- **External Integrations**: Calendar API (Google/Microsoft), Email Service

### Authentication Screens

#### Login Page (/login)
- **API Endpoints**:
  - `POST /api/v1/auth/login` - Authenticate user and return JWT
  - `POST /api/v1/auth/social/{provider}` - Social login (Google, LinkedIn)
- **Services**: Authentication Service
- **Database Tables**: `users`, `auth_sessions`
- **External Integrations**: OAuth providers

#### Registration (/register)
- **API Endpoints**:
  - `POST /api/v1/auth/register` - Create new user account
  - `POST /api/v1/auth/verify-email` - Verify email address
- **Services**: Authentication Service, User Service, Notification Service
- **Database Tables**: `users`, `verification_tokens`
- **External Integrations**: Email Service

### Student Screens

#### Student Dashboard (/student/dashboard)
- **API Endpoints**:
  - `GET /api/v1/students/{id}/dashboard` - Retrieve dashboard data
  - `GET /api/v1/students/{id}/upcoming-events` - Get upcoming events
  - `GET /api/v1/students/{id}/applications/stats` - Application statistics
- **Services**: Student Service, Job Service, Analytics Service
- **Database Tables**: `student_profiles`, `job_applications`, `events`

#### Job Discovery (/student/jobs)
- **API Endpoints**:
  - `GET /api/v1/jobs` - List jobs with filtering
  - `GET /api/v1/jobs/recommended` - AI-powered job recommendations
  - `POST /api/v1/jobs/{id}/save` - Save job to favorites
- **Services**: Job Service, Search Service
- **Database Tables**: `job_postings`, `saved_jobs`, `job_skills`
- **External Integrations**: Elasticsearch for advanced search

#### Job Application (/student/jobs/{id}/apply)
- **API Endpoints**:
  - `GET /api/v1/jobs/{id}` - Job details
  - `GET /api/v1/students/{id}/resumes` - List student resumes
  - `POST /api/v1/jobs/{id}/apply` - Submit job application
- **Services**: Job Service, File Service, Notification Service
- **Database Tables**: `job_applications`, `resumes`, `cover_letters`
- **External Integrations**: S3 for file storage

#### Resume Builder (/student/resumes)
- **API Endpoints**:
  - `GET /api/v1/students/{id}/resumes` - List student resumes
  - `POST /api/v1/students/{id}/resumes` - Create new resume
  - `PUT /api/v1/students/{id}/resumes/{resumeId}` - Update resume
  - `DELETE /api/v1/students/{id}/resumes/{resumeId}` - Delete resume
- **Services**: Student Service, File Service
- **Database Tables**: `resumes`, `resume_sections`, `resume_templates`
- **External Integrations**: PDF Generation Service

#### Interview Preparation (/student/interview-prep)
- **API Endpoints**:
  - `GET /api/v1/interview-prep/questions` - Get practice questions
  - `GET /api/v1/students/{id}/interviews` - Get scheduled interviews
  - `POST /api/v1/interview-prep/practice-sessions` - Record practice session
- **Services**: Interview Service, AI Service
- **Database Tables**: `interview_questions`, `practice_sessions`, `interview_schedules`
- **External Integrations**: Video Recording Service, Speech Analysis API

### College Admin Screens

#### Admin Dashboard (/admin/dashboard)
- **API Endpoints**:
  - `GET /api/v1/colleges/{id}/dashboard` - Retrieve dashboard metrics
  - `GET /api/v1/colleges/{id}/placement-stats` - Get placement statistics
  - `GET /api/v1/colleges/{id}/activities` - Recent activities
- **Services**: College Service, Analytics Service
- **Database Tables**: `colleges`, `placement_statistics`, `activity_logs`

#### Student Management (/admin/students)
- **API Endpoints**:
  - `GET /api/v1/colleges/{id}/students` - List college students
  - `GET /api/v1/colleges/{id}/students/{studentId}` - Student details
  - `POST /api/v1/colleges/{id}/students/import` - Bulk import students
  - `PUT /api/v1/colleges/{id}/students/{studentId}` - Update student
- **Services**: College Service, Student Service, File Service
- **Database Tables**: `student_profiles`, `college_departments`
- **External Integrations**: Excel/CSV Parser

#### Company Relations (/admin/companies)
- **API Endpoints**:
  - `GET /api/v1/colleges/{id}/companies` - List partner companies
  - `POST /api/v1/colleges/{id}/company-relations` - Create partnership
  - `GET /api/v1/colleges/{id}/recruitment-calendar` - View recruitment events
- **Services**: College Service, Company Service, Calendar Service
- **Database Tables**: `company_relations`, `recruitment_events`, `companies`

#### Job Postings Management (/admin/jobs)
- **API Endpoints**:
  - `GET /api/v1/colleges/{id}/jobs` - List jobs for college
  - `PUT /api/v1/colleges/{id}/jobs/{jobId}/approve` - Approve job posting
  - `PUT /api/v1/colleges/{id}/jobs/{jobId}/feature` - Feature job posting
- **Services**: College Service, Job Service
- **Database Tables**: `job_postings`, `featured_jobs`, `job_approvals`

### Company Recruiter Screens

#### Company Dashboard (/company/dashboard)
- **API Endpoints**:
  - `GET /api/v1/companies/{id}/dashboard` - Dashboard metrics
  - `GET /api/v1/companies/{id}/hiring-stats` - Hiring statistics
  - `GET /api/v1/companies/{id}/recent-applications` - Recent applications
- **Services**: Company Service, Analytics Service
- **Database Tables**: `companies`, `recruitment_metrics`, `job_applications`

#### Job Creation (/company/jobs/create)
- **API Endpoints**:
  - `GET /api/v1/job-templates` - Get job description templates
  - `POST /api/v1/companies/{id}/jobs` - Create job posting
  - `PUT /api/v1/companies/{id}/jobs/{jobId}` - Update job posting
- **Services**: Job Service, Company Service
- **Database Tables**: `job_postings`, `job_templates`, `job_skills`

#### Candidate Database (/company/candidates)
- **API Endpoints**:
  - `GET /api/v1/companies/{id}/candidates` - Search student profiles
  - `GET /api/v1/companies/{id}/candidates/{studentId}` - View student profile
  - `POST /api/v1/companies/{id}/talent-pools` - Create talent pool
- **Services**: Company Service, Student Service, Search Service
- **Database Tables**: `student_profiles`, `talent_pools`, `company_candidate_notes`
- **External Integrations**: Elasticsearch for advanced candidate search

#### Interview Scheduling (/company/interviews)
- **API Endpoints**:
  - `GET /api/v1/companies/{id}/interviews` - List scheduled interviews
  - `POST /api/v1/companies/{id}/interviews` - Schedule interview
  - `PUT /api/v1/companies/{id}/interviews/{interviewId}` - Update interview
- **Services**: Interview Service, Calendar Service, Notification Service
- **Database Tables**: `interview_schedules`, `interview_rooms`, `interview_feedback`
- **External Integrations**: Calendar API, Video Conference API

## Database Interactions

### Data Access Patterns

Each service follows repository pattern for database access:

```typescript
// Example Repository Pattern for Student Service
class StudentRepository {
  async findById(id: string): Promise<StudentProfile> {
    // Access database
  }
  
  async updateProfile(id: string, data: Partial<StudentProfile>): Promise<StudentProfile> {
    // Update database
  }
  
  async listByCollege(collegeId: string, filters: QueryFilters): Promise<PaginatedResult<StudentProfile>> {
    // Query database with filters and pagination
  }
}
```

### Database Transactions

For operations that modify multiple tables:

```typescript
// Example transaction for job application
async function submitApplication(applicationData) {
  return await db.transaction(async (trx) => {
    // 1. Create application record
    const application = await trx('job_applications').insert({...});
    
    // 2. Update job vacancy count
    await trx('job_postings')
      .where({ id: applicationData.jobId })
      .decrement('vacancy_count', 1);
      
    // 3. Create activity log
    await trx('activity_logs').insert({...});
    
    return application;
  });
}
```

## External Integrations

### Email Service
- Uses AWS SES or SendGrid
- Template-based emails for consistency
- Handles transactional and marketing emails

### File Storage
- AWS S3 for document storage
- Image optimization pipeline for profile pictures
- Virus scanning for uploaded resumes

### Calendar Integration
- Google Calendar API for interview scheduling
- Microsoft Outlook API as alternative
- iCalendar file generation for cross-platform compatibility

### Video Conferencing
- Zoom API for interview sessions
- Recording capabilities for interview reviews
- Custom branded virtual interview rooms

### Payment Processing
- Stripe for subscription payments
- Invoice generation
- Payment reminder automation

### AI Services
- Resume parsing and analysis
- Job-student matching algorithm
- Interview question recommendation
- Sentiment analysis for feedback

### Analytics
- Self-hosted Matomo for privacy-focused analytics
- Custom event tracking
- Anonymized user journey mapping
- A/B testing framework

### Mobile Push Notifications
- Firebase Cloud Messaging for Android
- Apple Push Notification Service for iOS
- Notification grouping and prioritization

This comprehensive backend functionality documentation provides the technical foundation to implement all the user screens in the PlacementPro platform, ensuring a robust and scalable solution that addresses the requirements of all user types.
