# Campus Resource Management System (CRMS) - Project Report

## 1. Project Overview
The **Campus Resource Management System (CRMS)** is a comprehensive, multi-tenant Software-as-a-Service (SaaS) application designed to modernize and streamline the scheduling of physical university resources (Computer Labs, Lecture Halls, Equipment). By transitioning from traditional pen-and-paper or disjointed spreadsheets to a unified digital platform, CRMS ensures that resources are allocated efficiently without double-booking or scheduling conflicts.

## 2. Technology Stack & Deployment
The system strictly adheres to modern web development standards, utilizing a decoupled Client-Server architecture:

* **Frontend (Client):** Developed using **Next.js** (React) styled with **Tailwind CSS** and animated via **Framer Motion** for a premium "Midnight Neon" Glassmorphism aesthetic. It is hosted globally via **Vercel**.
* **Backend (Server):** An **Express.js** API written entirely in robust **TypeScript**. It acts as the business logic brain and is deployed on **Render**.
* **Database:** A highly available Relational **PostgreSQL** Database hosted on **Neon**, queried and mutated using the **Prisma ORM**.

## 3. System Design & User Hierarchy
The system handles strict scoping through JSON Web Tokens (JWT) to enforce proper boundaries between users and universities:

* **SUPER_ADMIN (The Platform Owner):** Has the architectural clearance to generate entirely new `Institution` clusters within the database. 
* **ADMIN (The University Manager):** Tied to a specific institution. Admins are equipped with a powerful "Command Center" dashboard where they can oversee all institution operations, approve/reject student lab requests, and override double-booking conflicts.
* **STUDENT & FACULTY:** Standard users capable of interacting with the dynamic calendar, checking resource capacities, and making time-bound booking requests.

## 4. Applied Software Engineering Patterns
This capstone elevates itself beyond a standard CRUD application by heavily relying on **Gang of Four (GoF) Object-Oriented Design Patterns** to enforce complex business logic:

### A. The Strategy Pattern (Conflict Resolution)
* **Location:** `/patterns/strategy/`
* **Purpose:** The Strategy pattern allows the application to swap conflict resolution algorithms dynamically. Instead of creating a monolithic, fragile `if/else` block to handle booking conflicts, the server utilizes a `StrictConflictStrategy` (which strictly applies First-Come-First-Serve blocking mechanics) or a `PriorityConflictStrategy` (which selectively auto-cancels lower-tier requests based on hierarchical priority rules).

### B. The State Pattern (Booking Lifecycles)
* **Location:** `/patterns/state/`
* **Purpose:** A semantic booking flows through strict legal transitions (`PENDING` ➔ `APPROVED`, or `APPROVED` ➔ `CANCELLED`). The State Pattern maps each of these states to isolated classes. This guarantees that an Admin cannot legally transition a booking from `CANCELLED` back out to `APPROVED`, maintaining unbreakable data integrity globally. 

### C. The Observer Pattern (Event-Driven Architecture)
* **Location:** `/patterns/observer/`
* **Purpose:** A decentralized Publish-Subscribe (Pub/Sub) model. When a critical database event occurs (e.g., an Admin clicking "Approve" on a booking), the core Controller does not manually dispatch emails or fire notifications. Instead, it simply alerts the central `EventBus` (`Subject`). Downstream `Observers` (like the NotificationHandler or LogHandler) listen for this broadcast and independently trigger their secondary effects, dramatically reducing system coupling.

### D. The Factory Pattern (Abstracted Object Creation)
* **Location:** `/patterns/factory/UserFactory.ts`
* **Purpose:** Centralizes the complex assembly of user objects. When the backend needs to construct a user based on a database query, the Factory orchestrates injecting the correct subset of operational privileges based on the user's enumerated Role (`STUDENT`, `FACULTY`, `ADMIN`).

### E. The Mapper/DTO Pattern (Data Hiding)
* **Location:** `/mappers/`
* **Purpose:** Ensures raw database rows are never directly returned over the API. Data Transfer Objects (DTOs) deliberately strip sensitive internal fields (like `passwordHash` or `deletedAt` timestamps) out of the Prisma schema payload before releasing the JSON to the Next.js client.

## 5. Summary
The CRMS represents a structurally sound, enterprise-ready architecture. From zero-downtime CI/CD deployment pipelines on Vercel/Render to strict TypeScript integration and proven GoF software engineering patterns, the system is fundamentally designed to prevent race conditions, scale reliably, and offer an incredibly fluid user experience.
