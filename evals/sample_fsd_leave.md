# Functional Specification Document: Employee Leave Management System

## 1. Overview

| Field | Detail |
|-------|--------|
| Project Name | Employee Leave Management (ELM) |
| Description | System to manage employee leave requests: apply, approve/reject, balance tracking, and reporting |
| Target Users | All employees, department managers, HR admin, system admin |
| Priority | High — replaces manual WhatsApp/email process |
| Figma | https://www.figma.com/file/elm-prototype |
| Start Date | 2026-05-01 |
| Target Go-Live | 2026-06-01 |

---

## 2. User Roles

| Role | Description |
|------|-------------|
| **Employee** | Apply for leave, view own leave history and balance |
| **Manager** | Approve/reject leave for team members, view team leave calendar |
| **HR Admin** | Manage leave types, manage leave balance, view all leave reports, manage employees |
| **System Admin** | User management, system configuration, audit log access |

---

## 3. Functional Requirements

### 3.1 Employee Leave Application

**FR-01: Submit Leave Request**

- Employee can apply for leave by selecting:
  - Leave type (Annual, Sick, Personal, Maternity, Paternity, Marriage, Unpaid)
  - Start date and end date
  - Half-day option (morning/afternoon)
  - Attachment (for Sick leave: medical certificate, max 5MB PDF/image)
  - Reason (optional for Annual/Personal, required for Sick)
- System validates:
  - Leave must be at least 1 day (or half-day)
  - Cannot apply for past dates
  - Cannot apply for more than 3 consecutive days without manager pre-approval (flag for HR review)
  - Cannot apply if balance is insufficient (for Annual leave)
- System calculates leave balance available before submission
- Leave status initially: `PENDING_MANAGER`
- After submission, notification sent to direct manager (email + in-app)

**FR-02: Edit Leave Request**

- Employee can edit leave request only if status is `PENDING_MANAGER` or `PENDING_HR`
- Cannot edit if status is `APPROVED` or `REJECTED`
- After edit, status resets to `PENDING_MANAGER` (re-approval required)
- Edit history tracked in `leave_requests.audit_log`

**FR-03: Cancel Leave Request**

- Employee can cancel leave request if status is `PENDING_MANAGER` or `PENDING_HR`
- Cancellation does NOT restore leave balance automatically
- Balance restored only when status is `REJECTED` or `CANCELLED` by HR/manager
- After cancellation, notification sent to manager and HR

### 3.2 Leave Approval Workflow

**FR-04: Manager Approval**

- Manager receives notification when team member submits leave
- Manager can:
  - **Approve**: Status → `APPROVED`, balance deducted, notification to employee
  - **Reject**: Status → `REJECTED`, balance preserved, notification to employee with reason
  - **Escalate to HR**: Status → `PENDING_HR`, notification to HR admin
- Manager can only approve/reject for their direct reports (not for themselves — HR handles)
- Manager cannot approve leave for themselves

**FR-05: HR Approval**

- HR admin receives notification for escalated leave requests
- HR can:
  - **Approve**: Status → `APPROVED`, balance deducted, notification to employee and manager
  - **Reject**: Status → `REJECTED`, balance preserved, notification to employee and manager
  - **Reassign to another manager**: Status → `PENDING_MANAGER` (new manager)
- HR can view all leave requests across the company

**FR-06: Auto-Approval**

- For Annual leave ≤ 2 consecutive days: auto-approve if employee has sufficient balance
- Auto-approved leaves skip manager approval step
- Status goes directly from `PENDING_MANAGER` → `APPROVED`
- Notification still sent to manager for awareness

### 3.3 Leave Balance Management

**FR-07: Leave Balance Accrual**

- Annual leave balance accrues monthly: 1 day per month of service, max 12 days carried forward
- Balance resets (carried forward) on January 1st each year
- Unused balance above 12 days is forfeited on December 31st
- Pro-rated for new hires: (months remaining in year / 12) × 12

**FR-08: Manual Balance Adjustment by HR**

- HR admin can manually adjust leave balance (add/deduct days)
- Requires reason and approval from System Admin
- Adjustment recorded in `leave_balances.audit_log`
- Audit trail includes: who, when, what, why

### 3.4 Leave Calendar & Reporting

**FR-09: Team Leave Calendar**

- Manager can view team leave calendar (month view)
- Shows: approved leaves, pending requests, no-show days
- Color-coded by leave type
- Export to CSV available

**FR-10: Leave Report**

- HR admin can generate leave reports:
  - By department
  - By employee
  - By leave type
  - By date range
- Report includes: total applied, approved, rejected, pending, balance remaining
- Export to PDF and CSV

### 3.5 Employee Self-Service

**FR-11: View Leave History**

- Employee can view their own leave history
- Filter by: leave type, date range, status
- Shows: leave type, dates, status, approver, reason (if any)

**FR-12: View Leave Balance**

- Employee can view current leave balance
- Shows: Annual leave remaining, sick leave remaining, maternity/paternity remaining
- Shows: accrued this year, used this year, carried forward from previous year

### 3.6 Notifications

**FR-13: Notification System**

- Email notification for:
  - Leave request submitted (to manager)
  - Leave request approved/rejected (to employee)
  - Leave request escalated to HR (to HR admin)
  - Leave balance below threshold (to employee, configurable threshold)
- In-app notification for:
  - All of the above
  - New leave request in team calendar
- Notification preferences: employee can choose email + in-app, email only, or in-app only

---

## 4. Non-Functional Requirements

| Category | Requirement |
|----------|-------------|
| **Performance** | API response time < 200ms (p95), calendar page < 1s load |
| **Security** | RBAC, JWT auth, data encryption at rest, audit logging for all balance changes |
| **Availability** | 99.9% uptime during business hours (08:00-18:00 WIB) |
| **Data Retention** | Leave records retained for 7 years per labor law |
| **Compliance** | GDPR-like: employees can request data export, right to be forgotten (anonymize) |
| **Localization** | Indonesian language UI, date format DD/MM/YYYY, time zone WIB (UTC+7) |

---

## 5. Business Rules

| Rule ID | Description |
|---------|-------------|
| **BR-01** | Annual leave max 12 days/year, accrues 1 day/month |
| **BR-02** | Sick leave max 12 days/year, requires medical certificate for ≥ 3 consecutive days |
| **BR-03** | Marriage leave: 3 days (consecutive), requires marriage certificate attachment |
| **BR-04** | Maternity leave: 3 months (90 days), requires medical certificate |
| **BR-05** | Paternity leave: 2 days (consecutive), requires birth certificate |
| **BR-06** | Unpaid leave: unlimited days, requires HR approval only (skips manager) |
| **BR-07** | Personal leave: max 3 days/year, no certificate required |
| **BR-08** | Cannot apply for leave on public holidays (checked against holiday list) |
| **BR-09** | Leave cannot span across different calendar years (split into two requests) |
| **BR-10** | Manager approval deadline: 2 business days, auto-escalate to HR if no action |
| **BR-11** | Annual leave balance carry-forward: max 12 days, excess forfeited Dec 31 |
| **BR-12** | New hire pro-rated annual leave: (months remaining / 12) × 12 |

---

## 6. Public Holidays

Public holidays are managed separately (referenced by the leave system). The system must check against a `public_holidays` table when validating leave requests.

| Year | Holiday Name | Date | Type |
|------|-------------|------|------|
| 2026 | New Year's Day | 01/01/2026 | National |
| 2026 | Indonesian New Year (Chinese) | 19/02/2026 | National |
| 2026 | Ascension of the Prophet | 27/03/2026 | National |
| 2026 | Good Friday | 10/04/2026 | National |
| 2026 | Labor Day | 01/05/2026 | National |
| 2026 | Ascension Day | 14/05/2026 | National |
| 2026 | Pancasila Day | 01/06/2026 | National |
| 2026 | Eid Holiday (varies) | 18-19/06/2026 | National |
| 2026 | Independence Day | 17/08/2026 | National |
| 2026 | Christmas Day | 25/12/2026 | National |

*Note: Exact dates subject to government announcement. System must support admin-managed holiday list.*

---

## 7. Edge Cases

| Scenario | Expected Behavior |
|----------|------------------|
| Manager is on leave | HR handles approval (auto-escalate) |
| Employee applies on weekend/holiday | Treated as submitted on next business day |
| Balance becomes zero mid-approval | Approval still allowed (balance can go negative, limited to -3 days) |
| Concurrent approval attempts (manager + HR) | Last-write-wins with optimistic locking (version field) |
| Attachment exceeds 5MB | Reject with error: "File too large (max 5MB)" |
| Invalid file type for attachment | Reject with error: "Only PDF and images allowed" |
| Leave request spans public holiday | Holiday excluded from leave day count |
| Employee leaves company | All pending requests auto-cancelled, balance forfeited |
| Duplicate leave application (same type, overlapping dates) | Reject with error: "You already have a leave request for these dates" |

---

## 8. Assumptions

| Assumption | Reason |
|------------|--------|
| Employee data already exists in the existing `mst_employees` table | Will reference existing employee master data, no need to create new employee registration |
| Manager hierarchy is stored in existing `mst_employees.manager_id` column | Uses existing reporting structure |
| Email service is already configured (SendGrid or similar) | Will use existing email service integration |
| Public holidays list is managed by HR admin in a separate module | Leave system references it, does not manage it |
| System uses JWT for authentication (existing auth module) | Will integrate with existing auth system |
| File upload uses existing AWS S3 integration | Will use existing file storage service |

---

## 9. Out of Scope

| Item | Reason |
|------|--------|
| Overtime management | Separate module planned |
| Time attendance / clock-in | Separate system (existing) |
| Payroll integration | Phase 2 only |
| Mobile app | Web responsive only, Phase 2 native app |
| Leave pool / flexi leave | Not required for current implementation |
| Multi-company support | Single company only for now |
