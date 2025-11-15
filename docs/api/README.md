# Okada Platform API Documentation

Complete API documentation for the Okada e-commerce platform.

## 📚 Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [Base URLs](#base-urls)
- [Key Features](#key-features)
- [API Specification](#api-specification)
- [Quick Start](#quick-start)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [Webhooks](#webhooks)

---

## 🌟 Overview

The Okada Platform API is a RESTful API that powers all three mobile applications (Customer, Seller, Rider) and the Admin Dashboard. It provides comprehensive e-commerce functionality with Cameroon-specific features.

### Key Differentiators

1. **Quality Verification Photos** - Riders submit photos before delivery for customer approval
2. **MTN/Orange Money Integration** - Native support for Cameroon's top payment methods
3. **Bilingual Support** - Full English/French localization
4. **Offline-First** - Designed for intermittent connectivity
5. **FCFA Currency** - All amounts in Central African CFA franc

---

## 🔐 Authentication

All authenticated endpoints require a Bearer token in the Authorization header:

```http
Authorization: Bearer {access_token}
```

### Obtaining a Token

**Register:**
```http
POST /auth/register
Content-Type: application/json

{
  "name": "Jean Dupont",
  "email": "jean.dupont@email.com",
  "password": "SecurePassword123!",
  "phone": "+237650123456",
  "role": "customer",
  "language": "fr"
}
```

**Login:**
```http
POST /auth/login
Content-Type: application/json

{
  "email": "jean.dupont@email.com",
  "password": "SecurePassword123!"
}
```

**Response:**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "user": {
    "id": 1,
    "name": "Jean Dupont",
    "email": "jean.dupont@email.com",
    "role": "customer"
  }
}
```

### Token Refresh

```http
POST /auth/refresh
Authorization: Bearer {access_token}
```

### Logout

```http
POST /auth/logout
Authorization: Bearer {access_token}
```

---

## 🌐 Base URLs

| Environment | Base URL |
|-------------|----------|
| **Production** | `https://api.okada.cm/v1` |
| **Staging** | `https://staging-api.okada.cm/v1` |
| **Development** | `http://localhost:8000/api/v1` |

---

## ✨ Key Features

### 1. Quality Verification Photos (KEY DIFFERENTIATOR!)

Riders submit photos of products before delivery for customer approval.

**Submit Photos (Rider):**
```http
POST /quality-verifications
Authorization: Bearer {rider_token}
Content-Type: multipart/form-data

order_id: 123
photos: [file1.jpg, file2.jpg, file3.jpg]
notes: "Product looks good, ready for delivery"
```

**Approve Photos (Customer):**
```http
POST /quality-verifications/{id}/approve
Authorization: Bearer {customer_token}
```

**Reject Photos (Customer):**
```http
POST /quality-verifications/{id}/reject
Authorization: Bearer {customer_token}
Content-Type: application/json

{
  "reason": "Product appears damaged"
}
```

### 2. MTN/Orange Money Payments

**Initiate Payment:**
```http
POST /payments/initiate
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "order_id": 123,
  "payment_method": "mtn_money",
  "phone_number": "+237650123456"
}
```

**Check Payment Status:**
```http
GET /payments/{id}/status
Authorization: Bearer {access_token}
```

### 3. Real-Time Delivery Tracking

**Get Available Deliveries (Rider):**
```http
GET /deliveries/available?latitude=4.0511&longitude=9.7679&radius=5
Authorization: Bearer {rider_token}
```

**Update Delivery Location (Rider):**
```http
POST /deliveries/{id}/location
Authorization: Bearer {rider_token}
Content-Type: application/json

{
  "latitude": 4.0511,
  "longitude": 9.7679
}
```

### 4. Product Management

**List Products:**
```http
GET /products?page=1&per_page=20&category_id=1&search=phone&min_price=100000&max_price=500000&sort=price_asc
```

**Create Product (Seller):**
```http
POST /products
Authorization: Bearer {seller_token}
Content-Type: application/json

{
  "name": "Samsung Galaxy S21",
  "description": "Latest Samsung flagship",
  "price": 350000,
  "stock": 50,
  "category_ids": [1, 2]
}
```

**Upload Product Images (Seller):**
```http
POST /products/{id}/images
Authorization: Bearer {seller_token}
Content-Type: multipart/form-data

images: [image1.jpg, image2.jpg, image3.jpg]
```

### 5. Shopping Cart

**Get Cart:**
```http
GET /cart
Authorization: Bearer {access_token}
```

**Add to Cart:**
```http
POST /cart/items
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "product_id": 123,
  "quantity": 2
}
```

### 6. Orders

**Create Order:**
```http
POST /orders
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "delivery_address": {
    "street": "123 Avenue de la Liberté",
    "city": "Douala",
    "region": "Littoral",
    "country": "Cameroon",
    "phone": "+237650123456"
  },
  "payment_method": "mtn_money",
  "phone_number": "+237650123456",
  "notes": "Please call before delivery"
}
```

**Get Order Details:**
```http
GET /orders/{id}
Authorization: Bearer {access_token}
```

---

## 📖 API Specification

The complete API specification is available in **OpenAPI 3.0** format:

- **File**: [`openapi.yaml`](./openapi.yaml)
- **Format**: YAML
- **Version**: 1.0.0

### Viewing the Specification

**Option 1: Swagger UI (Recommended)**
```bash
# Install Swagger UI
npm install -g swagger-ui-watcher

# View the spec
swagger-ui-watcher openapi.yaml
```

**Option 2: Online Viewer**
1. Go to [editor.swagger.io](https://editor.swagger.io/)
2. Copy the contents of `openapi.yaml`
3. Paste into the editor

**Option 3: VS Code Extension**
1. Install "OpenAPI (Swagger) Editor" extension
2. Open `openapi.yaml`
3. Right-click → "Preview Swagger"

### Generating API Clients

The OpenAPI specification can be used to generate API clients in multiple languages:

**Flutter/Dart:**
```bash
cd ../../../okada-mobile
./scripts/generate_api_client.sh
```

**JavaScript/TypeScript:**
```bash
openapi-generator-cli generate -i openapi.yaml -g typescript-axios -o ./clients/typescript
```

**Python:**
```bash
openapi-generator-cli generate -i openapi.yaml -g python -o ./clients/python
```

---

## 🚨 Error Handling

### Error Response Format

```json
{
  "message": "An error occurred",
  "error": "ERROR_CODE"
}
```

### Validation Errors

```json
{
  "message": "The given data was invalid",
  "errors": {
    "email": ["The email field is required"],
    "password": ["The password must be at least 8 characters"]
  }
}
```

### HTTP Status Codes

| Code | Description |
|------|-------------|
| **200** | OK - Request successful |
| **201** | Created - Resource created successfully |
| **204** | No Content - Request successful, no content to return |
| **400** | Bad Request - Invalid request format |
| **401** | Unauthorized - Invalid or missing authentication token |
| **403** | Forbidden - User does not have permission |
| **404** | Not Found - Resource not found |
| **409** | Conflict - Resource conflict (e.g., delivery already accepted) |
| **413** | Payload Too Large - File upload too large |
| **422** | Unprocessable Entity - Validation error |
| **429** | Too Many Requests - Rate limit exceeded |
| **500** | Internal Server Error - Server error |
| **503** | Service Unavailable - Server temporarily unavailable |

---

## ⏱️ Rate Limiting

To ensure fair usage and system stability, the API implements rate limiting:

| Endpoint Type | Rate Limit |
|---------------|------------|
| **Authentication** | 10 requests per minute |
| **Read Operations** | 100 requests per minute |
| **Write Operations** | 50 requests per minute |
| **File Uploads** | 20 requests per minute |

### Rate Limit Headers

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1642345678
```

### Rate Limit Exceeded Response

```http
HTTP/1.1 429 Too Many Requests
Content-Type: application/json

{
  "message": "Too many requests. Please try again later.",
  "error": "RATE_LIMIT_EXCEEDED",
  "retry_after": 60
}
```

---

## 🔔 Webhooks

The API supports webhooks for real-time event notifications.

### Supported Events

| Event | Description |
|-------|-------------|
| `order.created` | New order placed |
| `order.confirmed` | Order confirmed by seller |
| `order.cancelled` | Order cancelled |
| `payment.completed` | Payment successful |
| `payment.failed` | Payment failed |
| `delivery.accepted` | Rider accepted delivery |
| `delivery.picked_up` | Order picked up from seller |
| `delivery.in_transit` | Order in transit |
| `delivery.delivered` | Order delivered |
| `quality_verification.submitted` | Quality photos submitted |
| `quality_verification.approved` | Quality photos approved |
| `quality_verification.rejected` | Quality photos rejected |

### Webhook Endpoints

**MTN Mobile Money:**
```http
POST /payments/webhook/mtn
```

**Orange Money:**
```http
POST /payments/webhook/orange
```

### Webhook Security

All webhook requests include a signature header for verification:

```http
X-Webhook-Signature: sha256=abc123...
```

---

## 📊 Pagination

List endpoints support pagination with the following parameters:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | integer | 1 | Page number |
| `per_page` | integer | 20 | Items per page (max: 100) |

### Pagination Response

```json
{
  "data": [...],
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 10,
    "per_page": 20,
    "to": 20,
    "total": 200
  }
}
```

---

## 🌍 Localization

### Supported Languages

- **English** (`en`)
- **French** (`fr`)

### Setting Language

**In User Profile:**
```http
PUT /users/me
Content-Type: application/json

{
  "language": "fr"
}
```

**In Request Header:**
```http
Accept-Language: fr
```

### Currency

All amounts are in **FCFA** (Central African CFA franc):

```json
{
  "price": 350000,
  "currency": "FCFA"
}
```

### Phone Numbers

All phone numbers must be in **Cameroon format** (+237):

```
+237650123456
```

### Tax Rate

**VAT**: 19.25% (Cameroon standard rate)

---

## 🔧 Development

### Local Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/kimhons/okada-backend.git
   cd okada-backend
   ```

2. **Install dependencies:**
   ```bash
   composer install
   ```

3. **Configure environment:**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Run migrations:**
   ```bash
   php artisan migrate
   ```

5. **Start the server:**
   ```bash
   php artisan serve
   ```

6. **Access the API:**
   ```
   http://localhost:8000/api/v1
   ```

### Testing

```bash
# Run all tests
php artisan test

# Run specific test suite
php artisan test --testsuite=Feature

# Run with coverage
php artisan test --coverage
```

---

## 📞 Support

For API support, please contact:

- **Email**: api@okada.cm
- **Documentation**: https://docs.okada.cm
- **GitHub Issues**: https://github.com/kimhons/okada-backend/issues

---

## 📄 License

Proprietary - © 2024 Okada Platform. All rights reserved.

---

**Last Updated**: January 2024  
**API Version**: 1.0.0  
**OpenAPI Version**: 3.0.3

