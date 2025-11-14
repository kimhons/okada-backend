# Okada Backend - Laravel API

This repository contains the Laravel-based backend API for the Okada platform, providing all business logic, data management, and integrations for the Customer, Seller, Rider, and Admin applications.

## Project Overview

The Okada backend is built with Laravel and designed to run serverlessly on AWS Lambda using the Bref framework. This architecture minimizes hosting costs while providing automatic scaling and high availability.

## Technology Stack

- **Framework**: Laravel 10.x
- **Language**: PHP 8.2+
- **Database**: PostgreSQL (Amazon RDS Serverless)
- **Cache**: Redis (Amazon ElastiCache)
- **File Storage**: Amazon S3
- **Authentication**: Laravel Sanctum + Amazon Cognito
- **Deployment**: AWS Lambda via Bref
- **API Documentation**: Swagger/OpenAPI

## Architecture

This backend follows a **Domain-Driven Design (DDD)** approach with clear separation of concerns:

```
okada-backend/
├── app/
│   ├── Domain/              # Core business logic
│   │   ├── Customer/
│   │   ├── Seller/
│   │   ├── Rider/
│   │   ├── Order/
│   │   ├── Product/
│   │   └── Payment/
│   ├── Application/         # Use cases and application services
│   ├── Infrastructure/      # External integrations (S3, MTN, Orange, AI APIs)
│   └── Http/                # Controllers, middleware, requests
├── database/
│   ├── migrations/
│   └── seeders/
├── routes/
│   ├── api.php              # API routes
│   └── web.php              # Web routes (admin dashboard)
├── tests/
│   ├── Unit/
│   ├── Feature/
│   └── Integration/
└── docs/                    # API documentation
```

## Key Features

### Core Modules

1. **Authentication & Authorization**
   - User registration and login (Customer, Seller, Rider)
   - Role-based access control (RBAC)
   - JWT token management
   - Integration with Amazon Cognito

2. **Product Management**
   - Product CRUD operations
   - Category and subcategory management
   - Image upload to S3
   - Inventory tracking

3. **Order Management**
   - Order creation and processing
   - Real-time order status updates
   - Quality verification workflow
   - Order history and tracking

4. **Payment Processing**
   - MTN Mobile Money integration
   - Orange Money integration
   - Transaction logging and reconciliation
   - Refund processing

5. **Delivery Management**
   - Rider assignment algorithm
   - Real-time location tracking
   - Route optimization
   - Delivery confirmation

6. **AI Brain Integration**
   - Semantic search via AIMLAPI
   - Chatbot for customer support
   - Fraud detection
   - Product recommendations

### API Endpoints

All API endpoints are documented using Swagger/OpenAPI. Access the interactive documentation at `/api/documentation` when running locally.

## Getting Started

### Prerequisites

- PHP 8.2 or higher
- Composer
- PostgreSQL 14+
- Redis (optional for local development)
- AWS CLI (for deployment)

### Local Development Setup

1. Clone the repository:
```bash
git clone https://github.com/YOUR_USERNAME/okada-backend.git
cd okada-backend
```

2. Install dependencies:
```bash
composer install
```

3. Set up environment variables:
```bash
cp .env.example .env
php artisan key:generate
```

4. Configure your database in `.env`:
```
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=okada
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

5. Run migrations and seeders:
```bash
php artisan migrate --seed
```

6. Start the development server:
```bash
php artisan serve
```

The API will be available at `http://localhost:8000`.

## Testing

Run the full test suite:
```bash
php artisan test
```

Run specific test types:
```bash
php artisan test --testsuite=Unit
php artisan test --testsuite=Feature
```

Generate code coverage report:
```bash
php artisan test --coverage
```

## Deployment

This backend is designed to deploy to AWS Lambda using Bref. Deployment is automated via GitHub Actions.

### Manual Deployment

1. Install Bref:
```bash
composer require bref/bref bref/laravel-bridge --with-all-dependencies
```

2. Deploy to AWS:
```bash
serverless deploy --stage production
```

## Development Workflow

- **Main Branch**: Production-ready code only
- **Develop Branch**: Integration branch for features
- **Feature Branches**: `feature/module-name/feature-description` (e.g., `feature/orders/quality-verification`)
- **Bugfix Branches**: `bugfix/module-name/bug-description`

## Code Quality Standards

- All code must pass `php artisan test` with 100% success
- Code coverage must be above 80%
- All code must follow PSR-12 coding standards
- All API endpoints must be documented in Swagger

## Contributing

1. Create a feature branch from `develop`
2. Implement your feature with comprehensive tests
3. Ensure all tests pass and code is properly formatted
4. Submit a pull request to `develop`
5. Request review from Manus (Architect) or Claude Code (Implementation Lead)

## License

Proprietary - All rights reserved by Okada Platform

## Contact

For questions or support, contact the development team.

