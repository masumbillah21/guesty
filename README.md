# Guesty PMS - Multi-Tenant Property Management System (SaaS)

[![Laravel](https://img.shields.io/badge/Laravel-13.x-FF2D20?style=for-the-badge&logo=laravel)](https://laravel.com)
[![React](https://img.shields.io/badge/React-19.x-61DAFB?style=for-the-badge&logo=react)](https://react.dev)
[![Inertia.js](https://img.shields.io/badge/Inertia.js-3.0-9553E9?style=for-the-badge&logo=inertia)](https://inertiajs.com)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-4.x-38BDF8?style=for-the-badge&logo=tailwind-css)](https://tailwindcss.com)

**Guesty PMS** is an enterprise-grade, multi-tenant Property Management System (PMS) SaaS designed for hotel operators, vacation rental managers, and hospitality businesses. It streamlines property management, multi-channel distribution, automated guest messaging, billing, and real-time notifications in one centralized application.

Detailed architectural specifications, system diagrams, data models, and feature blueprints can be found in the [Product Brain Document](./PRODUCT_BRAIN.md).

---

## Key Features

### 1. 🏢 Multi-Tenant SaaS Architecture
- **Workplace Isolation**: Single-database multi-tenancy with logical data isolation (`team_id` / `tenant_id` scoping).
- **Flexible Workspace Routing**: Multi-tenant resolution via subdomains, custom domains, or workspace slugs (`/w/{tenant_slug}`).
- **Resource Quotas**: Enforced subscription limits on property listings, staff seats, and integration credits per tenant.

### 2. 🔐 Role & Permission Engine (RBAC)
- **Super Admin**: Platform management, system health overview, tenant auditing, and global billing metrics.
- **Tenant Admin (Workplace Owner)**: Full administrative authority over a specific workspace, billing, integrations, and staff.
- **Custom Workplace Roles**: Tenant Admins can dynamically create custom roles (e.g., Front Desk, Housekeeping, Revenue Manager) and assign granular permissions.

### 3. 💳 Stripe Subscription & Billing Engine
- Powered by **Laravel Cashier** for seamless Stripe integration.
- Tiered subscription plans (Starter, Growth, Enterprise) based on active listing counts.
- Usage-based add-ons for high-frequency channel sync and WhatsApp message bundles.
- Self-service Stripe Customer Portal for automated invoicing, plan changes, and payment updates.

### 4. 🔑 Authentication & Security
- **Multi-Guard Setup**: Stateful session authentication for Inertia.js web app & JWT tokens for REST/Mobile APIs.
- **Social Login**: OAuth 2.0 via Laravel Socialite (Google, Apple, Microsoft).
- **Two-Factor Authentication (2FA)**: TOTP Authenticator app support & Passkeys/WebAuthn biometric auth via `@laravel/passkeys`.

### 5. 🌐 Channex Integration (OTA Channel Manager)
- Real-time 2-way synchronization with **Channex.io API**.
- **ARI Push**: Instant updates for Availability, Rates, and Inventory to OTAs (Airbnb, Booking.com, Expedia, VRBO).
- **Reservation Webhooks**: Automated ingestion of reservations, modifications, and cancellations from channel partners.
- Interactive property and room type mapping interface.

### 6. 💬 WhatsApp Integration
- Powered by **Meta Cloud API** / Twilio WhatsApp API.
- Automated guest messaging flows: Booking confirmation, pre-arrival check-in guide, digital keys, in-stay support, and check-out feedback.
- Unified Guest Inbox UI built with React for staff-guest messaging.
- WhatsApp HSM template submission and status management.

### 7. 🔔 Multi-Channel Notification Dispatcher
- **In-App**: Real-time WebSocket alerts powered by Laravel Reverb / Pusher.
- **Email**: Transactional emails via AWS SES / Postmark.
- **SMS**: Critical alerts delivered via Twilio / Vonage.
- **Push**: Background push notifications for Web PWA, iOS, and Android via **Firebase Cloud Messaging (FCM)**.
- User-level notification channel preferences matrix.

---

## Technology Stack

| Layer | Technologies Used |
| :--- | :--- |
| **Backend** | [Laravel 13](./composer.json), PHP 8.3+, Fortify, Cashier, Wayfinder |
| **Frontend** | [React 19](./package.json), Inertia.js 3.0, TypeScript 5.7, Tailwind CSS 4 |
| **UI Components** | Radix UI Primitives, Lucide Icons, Sonner Notifications |
| **Database & Cache** | PostgreSQL / MySQL, Redis (Queues & WebSockets) |
| **Testing & Tooling** | Pest / PHPUnit, PHPStan, Pint, Prettier, ESLint |

---

## Project Structure Overview

```
guesty/
├── app/
│   ├── Concerns/          # Trait definitions (HasTeams, Slugs)
│   ├── Enums/             # PHP Enums (TeamRole, ReservationStatus)
│   ├── Http/              # Controllers, Middleware, Request Validation
│   ├── Models/            # Eloquent Models (User, Team, Membership)
│   ├── Notifications/     # Multi-channel notification classes
│   └── Policies/          # Authorization policy rules
├── config/                # Framework & package configurations
├── database/
│   ├── factories/         # Model factories for testing
│   ├── migrations/        # Database schema migrations
│   └── seeders/           # Initial database seeders
├── resources/
│   ├── js/                # React 19 Inertia SPA application
│   └── css/               # Tailwind CSS 4 design system styles
├── routes/                # Application routes (web, api, settings, console)
├── PRODUCT_BRAIN.md       # Product Brain & Technical Architecture Blueprint
└── README.md              # Project Documentation
```

---

## Local Development Setup

### Prerequisites
- PHP `>= 8.3`
- Composer `>= 2.7`
- Node.js `>= 20.x` and `npm` or `pnpm`
- SQLite / PostgreSQL / MySQL
- Redis (Optional, recommended for queues & WebSockets)

### Step-by-Step Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-org/guesty.git
   cd guesty
   ```

2. **Install PHP & Node Dependencies**:
   ```bash
   composer install
   npm install
   ```

3. **Configure Environment Variables**:
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Run Database Migrations & Seeders**:
   ```bash
   php artisan migrate --seed
   ```

5. **Start Development Servers**:
   ```bash
   composer dev
   ```
   Or run backend and frontend separately:
   ```bash
   php artisan serve
   npm run dev
   ```

---

## Environment Configuration (`.env`)

Ensure the following service credentials are configured in your `.env` file for full feature activation:

```ini
# Application Setup
APP_NAME="Guesty PMS"
APP_ENV=local
APP_URL=http://localhost:8000

# Stripe Configuration
STRIPE_KEY=pk_test_...
STRIPE_SECRET=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Social Auth (Laravel Socialite)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI="${APP_URL}/auth/google/callback"

# Channex OTA Integration
CHANNEX_API_KEY=
CHANNEX_ENVIRONMENT=staging # or production
CHANNEX_WEBHOOK_SECRET=

# WhatsApp Business API
WHATSAPP_PHONE_NUMBER_ID=
WHATSAPP_BUSINESS_ACCOUNT_ID=
WHATSAPP_ACCESS_TOKEN=

# Firebase Cloud Messaging (FCM Push)
FCM_SERVER_KEY=
FCM_SENDER_ID=
FCM_PROJECT_ID=

# Multi-Channel Notifications (Mail & SMS)
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
TWILIO_SID=
TWILIO_AUTH_TOKEN=
TWILIO_FROM_NUMBER=
```

---

## Code Quality & Testing Commands

Execute quality check scripts prior to committing code:

```bash
# Code Formatting (Laravel Pint & Prettier)
composer lint:check
npm run format:check

# Static Analysis & Type Checking
composer types:check    # PHPStan analysis
npm run types:check     # TypeScript check

# Run Automated Test Suite
composer test
```

---

## Documentation & Architecture

For a complete breakdown of domain data models, notification queues, WhatsApp message flows, and Channex 2-way sync mechanics, refer to the [Product Brain Document](./PRODUCT_BRAIN.md).
