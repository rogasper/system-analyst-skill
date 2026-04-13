# Functional Specification Document - User Management System

## 1. Business Overview
This document specifies requirements for a User Management System that handles user registration, authentication, profile management, and role-based access control.

## 2. Functional Requirements

### 2.1 User Registration
- Users can register with email, password, full name, and phone number
- Email must be unique and validated
- Password must be at least 8 characters with uppercase, lowercase, and number
- Phone number must be in Indonesian format (starts with 08...)
- Upon registration, user receives verification email
- Default role is "user"

### 2.2 User Login
- Users can login with email and password
- System generates JWT token upon successful login
- Token expires in 24 hours
- Failed login attempts are tracked (max 5 attempts, then lock for 15 minutes)

### 2.3 User Profile Management
- Users can view their profile
- Users can update their profile (full name, phone number, address)
- Users cannot change their email directly (requires email change request)
- Address is stored as JSON object with: street, city, province, postal_code

### 2.4 User List (Admin Only)
- Admins can view list of all users
- List supports pagination (page, perPage)
- List supports filtering by: role, status, created_date range
- List supports search by: full name, email
- List shows: id, email, full_name, phone_number, role, status, created_at

### 2.5 User Details (Admin Only)
- Admins can view detailed information about any user
- Shows complete user profile including audit trail
- Shows login history (last 10 logins)

### 2.6 User Status Management (Admin Only)
- Admins can activate/deactivate users
- Deactivated users cannot login
- Reason for deactivation is required

## 3. Data Entities

### 3.1 users table
- Primary key: id (UUID)
- Fields: email, password (hashed), full_name, phone_number, role, status, address (JSON)
- Audit fields: created_at, created_by, updated_at, updated_by, deleted_at
- Indexes: email (unique), phone_number

### 3.2 login_history table
- Primary key: id (UUID)
- Foreign key: user_id (references users.id)
- Fields: ip_address, user_agent, login_time, status (success/failed)
- Indexes: user_id, login_time

### 3.3 email_verification table
- Primary key: id (UUID)
- Foreign key: user_id (references users.id)
- Fields: token, expires_at, verified_at
- Indexes: token (unique)

## 4. Roles
- **admin**: Full access to all features
- **user**: Can manage own profile only

## 5. User Status Values
- **active**: User is active and can login
- **inactive**: User is deactivated and cannot login
- **pending**: User is pending email verification

## 6. Non-Functional Requirements
- API response time < 500ms for simple operations
- API response time < 2s for list operations with filters
- All endpoints require JWT authentication (except registration and login)
- Password must be hashed using bcrypt
- Rate limiting: 100 requests per minute per IP
