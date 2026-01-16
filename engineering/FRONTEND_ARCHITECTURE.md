# Tetra Frontend Architecture Reference

> **Purpose**: This document provides AI agents and engineers with a structural map of Tetra's frontend applications to inform routing decisions, feature placement, and engineering recommendations.

---

## Overview

Tetra's frontend is a **Next.js application** using file-based routing. The codebase serves three distinct user types through separate application areas:

| Application | Base Path | User Type | Primary Purpose |
|-------------|-----------|-----------|-----------------|
| Onboarding | `/onboarding/*` | Homeowners (new) | Quote generation and system selection |
| Homeowner Portal | `/homeowner/*` | Homeowners (existing) | Job tracking and proposal management |
| Contractor Portal | `/co/*` | Contractors | Job management and quoting |

---

## Application: Onboarding

**User**: Prospective homeowners  
**Purpose**: Customer acquisition funnel for HVAC quotes and system purchases  
**Entry Point**: `/onboarding`

### Route Structure

```
/onboarding
├── /                        → OnboardingLandingPage
├── /contact-info            → ContactInfoPage
├── /welcome                  → OnboardingWelcomePage
├── /confirm-home-info        → ConfirmHomeInfoPage
├── /your-home-info           → OnboardingV2Page
├── /questions-form           → QuestionsFormPage
├── /product-listing          → ProductListingPage
├── /product-details          → ProductDetailsPage
├── /add-ons                  → AddOnsPage
├── /review                   → ReviewPage
├── /deposit                  → DepositPage
├── /success                  → SuccessPage
├── /chat-with-an-expert      → ChatWithExpertPage
└── /out-of-territory         → OutOfTerritoryPage
```

### Flow Sequence

1. **Lead Capture**: `landing` → `contact-info` → `welcome`
2. **Home Qualification**: `confirm-home-info` → `your-home-info` → `questions-form`
3. **Product Selection**: `product-listing` → `product-details` → `add-ons`
4. **Conversion**: `review` → `deposit` → `success`
5. **Escape Hatches**: `chat-with-an-expert`, `out-of-territory`

### Engineering Notes

- This is the highest-traffic customer-facing flow
- Changes here directly impact conversion metrics
- `out-of-territory` handles geographic exclusions gracefully

---

## Application: Homeowner Portal

**User**: Existing customers with active or past jobs  
**Purpose**: Job visibility, proposal review, and timeline tracking  
**Entry Point**: `/homeowner` (redirects to `/homeowner/jobs`)

### Route Structure

```
/homeowner
├── /                        → Redirect to /jobs
├── /login                   → LoginPage
├── /jobs                    → JobsListPage
├── /canceled                → CanceledJobsPage
├── /summary/[proposalId]    → ProposalSummaryPage
└── /timeline/[proposalId]   → ProposalTimelinePage
```

### Dynamic Routes

| Parameter | Type | Description |
|-----------|------|-------------|
| `[proposalId]` | string | Unique identifier for a customer proposal |

### Engineering Notes

- Authenticated routes require login
- `/summary/[proposalId]` and `/timeline/[proposalId]` are the primary job detail views
- Consider this portal when adding customer-facing job status features

---

## Application: Contractor Portal

**User**: Contracted HVAC professionals  
**Purpose**: Job assignment, quoting, and completion tracking  
**Entry Point**: `/co/dashboard` (redirects to `/co/dashboard/jobs`)

### Route Structure

```
/co
└── /dashboard
    ├── /                    → Redirect to /jobs
    ├── /login               → LoginPage
    ├── /jobs                → ActiveJobsPage
    └── /jobs/completed      → CompletedJobsPage
```

### Engineering Notes

- All routes nested under `/co/dashboard`
- Contractor-specific UI patterns and terminology
- Job state management differs from homeowner view

---

## Standalone Pages

**Purpose**: Shareable documents and utility pages accessed via direct links

| Route | Purpose | Typical Access Pattern |
|-------|---------|------------------------|
| `/proposal` | Proposal document view | Shared link to customers |
| `/quote` | Quote document view | Shared link to customers |
| `/invoice` | Invoice document view | Payment/billing links |
| `/work-order` | Work order document view | Contractor reference |
| `/upload` | File upload utility | Internal/customer uploads |
| `/dmsproposal` | DMS proposal integration | System integration |

### Engineering Notes

- These pages are typically accessed via parameterized URLs (query strings or tokens)
- Design for standalone viewing without portal navigation context
- Consider print/PDF styling for document pages

---

## Error Pages

**Purpose**: Graceful handling of edge cases and system errors

| Route | Trigger Condition |
|-------|-------------------|
| `/error` | Generic application errors |
| `/far-out-error` | Scheduling too far in advance |
| `/fuel-assistance-error` | Fuel assistance program conflicts |
| `/home-type-error` | Unsupported home/property type |

### Engineering Notes

- Error pages should provide clear next steps
- Consider analytics tracking on error page visits
- These represent known business logic exclusions, not just system failures

---

## Technical Patterns

### File Structure Convention

```
pages/
├── [app-area]/
│   ├── index.tsx           → Landing/redirect page
│   ├── [feature]/
│   │   └── index.tsx       → Feature page
│   └── [feature]/[param].tsx → Dynamic route
├── _app.tsx                → App wrapper (not a route)
└── _document.tsx           → Document wrapper (not a route)
```

### Dynamic Route Parameters

| Parameter | Used In | Description |
|-----------|---------|-------------|
| `[proposalId]` | Homeowner Portal | Links proposal data to customer views |
| `[quoteId]` | Various | Quote reference identifier |
| `[contractorId]` | Contractor Portal | Contractor account identifier |
| `[condenserId]` | Product pages | HVAC equipment identifier |

### Routing Decision Framework

When determining where to place new features:

1. **Who is the user?**
   - New customer → `/onboarding/*`
   - Existing customer → `/homeowner/*`
   - Contractor → `/co/*`

2. **Is it a shareable document?**
   - Yes → Standalone page at root level
   - No → Nested in appropriate portal

3. **Does it require authentication?**
   - Customer auth → `/homeowner/*`
   - Contractor auth → `/co/*`
   - No auth → `/onboarding/*` or standalone

4. **Is it an error/edge case handler?**
   - Yes → Create dedicated error page with clear recovery path

---

## Recommendations for AI Agents

When making engineering recommendations for this codebase:

1. **Respect the three-portal architecture** — Don't mix user contexts
2. **Follow existing naming conventions** — kebab-case routes, PascalCase components
3. **Consider the user journey** — Onboarding is sequential; portals are dashboard-based
4. **Check for existing patterns** — Similar features likely exist; reference them
5. **Document pages are special** — They're standalone, printable, and shareable

---

*Last Updated: January 2025*
