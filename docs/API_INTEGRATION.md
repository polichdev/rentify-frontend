# Rentify Frontend — API Integration Guide

This document outlines the API integration layer between **Rentify Frontend** (`React 19 + TypeScript`) and **Rentify Backend** (`Spring Boot REST API`).

---

## Table of Contents
- [Architecture & Client Setup](#architecture--client-setup)
- [Authentication & Security](#authentication--security)
- [Payload Transformation (snake_case to camelCase)](#payload-transformation)
- [Error Handling & Lifecycle Events](#error-handling--lifecycle-events)
- [API Endpoints Reference](#api-endpoints-reference)
  - [Authentication & Sessions](#1-authentication--sessions)
  - [User Profile Management](#2-user-profile-management)
  - [Properties & Map Listings](#3-properties--map-listings)
  - [Bookings & Reservations](#4-bookings--reservations)
  - [Wallet & Synthetic Payments](#5-wallet--synthetic-payments)
  - [Promotions & Boost Packages](#6-promotions--boost-packages)
  - [In-App Conversations & Chat](#7-in-app-conversations--chat)
  - [Reviews & Ratings](#8-reviews--ratings)
  - [Favorites](#9-favorites)
  - [Amenities & Locations](#10-amenities--locations)

---

## Architecture & Client Setup

The frontend uses **Axios** as its primary HTTP client, encapsulated in `src/services/api.ts`, and powered by **TanStack Query v5** for declarative caching and asynchronous state synchronization.

### Base Configuration
- **Base URL**: `/api/v1` (configured via `VITE_API_BASE_URL`)
- **Dev Proxy**: Proxies `/api` requests to `http://localhost:8080` (configured via `VITE_DEV_API_PROXY_TARGET`)
- **Headers**:
  - `Content-Type: application/json`
  - `X-Requested-With: XMLHttpRequest`

---

## Authentication & Security

Rentify supports two authentication strategies configured via `VITE_AUTH_STRATEGY`:

### 1. HTTP-Only Cookie Strategy (`VITE_AUTH_STRATEGY=cookie`) [Default]
- Authentication token is stored in a secure, HTTP-only cookie managed by the server.
- **`withCredentials: true`** is enabled on all Axios instances.
- **CSRF Protection**: For state-mutating requests (`POST`, `PUT`, `PATCH`, `DELETE`), the frontend reads the `csrf_token` cookie and injects it into the `X-CSRF-Token` HTTP header.

### 2. Bearer Token Strategy (`VITE_AUTH_STRATEGY=bearer`)
- Authentication token is retrieved from `localStorage` / memory.
- Injected into every outgoing HTTP request header as:
  ```http
  Authorization: Bearer <jwt_token>
  ```

---

## Payload Transformation

The backend exposes data in standard JSON `snake_case`, while the frontend strictly adheres to TypeScript `camelCase` conventions.

The Axios instance includes a custom deep response transformer (`camelCaseKeysDeep`) that converts all response keys recursively at the transport boundary before data reaches React Query hooks:

```typescript
// Backend response:
{ "property_id": 12, "nightly_price": 1500, "is_instant_booking": true }

// Frontend received object:
{ propertyId: 12, nightlyPrice: 1500, isInstantBooking: true }
```

---

## Error Handling & Lifecycle Events

The HTTP client dispatches global DOM events on critical network state changes:

| Event Name | Trigger Condition | Handled Behavior |
| :--- | :--- | :--- |
| `auth:session-expired` | HTTP `401 Unauthorized` received on protected endpoint | Clears React Query cache, resets `AuthContext`, and redirects to login |
| `network:offline` | Network error / loss of internet connection | Displays floating network status banner to notify user |

---

## API Endpoints Reference

### 1. Authentication & Sessions

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `POST` | `/api/v1/auth/login` | Authenticate with email and password | No |
| `POST` | `/api/v1/auth/register` | Register new user account | No |
| `POST` | `/api/v1/auth/google` | Authenticate via Google OAuth 2.0 token | No |
| `POST` | `/api/v1/auth/logout` | Terminate active session and invalidate cookie | Yes |

---

### 2. User Profile Management

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/users/profile` | Fetch authenticated user profile details | Yes |
| `GET` | `/api/v1/users/me` | Lightweight session verification probe | Yes |
| `PUT` | `/api/v1/users/profile` | Update personal information (name, phone) | Yes |
| `PATCH` | `/api/v1/users/profile/password` | Change user account password | Yes |
| `POST` | `/api/v1/users/profile/avatar` | Upload and set profile avatar image | Yes |
| `DELETE` | `/api/v1/users/profile/avatar` | Remove existing avatar image | Yes |
| `GET` | `/api/v1/users/{id}/public` | Fetch public host profile, rating, and listings | No |

---

### 3. Properties & Map Listings

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/properties/search` | Paginated search with filters (city, type, price, amenities) | No |
| `GET` | `/api/v1/properties/search/map-pins` | High-speed coordinates payload for map clusters & pins | No |
| `GET` | `/api/v1/properties/{id}` | Full property listing details with images and host info | No |
| `POST` | `/api/v1/properties` | Create and publish a new property listing | Yes |
| `PUT` | `/api/v1/properties/{id}` | Update existing property details and pricing | Yes |
| `DELETE` | `/api/v1/properties/{id}` | Delete or unpublish listing | Yes |
| `GET` | `/api/v1/properties/my` | Retrieve all listings owned by current user | Yes |

---

### 4. Bookings & Reservations

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `POST` | `/api/v1/bookings` | Submit a rental booking request | Yes |
| `GET` | `/api/v1/bookings/my` | List tenant's booking history and status | Yes |
| `GET` | `/api/v1/bookings/host` | List all incoming booking requests for host's properties | Yes |
| `GET` | `/api/v1/bookings/{id}` | Retrieve booking details and payment state | Yes |
| `PATCH` | `/api/v1/bookings/{id}/status` | Accept, reject, or cancel booking (`CONFIRMED`, `REJECTED`, `CANCELLED`) | Yes |

---

### 5. Wallet & Synthetic Payments

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/wallet` | Fetch current digital wallet balance and currency | Yes |
| `GET` | `/api/v1/wallet/transactions` | Retrieve historical transaction ledger | Yes |
| `POST` | `/api/v1/wallet/top-up` | Top up in-app wallet balance | Yes |
| `POST` | `/api/v1/payments/process` | Process synthetic payment for booking or boost package | Yes |

---

### 6. Promotions & Boost Packages

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/promotions/packages` | Fetch available listing boost packages and tiers | Yes |
| `POST` | `/api/v1/promotions` | Purchase and apply promotion boost to a property | Yes |
| `GET` | `/api/v1/promotions/my` | Active promotional campaigns and expiry dates | Yes |

---

### 7. In-App Conversations & Chat

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/conversations` | List user's active conversation threads | Yes |
| `POST` | `/api/v1/conversations` | Create or retrieve conversation thread for a property | Yes |
| `GET` | `/api/v1/conversations/{id}/messages` | Retrieve message history for conversation thread | Yes |
| `POST` | `/api/v1/conversations/{id}/messages` | Send message in conversation thread | Yes |

---

### 8. Reviews & Ratings

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/reviews/property/{propertyId}` | Fetch reviews and rating breakdown for property | No |
| `POST` | `/api/v1/reviews` | Submit tenant review and rating for completed stay | Yes |

---

### 9. Favorites

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/favorites` | Retrieve user's saved favorite property listings | Yes |
| `POST` | `/api/v1/favorites/{propertyId}` | Add property to favorites | Yes |
| `DELETE` | `/api/v1/favorites/{propertyId}` | Remove property from favorites | Yes |

---

### 10. Amenities & Locations

| Method | Endpoint | Description | Auth Required |
| :--- | :--- | :--- | :---: |
| `GET` | `/api/v1/amenities` | Fetch standard system amenities list | No |
| `GET` | `/api/v1/locations/cities` | Fetch popular and supported cities in Ukraine | No |
