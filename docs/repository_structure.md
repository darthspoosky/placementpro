# PlacementPro Repository Structure

This document outlines the repository organization and code structure for the PlacementPro platform.

## Repository Organization

The PlacementPro project is organized into multiple repositories following a microservices architecture:

```
placementpro/
├── placementpro-web/              # Web frontend (React)
├── placementpro-mobile/           # Mobile app (Android/Kotlin)
├── placementpro-services/         # Backend services (parent repository)
│   ├── auth-service/              # Authentication microservice
│   ├── student-service/           # Student management microservice
│   ├── college-service/           # College management microservice
│   ├── company-service/           # Company management microservice
│   ├── payment-service/           # Payment processing microservice
│   ├── analytics-service/         # Analytics and reporting microservice
│   ├── ai-service/                # AI features microservice
│   ├── notification-service/      # Notification microservice
│   └── integration-service/       # Third-party integrations microservice
├── placementpro-infrastructure/   # Infrastructure as code
├── placementpro-docs/             # Project documentation
└── placementpro-config/           # Shared configuration
```

## Web Frontend Structure (React.js)

```
placementpro-web/
├── public/                        # Static files
├── src/
│   ├── assets/                    # Images, fonts, etc.
│   ├── components/                # Reusable UI components
│   │   ├── common/                # Shared components
│   │   ├── layout/                # Layout components
│   │   └── [feature]/             # Feature-specific components
│   ├── hooks/                     # Custom React hooks
│   ├── pages/                     # Page components
│   │   ├── public/                # Public pages (landing, pricing, etc.)
│   │   ├── auth/                  # Authentication pages
│   │   ├── student/               # Student pages
│   │   ├── college/               # College admin pages
│   │   └── company/               # Company pages
│   ├── services/                  # API services
│   ├── store/                     # Redux store setup
│   │   ├── slices/                # Redux slices
│   │   ├── actions/               # Redux actions
│   │   └── selectors/             # Redux selectors
│   ├── styles/                    # Global styles
│   │   ├── theme/                 # Material UI theme configuration
│   │   └── global/                # Global CSS
│   ├── utils/                     # Utility functions
│   ├── App.js                     # Root App component
│   └── index.js                   # Entry point
├── .eslintrc.js                   # ESLint configuration
├── .prettierrc                    # Prettier configuration
├── jsconfig.json                  # JavaScript configuration
├── package.json                   # Dependencies and scripts
└── README.md                      # Repository documentation
```

## Mobile App Structure (Kotlin with Jetpack Compose)

```
placementpro-mobile/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/placementpro/
│   │   │   │   ├── core/
│   │   │   │   │   ├── di/                # Dependency injection
│   │   │   │   │   ├── network/           # Network utilities
│   │   │   │   │   ├── storage/           # Local storage
│   │   │   │   │   └── utils/             # Utility functions
│   │   │   │   ├── data/
│   │   │   │   │   ├── remote/            # Remote data sources
│   │   │   │   │   ├── local/             # Local data sources
│   │   │   │   │   ├── repository/        # Repository implementations
│   │   │   │   │   └── model/             # Data models
│   │   │   │   ├── domain/
│   │   │   │   │   ├── model/             # Domain models
│   │   │   │   │   ├── repository/        # Repository interfaces
│   │   │   │   │   └── usecase/           # Business logic
│   │   │   │   ├── presentation/
│   │   │   │   │   ├── common/            # Common UI components
│   │   │   │   │   └── [feature]/         # Feature-specific UI
│   │   │   │   │       ├── components/    # Feature components
│   │   │   │   │       ├── screens/       # Feature screens
│   │   │   │   │       └── viewmodel/     # Feature ViewModels
│   │   │   │   └── PlacementProApp.kt     # Application class
│   │   │   ├── res/                       # Android resources
│   │   │   └── AndroidManifest.xml        # Android manifest
│   │   └── test/                          # Unit tests
│   ├── build.gradle                       # App-level Gradle
│   └── proguard-rules.pro                 # ProGuard rules
├── build.gradle                           # Project-level Gradle
├── gradle.properties                      # Gradle properties
└── README.md                              # Repository documentation
```

## Microservice Structure (Spring Boot)

Each microservice follows a common structure:

```
[service-name]/
├── src/
│   ├── main/
│   │   ├── java/com/placementpro/[service]/
│   │   │   ├── config/                    # Configuration classes
│   │   │   ├── controller/                # REST controllers
│   │   │   ├── dto/                       # Data Transfer Objects
│   │   │   ├── exception/                 # Exception handling
│   │   │   ├── model/                     # Domain models
│   │   │   ├── repository/                # Data repositories
│   │   │   ├── service/                   # Business services
│   │   │   └── Application.java           # Application entry point
│   │   └── resources/
│   │       ├── application.yml            # Application configuration
│   │       ├── db/migration/              # Flyway migrations
│   │       └── static/                    # Static resources
│   └── test/                              # Tests
├── Dockerfile                             # Docker configuration
├── pom.xml                                # Maven dependencies
└── README.md                              # Service documentation
```

## Infrastructure as Code Structure

```
placementpro-infrastructure/
├── terraform/                             # Terraform IaC
│   ├── modules/                           # Reusable modules
│   │   ├── networking/                    # Networking infrastructure
│   │   ├── compute/                       # Compute resources
│   │   ├── database/                      # Database resources
│   │   └── security/                      # Security resources
│   ├── environments/                      # Environment-specific configs
│   │   ├── dev/                           # Development environment
│   │   ├── staging/                       # Staging environment
│   │   └── production/                    # Production environment
│   └── main.tf                            # Main Terraform file
├── kubernetes/                            # Kubernetes manifests
│   ├── base/                              # Base configurations
│   ├── overlays/                          # Environment overlays
│   └── kustomization.yaml                 # Kustomize config
└── README.md                              # Infrastructure documentation
```

## Documentation Structure

```
placementpro-docs/
├── architecture/                          # Architecture diagrams
├── api/                                   # API documentation
├── database/                              # Database schemas
├── deployment/                            # Deployment guides
├── development/                           # Development guides
├── testing/                               # Testing strategies
└── user/                                  # User documentation
```

## Branching Strategy

We follow a GitFlow branching strategy:

- `main` - Production code
- `develop` - Development code
- `feature/*` - Feature branches
- `release/*` - Release branches
- `hotfix/*` - Hotfix branches

## CI/CD Pipeline Structure

```
.github/workflows/
├── web-ci.yml                            # Web frontend CI pipeline
├── web-cd.yml                            # Web frontend CD pipeline
├── mobile-ci.yml                         # Mobile app CI pipeline 
├── mobile-cd.yml                         # Mobile app CD pipeline
└── services/                             # Microservices pipelines
    ├── [service-name]-ci.yml             # Service CI pipeline
    └── [service-name]-cd.yml             # Service CD pipeline
```

## Dependencies and Libraries

### Web Frontend
- React for UI components
- Redux for state management
- Material-UI for design system
- Formik for form handling
- Axios for API requests
- React Router for navigation

### Mobile App
- Jetpack Compose for UI
- Kotlin Coroutines and Flow for asynchronous operations
- Hilt for dependency injection
- Retrofit for API requests
- Room for local database
- Coil for image loading

### Microservices
- Spring Boot for application framework
- Spring Security for authentication and authorization
- Spring Data JPA for data access
- PostgreSQL for relational data
- MongoDB for document data
- Redis for caching
- Elasticsearch for search functionality

This repository structure provides a clear organization for the PlacementPro platform, enabling teams to work efficiently on different components while maintaining a coherent architecture.
