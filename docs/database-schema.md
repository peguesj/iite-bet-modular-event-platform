# Database Schema for iite.bet (IBT)

**Version:** 0.1.0  
**Author:** Jeremiah Pegues <word@iite.bet>  
**Date:** 2024-06-01

---

## Overview

This document describes the core database schema for iite.bet, designed to support modular event creation, social features, ticketing, and commerce.

The schema is implemented in Supabase (PostgreSQL) and supports role-based access control, event management, RSVPs, comments, tickets, payments, and analytics.

---

## Entity Relationship Diagram

```mermaid
erDiagram
    USERS {
        uuid id PK
        varchar email
        varchar password_hash
        varchar role
        varchar display_name
        timestamp created_at
    }
    EVENTS {
        uuid id PK
        uuid host_id FK
        varchar title
        text description
        timestamp start_time
        timestamp end_time
        varchar visibility
        varchar location
        varchar venue_id FK
        timestamp created_at
    }
    VENUES {
        uuid id PK
        varchar name
        varchar address
        varchar city
        varchar state
        varchar country
        varchar postal_code
        varchar phone
        varchar website
    }
    RSVPS {
        uuid id PK
        uuid event_id FK
        uuid user_id FK
        varchar status
        timestamp created_at
    }
    COMMENTS {
        uuid id PK
        uuid event_id FK
        uuid user_id FK
        text content
        uuid parent_comment_id FK NULL
        timestamp created_at
    }
    TICKETS {
        uuid id PK
        uuid event_id FK
        varchar type
        numeric price
        int quantity
        int sold
        timestamp created_at
    }
    PAYMENTS {
        uuid id PK
        uuid ticket_id FK
        uuid user_id FK
        numeric amount
        varchar status
        timestamp created_at
    }
    AFFILIATE_CODES {
        uuid id PK
        uuid promoter_id FK
        varchar code
        numeric commission_rate
        timestamp created_at
    }
    ANALYTICS {
        uuid id PK
        uuid event_id FK
        int views
        int rsvps
        int ticket_sales
        timestamp recorded_at
    }

    USERS ||--o{ EVENTS : hosts
    USERS ||--o{ RSVPS : attends
    USERS ||--o{ COMMENTS : writes
    USERS ||--o{ PAYMENTS : makes
    USERS ||--o{ AFFILIATE_CODES : owns
    EVENTS ||--o{ RSVPS : has
    EVENTS ||--o{ COMMENTS : has
    EVENTS ||--o{ TICKETS : offers
    TICKETS ||--o{ PAYMENTS : receives
    EVENTS ||--o{ ANALYTICS : tracks
    VENUES ||--o{ EVENTS : hosts
    COMMENTS ||--o| COMMENTS : replies_to
```

---

## Notes

- `USERS.role` supports roles: Host, Artist, Promoter, Moderator, Attendee.
- `EVENTS.visibility` supports: public, private, RSVP-only.
- `COMMENTS.parent_comment_id` allows threaded comments.
- `AFFILIATE_CODES` link promoters to commission structures.
- `ANALYTICS` stores aggregated event metrics.

---

## Next Steps

- Implement Supabase migrations for this schema.
- Setup role-based policies in Supabase.
- Integrate schema with Next.js frontend.
