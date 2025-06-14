# PlacementPro Database Schema Design

## Overview

PlacementPro employs a multi-database approach to optimize for different data access patterns:

- **PostgreSQL**: Primary relational database for structured data
- **MongoDB**: Document storage for unstructured content
- **Redis**: Caching and session management
- **Elasticsearch**: Search functionality

## Core Entities

### User

Central entity representing all users in the system with role-based distinctions.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  role VARCHAR(20) NOT NULL CHECK (role IN ('STUDENT', 'COLLEGE_ADMIN', 'COMPANY_RECRUITER', 'SYSTEM_ADMIN')),
  phone VARCHAR(20),
  profile_picture_url VARCHAR(255),
  account_status VARCHAR(20) DEFAULT 'ACTIVE' CHECK (account_status IN ('ACTIVE', 'INACTIVE', 'SUSPENDED', 'DELETED')),
  email_verified BOOLEAN DEFAULT FALSE,
  phone_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  last_login_at TIMESTAMP WITH TIME ZONE
);
```

### Student Profile

```sql
CREATE TABLE student_profiles (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id),
  college_id UUID NOT NULL REFERENCES colleges(id),
  enrollment_number VARCHAR(50) UNIQUE,
  degree VARCHAR(100),
  department VARCHAR(100),
  graduation_year INTEGER,
  cgpa DECIMAL(4,2),
  date_of_birth DATE,
  linkedin_url VARCHAR(255),
  github_url VARCHAR(255),
  portfolio_url VARCHAR(255),
  bio TEXT,
  skills TEXT[],
  placement_status VARCHAR(20) DEFAULT 'NOT_PLACED' CHECK (placement_status IN ('NOT_PLACED', 'PLACED', 'OFFER_RECEIVED')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### College

```sql
CREATE TABLE colleges (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  code VARCHAR(50) UNIQUE,
  address TEXT,
  city VARCHAR(100),
  state VARCHAR(100),
  country VARCHAR(100),
  pincode VARCHAR(20),
  website VARCHAR(255),
  phone VARCHAR(20),
  email VARCHAR(255),
  established_year INTEGER,
  accreditation VARCHAR(100),
  description TEXT,
  logo_url VARCHAR(255),
  subscription_plan VARCHAR(50),
  subscription_status VARCHAR(20),
  subscription_start_date DATE,
  subscription_end_date DATE,
  student_limit INTEGER,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### Company

```sql
CREATE TABLE companies (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  industry VARCHAR(100),
  address TEXT,
  city VARCHAR(100),
  state VARCHAR(100),
  country VARCHAR(100),
  pincode VARCHAR(20),
  website VARCHAR(255),
  phone VARCHAR(20),
  email VARCHAR(255),
  description TEXT,
  logo_url VARCHAR(255),
  employee_count INTEGER,
  established_year INTEGER,
  subscription_plan VARCHAR(50),
  subscription_status VARCHAR(20),
  subscription_start_date DATE,
  subscription_end_date DATE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### Job Posting

```sql
CREATE TABLE job_postings (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL REFERENCES companies(id),
  title VARCHAR(255) NOT NULL,
  description TEXT NOT NULL,
  job_type VARCHAR(50) CHECK (job_type IN ('FULL_TIME', 'PART_TIME', 'INTERNSHIP', 'CONTRACT')),
  location VARCHAR(255),
  remote_option BOOLEAN DEFAULT FALSE,
  department VARCHAR(100),
  salary_min INTEGER,
  salary_max INTEGER,
  salary_currency VARCHAR(3) DEFAULT 'INR',
  qualifications TEXT,
  responsibilities TEXT,
  required_skills TEXT[],
  preferred_skills TEXT[],
  experience_min INTEGER,
  experience_max INTEGER,
  education_level VARCHAR(100),
  posting_date DATE NOT NULL DEFAULT CURRENT_DATE,
  application_deadline DATE,
  status VARCHAR(20) DEFAULT 'ACTIVE' CHECK (status IN ('DRAFT', 'ACTIVE', 'CLOSED', 'FILLED')),
  is_featured BOOLEAN DEFAULT FALSE,
  vacancy_count INTEGER DEFAULT 1,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### Job Application

```sql
CREATE TABLE job_applications (
  id UUID PRIMARY KEY,
  job_posting_id UUID NOT NULL REFERENCES job_postings(id),
  student_id UUID NOT NULL REFERENCES student_profiles(id),
  resume_id UUID,
  cover_letter TEXT,
  application_date TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  status VARCHAR(20) DEFAULT 'APPLIED' CHECK (status IN ('APPLIED', 'REVIEWED', 'SHORTLISTED', 'REJECTED', 'INTERVIEW_SCHEDULED', 'OFFERED', 'ACCEPTED', 'DECLINED')),
  rejection_reason TEXT,
  notes TEXT,
  modified_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE UNIQUE INDEX unique_application_idx ON job_applications(job_posting_id, student_id);
```

### Interview Schedule

```sql
CREATE TABLE interview_schedules (
  id UUID PRIMARY KEY,
  application_id UUID NOT NULL REFERENCES job_applications(id),
  interview_type VARCHAR(50) CHECK (interview_type IN ('PHONE', 'VIDEO', 'IN_PERSON', 'ASSESSMENT', 'GROUP')),
  scheduled_start TIMESTAMP WITH TIME ZONE NOT NULL,
  scheduled_end TIMESTAMP WITH TIME ZONE NOT NULL,
  location TEXT,
  meeting_link VARCHAR(255),
  interviewer_name VARCHAR(255),
  interviewer_email VARCHAR(255),
  interviewer_designation VARCHAR(100),
  status VARCHAR(20) DEFAULT 'SCHEDULED' CHECK (status IN ('SCHEDULED', 'COMPLETED', 'CANCELLED', 'RESCHEDULED')),
  feedback TEXT,
  rating INTEGER CHECK (rating BETWEEN 1 AND 5),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```