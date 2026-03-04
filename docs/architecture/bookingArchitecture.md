


# Architecture & Tech Stack – Luxury Private Chef Booking System

## Overview

This document defines the **technical architecture**, **stack decisions**, and **booking system logic** for the luxury private chef website.  
It reflects industry-standard practices for luxury services with controlled booking workflows, transactional emails, and payment integration.

---

## 1. Framework & Language

- **Next.js 14+ (App Router)**
- **TypeScript** (strict mode enabled)
- **TailwindCSS** for styling

**Reasoning:**
- Modern, scalable, production-ready
- Fullstack capabilities (frontend + backend API)
- Supports server-side rendering, static pages, and API routes
- Easy integration with Stripe and transactional email services

---

## 2. Database

- **MongoDB** (cloud-hosted e.g., Atlas)
- **Mongoose ODM**

**Reasoning:**
- Booking data is document-friendly
- Flexible and fast for development
- Supports schema validation and indexing for queries
- Easy to extend for additional features

---

## 3. Authentication (Admin)

- Single admin login (chef)
- Session-based authentication with httpOnly cookies
- Password hashed and stored securely in environment variables

**Reasoning:**
- Only one user needs admin access
- Simple, secure, and lightweight
- No need for multi-user roles or OAuth

---

## 4. Payments

- **Stripe Checkout** + **Stripe Webhooks**
- Flow:
  1. Admin approves booking
  2. Stripe checkout session generated
  3. Session ID stored in booking
  4. Email sent with payment link
  5. Stripe webhook confirms payment
  6. Booking updated → `paid`
  7. Confirmation email sent

**Reasoning:**
- Industry-standard
- Secure and PCI-compliant
- Supports international and Nigerian clients
- Handles multi-currency
- Clean API for automation

---

## 5. Email System

- **Resend** transactional email service
- Email types:
  - Booking submitted
  - Booking approved (with payment link)
  - Booking rejected
  - Payment confirmed

**Reasoning:**
- Simple API, easy integration with Next.js
- Maintains professional email deliverability

---

## 6. Booking Status Lifecycle


- submitted
- under_review
- approved
- payment_sent
- paid
- confirmed
- rejected
- cancelled



## 7. Booking Flow

1. Client submits booking → status `submitted`
2. System sends “request under review” email
3. Admin reviews in dashboard:

   * Approve → status `approved`
   * Reject → status `rejected`, email sent
4. Approved booking:

   * Stripe checkout session created
   * Status → `payment_sent`
   * Email sent with payment link
5. Stripe webhook confirms payment:

   * Status → `paid`
   * Confirmation email sent

---

## 8. Booking Schema (MongoDB)

```ts
{
  _id: ObjectId,
  fullName: string,
  email: string,
  phone: string,
  eventDate: Date,
  eventTime: string,
  guestCount: number,
  eventType: string,
  location: string,
  notes: string,

  status: 
    | "submitted"
    | "under_review"
    | "approved"
    | "payment_sent"
    | "paid"
    | "confirmed"
    | "rejected"
    | "cancelled",

  rejectionReason?: string,
  stripeSessionId?: string,
  stripePaymentIntentId?: string,

  createdAt: Date,
  updatedAt: Date
}
```

---

## 9. Folder Structure

```text
src/
 ├── app/
 │    ├── (public)/
 │    ├── admin/
 │    │     ├── login/
 │    │     ├── dashboard/
 │    │     └── bookings/
 │    └── api/
 │          ├── bookings/
 │          ├── admin/
 │          ├── stripe/
 │          └── webhooks/
 │
 ├── lib/
 │    ├── db.ts
 │    ├── stripe.ts
 │    ├── resend.ts
 │
 ├── models/
 │    └── Booking.ts
 │
 ├── utils/
 │
 └── types/
```

---

## 10. Admin Dashboard

* Minimal custom dashboard for chef
* Features:

  * Login
  * View all bookings
  * View booking details
  * Approve / Reject
  * Trigger Stripe payment link
  * Track payment status

**Reasoning:**
Industry-standard for booking luxury services. Protects pricing authority and booking control.

---

## 11. Principles & Standards

* Strict phase discipline: Discovery → Planning → Design → Build → Test → Launch
* System authority first, aesthetics second
* Secure all sensitive actions (payment, admin)
* Status-driven workflow ensures luxury brand authority
* Email + payment automation follows industry best practices

---

## 12. Summary

* **Frontend:** Next.js + TypeScript + TailwindCSS
* **Backend:** API routes inside Next.js
* **Database:** MongoDB + Mongoose
* **Payment:** Stripe
* **Email:** Resend
* **Booking Workflow:** Request → Admin Review → Payment → Confirmation
* **Admin:** Minimal dashboard for chef only.

