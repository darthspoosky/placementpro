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
  non_technical_skills TEXT[],
  soft_skills TEXT[],
  preferred_work_environment TEXT[],
  placement_status VARCHAR(20) DEFAULT 'NOT_PLACED' CHECK (placement_status IN ('NOT_PLACED', 'PLACED', 'OFFER_RECEIVED')),
  device_type VARCHAR(50),
  network_access_level VARCHAR(20) CHECK (network_access_level IN ('CONTINUOUS', 'INTERMITTENT', 'LIMITED')),
  diversity_data JSONB,
  career_counseling_status VARCHAR(20),
  mental_health_status VARCHAR(20),
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
  college_tier INTEGER CHECK (college_tier BETWEEN 1 AND 3),
  resource_level VARCHAR(20) CHECK (resource_level IN ('HIGH', 'MEDIUM', 'LOW')),
  subscription_plan VARCHAR(50),
  subscription_status VARCHAR(20),
  subscription_start_date DATE,
  subscription_end_date DATE,
  student_limit INTEGER,
  connectivity_quality VARCHAR(20) CHECK (connectivity_quality IN ('HIGH', 'MEDIUM', 'LOW')),
  offline_support_needed BOOLEAN DEFAULT FALSE,
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

### Alumni Network

```sql
CREATE TABLE alumni_profiles (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL REFERENCES users(id),
  college_id UUID NOT NULL REFERENCES colleges(id),
  graduation_year INTEGER NOT NULL,
  current_company VARCHAR(255),
  current_position VARCHAR(255),
  industry VARCHAR(100),
  experience_years INTEGER,
  linkedin_url VARCHAR(255),
  willing_to_mentor BOOLEAN DEFAULT FALSE,
  mentorship_areas TEXT[],
  availability VARCHAR(50),
  bio TEXT,
  career_highlights TEXT[],
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE mentorships (
  id UUID PRIMARY KEY,
  mentor_id UUID NOT NULL REFERENCES alumni_profiles(id),
  mentee_id UUID NOT NULL REFERENCES student_profiles(id),
  status VARCHAR(20) CHECK (status IN ('REQUESTED', 'ACTIVE', 'COMPLETED', 'REJECTED')),
  request_date TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  start_date TIMESTAMP WITH TIME ZONE,
  end_date TIMESTAMP WITH TIME ZONE,
  focus_areas TEXT[],
  goals TEXT,
  feedback_mentor TEXT,
  feedback_mentee TEXT,
  meeting_count INTEGER DEFAULT 0,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### Mental Health Support

```sql
CREATE TABLE mental_health_resources (
  id UUID PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  resource_type VARCHAR(50) CHECK (resource_type IN ('ARTICLE', 'VIDEO', 'TOOL', 'EXERCISE', 'CONTACT')),
  content TEXT,
  url VARCHAR(255),
  tags TEXT[],
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE student_wellness_profiles (
  id UUID PRIMARY KEY,
  student_id UUID NOT NULL REFERENCES student_profiles(id),
  stress_levels INTEGER[] DEFAULT '{}',
  confidence_levels INTEGER[] DEFAULT '{}',
  anxiety_triggers TEXT[],
  coping_strategies TEXT[],
  completed_assessments TEXT[],
  resource_recommendations UUID[] REFERENCES mental_health_resources(id),
  last_wellness_check TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
```

### Diversity and Inclusion

```sql
CREATE TABLE diversity_categories (
  id UUID PRIMARY KEY,
  category_name VARCHAR(100) NOT NULL,
  description TEXT,
  privacy_level VARCHAR(20) CHECK (privacy_level IN ('PUBLIC', 'INSTITUTIONAL', 'RESTRICTED')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE diversity_metrics (
  id UUID PRIMARY KEY,
  college_id UUID NOT NULL REFERENCES colleges(id),
  reporting_period VARCHAR(20),
  start_date DATE,
  end_date DATE,
  metrics_data JSONB,
  improvement_percentage DECIMAL(5,2),
  goals JSONB,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
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