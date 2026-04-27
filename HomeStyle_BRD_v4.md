# HomeStyle — Business Requirements Document
**Premium Design Furniture | Custom Web Application | v4.0**

| Field | Value |
|---|---|
| Version | 4.0 |
| Status | Draft — Pending Client Approval |
| Date | April 2026 |
| Build Type | Custom Web Application (Full-Stack) |
| Target Market | United States & Europe |
| Author | Product Owner / BA |
| Classification | Confidential |

> **Change Control:** All post-approval changes must be submitted via a Change Request (CR) form, reviewed by the Project Sponsor, and versioned incrementally.

---

## Table of Contents

1. [Document Revision History](#1-document-revision-history)
2. [Executive Summary & Business Case](#3-executive-summary--business-case)
3. [Business Objectives & Goals](#4-business-objectives--goals)
4. [Platform Decision — Custom Web Application](#5-platform-decision--custom-web-application)
5. [Target Market & User Personas](#6-target-market--user-personas)
6. [User Roles](#7-user-roles)
7. [Project Scope & Phase Roadmap](#8-project-scope--phase-roadmap)
8. [Open Business Decisions](#9-open-business-decisions-pending-client-sign-off)
9. [Client Responsibilities & Deliverables](#10-client-responsibilities--deliverables)
10. [Business Assumptions & Constraints](#11-business-assumptions--constraints)
11. [Key Business Processes](#12-key-business-processes)
12. [Business Capabilities](#13-business-capabilities-high-level-requirements)
13. [System Email & Notification Triggers](#14-system-email--notification-triggers)
14. [Returns & Exchange — Business Rules](#15-returns--exchange--business-rules)
15. [Non-Functional Requirements](#16-non-functional-requirements)
16. [Time Constraints & Validation Rules](#17-time-constraints--validation-rules)
17. [Domain-Specific Considerations](#18-domain-specific-considerations)
18. [Key Performance Indicators](#19-key-performance-indicators-kpis)
19. [Stakeholder Register & RACI](#20-stakeholder-register--raci)
20. [Approvals](#21-approvals)

---

## 1. Document Revision History

| Version | Date | Description | Author |
|---|---|---|---|
| 1.0 | Apr 2026 | Initial Draft | PO / BA |
| 2.0 | Apr 2026 | Full revision: GDPR gaps, Returns flow, Guest Checkout decision, Currency model, Phase roadmap | PO / BA |
| 3.0 | Apr 2026 | Platform changed to custom-built web application. Tech stack section added. | PO / BA |
| 4.0 | Apr 2026 | Domain repositioned to premium design furniture (B2C + B2B trade). Personas, product configuration model, shipping, pricing, and B2B portal scope updated. Time constraints fully integrated (TC-01 to TC-38). | PO / BA |

---

## 3. Executive Summary & Business Case

### 3.1 The Business Opportunity

The global premium furniture market is projected to exceed $50 billion by 2028, driven by a growing consumer segment that demands both superior craftsmanship and a seamless digital purchasing experience. Design-conscious buyers, interior architects, and corporate workspace planners are increasingly researching, configuring, and purchasing premium furniture entirely online — yet most heritage design brands remain anchored in showroom-first, offline operations.

HomeStyle occupies a strong position: an established catalogue of award-winning design pieces, a network of certified trade partners, and deep supplier relationships with European craftmakers. The missing layer is a modern, conversion-optimised digital platform that can serve both discerning end consumers and professional trade buyers at scale.

### 3.2 Why Custom-Built

| Dimension | Platform (e.g. Shopify) | Custom Web Application |
|---|---|---|
| Ownership | Platform-owned; subject to pricing & policy changes | 100% client-owned IP; no vendor lock-in |
| Product Configuration | Limited variant depth (max 3 options); no nested configurator | Unlimited configuration depth for complex design products |
| B2B Trade Portal | Requires third-party apps; limited capability | Native B2B portal with trade pricing, project wishlists, and VAT exemption |
| Data Ownership | Data stored on platform; limited export control | All data owned and controlled by the client |
| Future Roadmap | Phase 2+ features constrained by platform | Phase 2/3 features fully buildable as planned |

### 3.3 Business Impact & ROI Narrative

| Metric | Current State (Offline / Showroom) | Target State (12 Months Post-Launch) |
|---|---|---|
| Revenue Channel | 100% offline / showroom & trade reps | 35–45% from online channel (B2C + B2B) |
| Customer Reach | Local / regional; trade via rep network | Nationwide US + key EU markets; direct trade self-serve |
| Order Processing | Manual (phone / email / showroom) | Fully automated via custom admin panel; trade orders self-served |
| Product Configuration | Configured in showroom with sales rep | Self-serve digital configurator for all variant combinations |
| Customer Data | Fragmented / unstructured across reps | Unified in proprietary database — client-owned |

### 3.4 Proposed Solution

A two-sided custom web application built to serve premium design furniture at scale:

- **Customer Storefront:** SSR web application (Next.js) delivering a premium shopping experience — collection-based navigation, high-fidelity product visualisation, deep product configurator (material, finish, fabric grade, base, size), multi-currency checkout with time-enforced inventory holds, white-glove delivery, order tracking, and a full returns portal.
- **Trade Portal:** A dedicated B2B section of the storefront allowing verified interior designers, architects, and corporate buyers to access trade pricing, project wishlists, specification sheets, and VAT exemption checkout — available from Phase 1 as a core differentiator.
- **Admin & Operations Panel:** Custom-built back-office web application for product catalogue, orders, inventory, shipping, customers, trade account approvals, promotions, content, and reporting — with granular role-based access and configurable SLA timers.
- **REST API Layer:** Versioned RESTful API (`/api/v1/`) serving all applications, with Stripe payment integration, shipping carrier APIs, transactional email service, and time-based background job scheduler.

---

## 4. Business Objectives & Goals

| # | Objective | Success Metric |
|---|---|---|
| BO-01 | Establish a fully owned online sales channel for both B2C and trade buyers | Online channel contributes ≥35% of total revenue within 12 months |
| BO-02 | Reach US and European customers and trade professionals | 8,000 registered customers + 500 verified trade accounts within 6 months |
| BO-03 | Automate order & fulfillment operations | Order processing time reduced by 60% vs. manual baseline; SLA compliance ≥95% |
| BO-04 | Deliver a superior digital experience befitting a premium design brand | CSAT ≥4.7/5; cart abandonment ≤60%; refund resolution ≤12 business days |
| BO-05 | Enable data-driven business decisions | Weekly reports consumed by management via admin dashboard |
| BO-06 | Achieve full legal compliance (GDPR / CCPA) | Zero violations; GDPR erasure processed within 30 days |
| BO-07 | Own all customer and business data outright | All data stored in client-controlled database; zero vendor data lock-in |
| BO-08 | Establish and grow a verified trade community | Trade portal active from launch; trade revenue ≥20% of online channel by Month 12 |

---

## 5. Platform Decision — Custom Web Application

### 5.1 Decision Summary

Following evaluation of available e-commerce platforms, the client has selected a fully custom web application. All functionality will be designed, developed, tested, and deployed as proprietary software. There is no dependency on any e-commerce SaaS platform.

> **DECISION CONFIRMED:** Fully custom-built web application. Final and approved by Project Sponsor. All downstream documents (SRS, API Spec, Architecture Diagram, Database Schema) reflect a custom-build approach.

### 5.2 Recommended Technology Stack

| Layer | Recommended Technology | Rationale |
|---|---|---|
| Frontend (Storefront + Trade Portal) | Next.js (React) + TypeScript | SSR/SSG for SEO; shared component library across B2C and trade views |
| Product Configurator | React + Three.js (r128) or custom SVG | Interactive material/finish selector with real-time visual preview |
| Admin Panel | React + TypeScript (SPA) | Rich interactive UI; role-based routing; trade account approval workflows |
| Backend / API | Node.js + NestJS | JavaScript full-stack consistency; strong ORM support; modular architecture for B2C + B2B logic |
| Database (Primary) | PostgreSQL | ACID compliance; strong relational model for complex product configurations, orders, trade accounts |
| Database (Cache) | Redis | Session storage; cart caching; inventory hold TTL keys; rate limiting |
| File / Image Storage | AWS S3 + CloudFront CDN | Scalable object storage; global CDN for product images, finish swatches, specification PDFs |
| Payment Processing | Stripe (REST API) | Multi-currency; B2B invoicing via Stripe; VAT-exempt checkout for trade accounts |
| Search Engine | Elasticsearch or Algolia | Full-text search with faceted filters across complex product attributes and finish options |
| Tax Calculation | Stripe Tax or TaxJar API | US sales tax by state + EU VAT calculation; VAT exemption flag for verified trade accounts |
| Shipping API | EasyPost or ShipEngine | Multi-carrier; white-glove delivery partners; freight carriers for large items |
| Authentication | JWT + OAuth 2.0 | Stateless API auth; social login; separate trade account verification flow |
| Hosting / Infra | AWS (ECS / RDS / S3 / CloudFront) | Production-grade infrastructure; auto-scaling; managed DB; global CDN |

---

## 6. Target Market & User Personas

### 6.1 Target Market

| Dimension | United States | Europe (EU + UK) |
|---|---|---|
| Currency | USD ($) | EUR (€) and GBP (£) |
| Tax Model | Sales tax added at checkout by state | VAT included in displayed price |
| Data Privacy Law | CCPA (California) | GDPR — full compliance required |
| Payment Methods | Visa, MC, Amex, Apple Pay, Google Pay | Visa, MC, iDEAL (NL), SEPA Direct Debit, Apple Pay |
| B2B Buyers | Interior designers, corporate workspace buyers, architects | Interior architects, contract furniture specifiers, hospitality buyers |

### 6.2 Persona 1 — The Design Enthusiast (Primary B2C, US)

> **Alexandra, 38 — Creative Director, New York, NY**
> Purchase frequency: 1–3×/year | Budget: $2,000–$10,000/order | Channel: Design publications, Instagram, direct referral

- **Goals:** Curate a living space that reflects her design sensibility. Wants to explore material combinations and verify quality before committing to high-value purchases.
- **Pain Points:** Fears receiving a piece that looks different in person. Frustrated by vague lead times on made-to-order items. Expects white-glove delivery as standard, not an upsell.
- **Key Needs:** High-fidelity material swatches, deep product configurator, confirmed lead time at checkout, white-glove delivery, verified reviews from design professionals.

### 6.3 Persona 2 — The Interior Architect (Primary B2B, EU)

> **Pieter, 44 — Interior Architect, Amsterdam, Netherlands**
> Purchase frequency: 8–15 orders/year | Budget: €5,000–€80,000/project | Channel: Trade network, direct relationship

- **Goals:** Specify and order furniture for multiple concurrent client projects. Needs to manage project wishlists, access specification PDFs and CAD files, and order at trade pricing with VAT exemption.
- **Pain Points:** Wastes time managing orders across email and phone. Cannot get VAT-exempt invoices without contacting a rep. Needs reliable lead times to coordinate with construction schedules.
- **Key Needs:** Verified trade account, project wishlist management, trade pricing, VAT-exempt checkout, specification PDF download, account manager contact, reliable lead time display.

### 6.4 Persona 3 — The Corporate Workspace Buyer (B2B, US)

> **Marcus, 51 — Head of Real Estate & Facilities, San Francisco, CA**
> Purchase frequency: 2–4 large projects/year | Budget: $50,000–$500,000/project | Channel: Trade referral, direct outreach

- **Goals:** Furnish new office locations with premium pieces that communicate brand identity. Needs bulk pricing, purchase order support, and delivery coordination across multiple sites.
- **Key Needs:** Trade account, bulk order quoting, PO-based payment option, multi-site delivery coordination, dedicated account manager, formal invoicing.

---

## 7. User Roles

| Role | Description & Permissions |
|---|---|
| Guest / Visitor | Unauthenticated user. Can browse products, search, use product configurator, view product details, check delivery availability, view reviews, and add items to a session-based cart. Guest cart preserved via server-side session token (TC-12: 30-day TTL). |
| Registered Customer | Authenticated via JWT. All guest permissions plus: persistent cart, order history, order tracking, wishlist, returns portal, post-purchase reviews, and account management. |
| Verified Trade Account | Authenticated B2B user (interior designer, architect, corporate buyer). All customer permissions plus: trade pricing visible at product and cart level, VAT-exempt checkout, project wishlist management, specification PDF download (full resolution), account manager contact, formal invoice generation. |
| Super Admin | Full access to all admin panel modules including trade account approval/rejection, user management, product catalogue, orders, inventory, shipping, promotions, reports, CMS, roles & permissions, and system settings. |
| Admin — Operations | Role-based access: orders, inventory, shipping, returns, and customer management. No access to financial configuration or system settings. |
| Admin — Content | Role-based access: product catalogue, collections, CMS pages, blog, email templates, promotions, and review moderation. No access to orders or financial data. |
| Admin — Trade | Role-based access: trade account applications (approve/reject), trade pricing management, account manager assignment, and B2B order oversight. |

---

## 8. Project Scope & Phase Roadmap

### 8.1 Phase Delivery Roadmap

> **SCOPE BOUNDARY:** Features not listed under Phase 1 are explicitly deferred. Any request to include deferred features requires a formal Change Request (CR) and re-estimation.

| Phase | Timeline | Features Included | Status |
|---|---|---|---|
| Phase 1 — MVP | Months 1–6 | Full custom storefront + admin panel, product catalogue, deep product configurator, cart with time-enforced inventory holds, checkout with SLA timers, Stripe payments, multi-currency, trade portal (verified trade accounts, trade pricing, VAT exemption, project wishlists, spec PDF download), order management with SLA tracking, customer accounts (JWT auth), wishlist, ratings & reviews, returns & exchange portal, shipping config, discount codes, CMS pages, GDPR consent, system email notifications, admin dashboard & reports, role-based access. | In Scope |
| Phase 2 — Growth | Months 7–12 | WebAR / 3D room visualiser, loyalty program, multi-language (DE, FR, IT), AI product recommendations, live chat with trade account managers, CAD file download (DWG/DXF). | Deferred |
| Phase 3 — Scale | Year 2+ | Native mobile app, multi-vendor marketplace, BNPL payments, ERP integration, advanced trade analytics, custom project mood boards. | Future |

### 8.2 Phase 1 — In-Scope: Customer Storefront

- User registration and login (email/password + social OAuth via Google and Facebook)
- Email verification flow and password reset
- Product browsing: collection-based category navigation (by room, by designer, by material, by style)
- Full-text product search with auto-suggest, filters (price, material, finish, size, colour, rating, lead time, availability), and sorting
- Product detail page: multi-image gallery, material/finish swatch selector, dimension table (inches + cm), finish and fabric grade configurator, lead time display, specification PDF download (TC-09: 15-min presigned URL), delivery availability checker, reviews
- Shopping cart: add/remove/update quantity and configuration; session-based guest cart merged on login
- Wishlist: save configured products, share via link, move items to cart
- Guest checkout (pending OD-01)
- Multi-step checkout: address → delivery method selection → payment
- White-glove delivery as default option for all large furniture; standard delivery for accessories
- Lead time confirmed and displayed at checkout for each configured item
- Multi-currency display and checkout (USD / EUR / GBP)
- US sales tax via Stripe Tax / TaxJar; EU VAT-inclusive pricing with VAT breakdown at checkout
- Payment via Stripe: credit/debit cards, Apple Pay, Google Pay, iDEAL (NL), SEPA Direct Debit (EU)
- Discount code input at checkout
- Order confirmation page and confirmation email
- Order history and order detail view with lead time status
- Real-time shipment tracking via carrier tracking link
- Returns & exchange request portal
- Post-purchase product reviews (one per customer per product, post-delivery unlock)
- GDPR cookie consent banner and preference centre
- CMS-managed pages: About Us, Designer Profiles, FAQ, Delivery Info, Returns Policy, Privacy Policy, Terms & Conditions

### 8.3 Phase 1 — In-Scope: Trade Portal

- Trade account application form (company name, profession, tax/VAT registration number, portfolio URL)
- Admin-reviewed trade account approval workflow (TC-TRA-01: review within 2 business days)
- Trade pricing displayed at product and cart level once authenticated as verified trade account
- VAT-exempt checkout flow for verified EU trade accounts with valid VAT registration
- Project wishlist: named wishlists per project, shareable with clients, moveable to cart
- Specification PDF download: full-resolution PDFs including technical drawings, material specifications, and finish options
- Formal invoice generation with company name, VAT number, and purchase order reference
- Account manager contact details visible in trade account dashboard

### 8.4 Phase 1 — In-Scope: Admin Panel

- Secure admin login with mandatory 2FA (TOTP)
- Dashboard: real-time KPI cards (today's revenue, order count, new customers, low-stock alerts, pending trade applications), sales trend chart
- Product management: full CRUD, multi-image upload to S3, configurator option management (materials, finishes, fabric grades, base options), per-currency pricing, lead time per configuration, stock levels, specification PDF upload
- Trade account management: review applications, approve/reject with reason, assign account manager, manage trade pricing tiers
- Order management: list/search/filter, status updates, lead time tracking, shipment creation, packing slip print, invoice download
- Returns management: view requests, approve/reject, issue Stripe refund, track return status
- Customer management: view/search, order history, activate/deactivate, trade account status
- Promotions: discount code CRUD — percentage and fixed, trade-only codes, usage limits, expiry
- Shipping configuration: zones, flat-rate and weight-based rates, white-glove partner management
- Review moderation: approve, reject, flag, or respond to reviews before storefront publication
- Reports: revenue by date range/product/category, trade vs consumer split, top-selling configurations — exportable to PDF and Excel

### 8.5 Out of Scope — Phase 1

- WebAR / 3D room placement (Phase 2)
- CAD file download — DWG/DXF (Phase 2)
- Multi-language storefront — German, French, Italian (Phase 2)
- Loyalty / rewards program (Phase 2)
- Native iOS / Android mobile app (Phase 3)
- ERP / accounting software integration (Phase 3)
- Custom project mood boards (Phase 3)

---

## 9. Open Business Decisions (Pending Client Sign-Off)

> **ACTION REQUIRED:** All items below must be confirmed by the client within 2 weeks of BRD approval. Unresolved decisions will block SRS sign-off.

| # | Decision | Options | Dev Impact | Owner |
|---|---|---|---|---|
| OD-01 | Guest Checkout | A) Allow checkout without account / B) Require registration before checkout | Option A: guest order model in DB; session cart merge logic required | Business Owner |
| OD-02 | Currency / Exchange Rate Model | A) Live rates via Fixer.io API (auto-converted) / B) Admin manually sets fixed prices per currency | Option A: scheduled rate-sync job required | Biz Owner + Finance |
| OD-03 | Made-to-Order Pre-Order Handling | A) Allow pre-orders with confirmed lead time shown / B) Show 'Out of Stock / Contact Us' only | Option A: pre-order inventory state + lead time queue required | Business Owner |
| OD-04 | Discount Code Stacking | A) One code per order only / B) Codes can stack with sale prices / C) Codes cannot stack with sale prices | Determines promotion engine logic; Option B most complex | Biz Owner + Marketing |
| OD-05 | Trade Account Pricing Model | A) Single trade tier (fixed % discount) / B) Multiple tiers (designer / architect / corporate) with different discounts | Option B: pricing tier table + tier assignment logic required | Biz Owner + Finance |
| OD-06 | VAT Exemption Verification | A) Self-declared VAT number (admin verified manually) / B) Automated VAT number validation via EU VIES API | Option B: VIES API integration required at trade account application | Biz Owner + Legal |
| OD-07 | Return Window Duration | Confirm exact return eligibility window (default: 30 days from delivery — TC-19) | Affects server-side eligibility calculation and all customer-facing return messaging | Biz Owner + Legal |
| OD-08 | Made-to-Order Return Policy | A) No returns on configured / made-to-order items / B) Exchange or store credit only / C) Standard 30-day policy applies | Determines return eligibility logic per order type | Biz Owner + Legal |
| OD-09 | Lead Time Display | A) Show range (e.g. '8–12 weeks') from product DB / B) Real-time lead time via supplier API / C) 'Contact us for lead time' for bespoke items | Option B: supplier API integration per product category required | Biz Owner + Ops |
| OD-10 | Admin-Configurable Time Values | Confirm which TC values are hard-coded vs. configurable via System Settings panel | Configurable values require a System Settings module in admin panel | Biz Owner + Dev Lead |

---

## 10. Client Responsibilities & Deliverables

| # | Deliverable | Specification | Required By |
|---|---|---|---|
| CR-01 | Product Photography | Min. 8 high-res images per product (all angles, lifestyle, detail shots). Minimum 3000×3000px, JPEG/WebP. Material swatch photography per finish option. | Month 2 |
| CR-02 | Finish & Material Swatch Images | High-resolution swatch images per material and finish option (min. 500×500px, on white background). Required for product configurator. | Month 2 |
| CR-03 | Specification PDFs | One technical PDF per product model: dimensions (mm + inches), material spec, finish options, weight, assembly instructions. Filename matching SKU code. | Month 3 |
| CR-04 | Product Data CSV | Full catalogue: product name, designer credit, description, SKU, price per currency, dimensions, weight, materials, configuration options, lead times per configuration, stock qty. | Month 1 |
| CR-05 | Stripe Account | Active Stripe account with identity verification. Client provides restricted API keys to dev team. | Month 2 |
| CR-06 | Cloud Hosting Account | AWS account created and dev team granted programmatic access. S3 buckets, RDS instance, and domain configured. | Week 2 |
| CR-07 | Lead Time Data | Confirmed lead time ranges per product/configuration from supplier agreements. Required for checkout lead time display (OD-09). | Month 2 |
| CR-08 | Trade Account Criteria | Defined criteria for trade account eligibility (profession types, required documentation, pricing tier assignment rules per OD-05). | Month 2 |
| CR-09 | Legal Documents | Privacy Policy, T&Cs, Returns Policy, Trade Account T&Cs, Cookie Policy — drafted and approved by Legal before launch. | Month 4 |
| CR-10 | EU VAT & US Tax Config | VAT registration numbers per EU country. List of US states with sales tax nexus. | Month 2 |
| CR-11 | Shipping & White-Glove Partners | Active carrier accounts + white-glove delivery partner details with service zones and API credentials. | Month 3 |
| CR-12 | Brand Guidelines | Logo (SVG + PNG), brand colours, typography, visual identity standards, photography style guide. | Week 2 |
| CR-13 | Designer Credits & IP Rights | Confirmation of rights to display designer names and biographies on product pages and designer collection pages. | Month 2 |

---

## 11. Business Assumptions & Constraints

### 11.1 Assumptions

- Physical warehouse, inventory management, and supplier relationships are pre-established and operational before launch.
- Products are managed with unique SKU codes that incorporate configuration variant codes (e.g. `HS-CHAIR-001-WAL-BLK` for walnut base, black fabric).
- Primary language of the platform is English for all Phase 1 markets.
- Stripe handles PCI DSS compliance for all card payment processing.
- AWS will be used as the primary cloud infrastructure provider.
- Lead times for made-to-order items are confirmed by operations and stored in the product DB at launch.
- Trade account verification is a manual admin process in Phase 1 (no automated VIES API unless OD-06 confirmed).
- All time constraint default values in Section 17 are working defaults pending client confirmation per OD-10.

### 11.2 Constraints

- **Timeline:** Phase 1 (MVP) launch target is 6 months from project kickoff.
- **Compliance:** GDPR and CCPA compliance are non-negotiable prerequisites for EU and California customer data processing.
- **Security:** PCI DSS compliance mandatory. No card data stored on application servers. Admin 2FA mandatory.
- **Accessibility:** WCAG 2.1 Level AA required across all customer-facing pages.
- **API Design:** REST API versioned from day one (`/api/v1/`) for future mobile app and third-party integrations.

### 11.3 Risk Register

| Risk | Likelihood | Impact | Mitigation Strategy | Owner |
|---|---|---|---|---|
| Scope creep delaying Phase 1 MVP | High | High | Formal CR process; all new requests go to Phase 2 backlog. | PO / BA |
| Client delays providing product assets (swatches, spec PDFs, lead time data) | High | High | Hard deadlines in Section 10; block dependent sprints until assets confirmed. | PO / BA |
| Made-to-order lead time data inaccurate at launch | Medium | High | CR-07 hard deadline; ops team sign-off on lead times before go-live. | Ops Team |
| Trade account verification volume exceeds admin capacity | Medium | Medium | TC-TRA-01: 2-day SLA; auto-escalation to Super Admin. Batch approval UI for admin. | Admin — Trade |
| Inventory oversell during high-traffic checkout | Medium | High | TC-13: Redis TTL inventory hold enforced server-side. Load tested pre-launch. | Dev Lead |
| GDPR non-compliance (EU storefront) | Low | Critical | Legal review of cookie consent, privacy policy, social login legal basis before EU launch. | Legal |
| Data breach or payment security incident | Low | Critical | PCI DSS via Stripe; TLS 1.3; no card data server-side; TC-03/TC-11 admin security. | Dev Lead |

---

## 12. Key Business Processes

### 12.1 Customer Purchase Journey (To-Be — with Timer Events)

| # | Stage | Process Detail + Time Constraints |
|---|---|---|
| 1 | Discovery | Customer arrives via organic search, paid ad, design publication, or trade referral. Next.js serves SSR product/collection pages for SEO. |
| 2 | Product Evaluation | Views product detail page: high-fidelity images, material/finish swatch selector, dimension table, lead time for selected configuration, specification PDF (TC-09: 15-min presigned URL), delivery checker, verified reviews. |
| 3 | Product Configuration | Customer selects material, finish, fabric grade, base type, and size. System updates price, lead time, and stock availability in real time per configuration. Configured item added to cart or wishlist. |
| 4 | Cart & Authentication | Item added to cart. Guest cart: Redis session (TC-12: 30-day TTL). Registered: persisted in DB. Cart is NOT an inventory reservation at this stage. |
| 5 | Checkout — Address [TC-13 starts] | On checkout page load: system creates `inventory_hold` in Redis (TC-13: 15-min TTL). Countdown shown at ≤5 minutes. Lead time per configured item displayed and locked. |
| 6 | Checkout — Payment [TC-14: 30-min hard expiry] | Stripe PaymentIntent created server-side. Customer enters payment via Stripe Elements. Discount code re-validated server-side. Tax calculated. TC-15: 30-second Stripe API timeout. TC-16: idempotency key prevents duplicate charges. |
| 7 | Order Confirmation | Payment confirmed via webhook. Order record created. Inventory decremented. Cart cleared. Confirmation email dispatched with confirmed lead time (TC-29: retry policy). |
| 8 | Fulfillment [TC-17: 4-hr SLA] | Admin confirms order. For in-stock items: proceed to packing. For made-to-order: order placed with supplier; lead time clock starts. |
| 9 | Dispatch [TC-18: 48-hr SLA for in-stock] | Admin enters shipment details. White-glove delivery partner notified. Shipping confirmation email sent with tracking link. For made-to-order: dispatch SLA starts from confirmed production completion. |
| 10 | Post-Purchase | Order status: Delivered. TC-24: 7 days later, review prompt sent. TC-19: Return portal available for 30 days (standard items). Made-to-order return eligibility per OD-08. |

### 12.2 Trade Account Application & Approval Process

- Prospective trade buyer submits application: company name, profession type, tax/VAT registration number, portfolio or company website URL, intended use.
- System creates trade application record with status `pending_review`. Admin — Trade receives dashboard alert and email.
- **TC-TRA-01:** Admin must review within 2 business days. SLA breach triggers Super Admin escalation.
- If approved: trade account activated. Welcome email sent with trade pricing access and portal onboarding guide.
- If rejected: rejection email with reason. Customer can re-apply with additional documentation.
- Verified trade accounts see trade pricing on all product pages and in cart immediately on next login.

### 12.3 Returns & Exchange Process (with Timer Events)

- Customer navigates to 'My Orders'. 'Request Return' button visible only if within TC-19: 30-day window from delivery (server-side validated, not just UI-gated). Made-to-order items subject to OD-08 policy.
- Customer selects return reason and uploads photos. Eligibility re-validated server-side at submission.
- Admin receives dashboard alert. **TC-21:** Admin must review within 2 business days.
- If approved: admin sends return label or schedules white-glove pickup. **TC-20:** Customer must arrange return within 14 days of approval.
- Admin confirms receipt and inspects condition. **TC-22:** Admin must initiate Stripe refund within 1 business day of marking 'Item Received'.
- **TC-30:** If `refund.succeeded` webhook not received within 24 hours, admin alert triggered.
- **TC-23:** Total resolution target ≤12 business days from request to refund completed.

---

## 13. Business Capabilities (High-Level Requirements)

### 13.1 Product Discovery & Browsing

| ID | Capability | Business Value + Time Constraints | Priority |
|---|---|---|---|
| BC-01 | Collection-based navigation (by room, designer, material, style) | Customers shop by design intent, not just category. Reduces drop-off for design-led buyers. | Critical |
| BC-02 | Full-text search with faceted filters including lead time and finish | Narrows complex catalogue instantly. Search index synced within 60 seconds of product update. | Critical |
| BC-03 | Rich product detail page with configurator | All purchase-decision info in one view. TC-09: Spec PDF via 15-min presigned URL. Lead time updates per configuration. | Critical |
| BC-04 | Delivery availability checker | Reduces checkout abandonment by confirming delivery feasibility (incl. white-glove zones) upfront. | High |
| BC-05 | Designer profile and collection pages | Communicates brand heritage and design provenance — critical for premium positioning. | High |

### 13.2 Purchase Experience

| ID | Capability | Business Value + Time Constraints | Priority |
|---|---|---|---|
| BC-06 | Frictionless checkout with lead time lock | TC-12/TC-13/TC-14/TC-15/TC-16. Lead time for each configured item confirmed and locked at checkout. | Critical |
| BC-07 | Multi-currency checkout (USD / EUR / GBP) | TC-16: 24-hour Stripe idempotency key prevents duplicate charges across currency contexts. | Critical |
| BC-08 | White-glove delivery as primary option | Differentiates HomeStyle at premium positioning; standard for large items, optional for accessories. | Critical |
| BC-09 | Trade portal — pricing, VAT exemption, project wishlists | Direct B2B revenue channel. Trade pricing visible post-authentication. VAT-exempt checkout for eligible accounts. | Critical |
| BC-10 | Discount code application | Server-side re-validation at checkout submission prevents race condition exploits. Trade-only codes supported. | High |

### 13.3 Order, Returns & Post-Purchase

| ID | Capability | Business Value + Time Constraints | Priority |
|---|---|---|---|
| BC-11 | Customer self-service order tracking with lead time status | Reduces support contacts. TC-29: email retry ensures delivery confirmation received. Lead time stage visible in order history. | Critical |
| BC-12 | Refund status visible in customer order view | Eliminates 'where is my refund?' tickets. TC-23: ≤12 business day total resolution target. | Critical |
| BC-13 | Returns & exchange request portal | TC-19: 30-day window enforced server-side. TC-20: 14-day ship-back deadline. TC-21: 2-day admin review SLA. Made-to-order policy per OD-08. | Critical |
| BC-14 | Verified post-purchase review system | TC-24: Prompt sent 7 days post-delivery. TC-25: 180-day submission window. TC-26: 48-hr moderation SLA. | High |

---

## 14. System Email & Notification Triggers

### 14.1 Customer-Facing Emails

| # | Trigger | Content Summary | TC Ref | Retry / Timing |
|---|---|---|---|---|
| E-01 | Registration — Email Verification | Welcome + verification link | TC-06 | Link expires 24h. Max 3 resend/hr. |
| E-02 | Password Reset | Secure reset link | TC-07 | Link expires 1h. Single use. |
| E-03 | Order Confirmation | Order #, items with configurations, totals, confirmed lead time | TC-29 | Sent immediately on payment. Retry 3× at 1 min, 1× at 15 min. |
| E-04 | Order Confirmed by Admin | Order confirmed; in production or being prepared | TC-17 | Sent when admin marks Confirmed. |
| E-05 | Shipping Confirmation | Carrier, tracking number, white-glove delivery window | TC-18, TC-29 | Sent immediately on Shipped status. |
| E-06 | Delivery Confirmed + Review Prompt | Delivered confirmation. Review prompt sent 7 days later. | TC-24 | Scheduled BullMQ job fires 7 days post-Delivered. |
| E-07 | Return Request Received | Return #XXXX received; under review | TC-21 | Sent within 5 min of submission. |
| E-08 | Return Approved + Instructions | Return authorised; label attached / white-glove pickup scheduled | TC-20 | Customer must return within TC-20: 14 days. |
| E-09 | Refund Initiated | Refund of $XX initiated; 5–10 business days | TC-22, TC-30 | Sent on admin Stripe refund trigger. |
| E-10 | Trade Account Approved | Trade account activated; trade pricing and portal access enabled | TC-TRA-01 | Sent on admin approval action. |
| E-11 | Trade Account Rejected | Application declined with reason; invitation to re-apply | — | Sent on admin rejection action. |
| E-12 | Wishlist Item Back in Stock | Item in wishlist now available | TC-28 | BullMQ hourly job. One notification per restock event. |

---

## 15. Returns & Exchange — Business Rules

> **PENDING DECISION:** Return window duration (default TC-19: 30 days) subject to client Legal confirmation per OD-07. Made-to-order return policy subject to OD-08. All values below use 30 days as the working default for standard items.

### 15.1 Eligibility Rules

- **TC-19:** Return window is 30 days from confirmed delivery date. 'Request Return' button hidden (not disabled) after window closes. Server-side validation re-checks window at submission to prevent race conditions.
- Standard in-stock items: eligible for full cash refund if unassembled and in original packaging.
- Assembled items: eligible for exchange or store credit only.
- Damaged on arrival: full refund or replacement regardless of assembly status. Photo upload required.
- Made-to-order / configured items: subject to OD-08 decision. Default working assumption: exchange or store credit only (no cash refund) unless item is faulty or does not match specification.
- Bespoke items (custom dimensions or custom fabric not in standard range): non-returnable unless faulty.

### 15.2 Return Reasons (Customer-Selectable)

- Damaged on arrival
- Wrong item or configuration received
- Item does not match product description or images
- Changed my mind
- Item arrived outside confirmed lead time window
- Other (free text field, required)

### 15.3 Return Status State Machine

| Status | Definition | Time Trigger |
|---|---|---|
| Return Requested | Customer submitted request. Pending admin review. | At submission (must be within TC-19: 30 days of delivery) |
| Under Review | Admin reviewing. SLA: TC-21: 2 business days. | At request creation |
| Return Approved | Admin approved. Return instructions or white-glove pickup scheduled. | On admin approval action |
| Return Rejected | Admin rejected with reason. | On admin rejection action |
| Item in Transit | Customer shipped item back or pickup scheduled. | Within TC-20: 14 days of approval |
| Item Received | Admin confirmed receipt at warehouse. | On admin confirmation action |
| Refund Initiated | Stripe refund triggered by admin. | Within TC-22: 1 business day of Item Received |
| Refund Completed | Stripe `refund.succeeded` webhook received. | TC-30: alert if webhook not received within 24h |

---

## 16. Non-Functional Requirements

| ID | Category | Requirement | Priority |
|---|---|---|---|
| NFR-01 | Performance | LCP < 2.5s on product and collection pages. Next.js SSR + CloudFront CDN. PageSpeed ≥80 on mobile. | Critical |
| NFR-02 | Scalability | 500 concurrent users at launch; scale to 5,000 within 12 months. Redis handles inventory holds (TC-13) atomically under load. | High |
| NFR-03 | Security | TLS 1.3 enforced. PCI DSS via Stripe. TC-01/TC-03: 15-min JWT. TC-10/TC-11: login lockout. Admin 2FA mandatory. | Critical |
| NFR-04 | Availability | 99.9% uptime SLA. TC-29 email retry on failure. TC-30 Stripe webhook monitoring with 24h alert. | High |
| NFR-05 | Accessibility | WCAG 2.1 Level AA. Validated via axe-core automated + manual audit pre-launch. | Critical |
| NFR-06 | SEO | Next.js SSR for all product/collection pages. Structured data (JSON-LD) for products and designer profiles. Auto-generated sitemap. | High |
| NFR-07 | GDPR Compliance | No non-essential cookies before consent (TC-36: 12-month validity). Right to erasure within TC-34: 30 days. TC-35: backup purge within 90 days. | Critical |
| NFR-08 | CCPA Compliance | TC-37: 'Do Not Sell' opt-out takes effect immediately (same page load). | Critical |
| NFR-09 | Mobile Responsiveness | Mobile-first CSS. All pages functional from 375px. Tested on iOS Safari 17+ and Android Chrome. | Critical |
| NFR-10 | API Design | REST API versioned from day one (`/api/v1/`). OpenAPI 3.0 spec required. Rate limiting on all public endpoints. | High |
| NFR-11 | Checkout Integrity | TC-13: Inventory hold uses Redis atomic SETNX. TC-16: Stripe idempotency key prevents duplicate charges. Lead time locked at checkout initiation. | Critical |
| NFR-12 | Data Retention | TC-31: customer PII retained 3 years. TC-32: financial records 7 years. TC-33: server logs 90 days rolling. | Critical |
| NFR-13 | Image Quality | Product images served via CloudFront CDN at original resolution on desktop, responsive breakpoints on mobile. Swatch images must render accurately at 100×100px minimum. | High |

---

## 17. Time Constraints & Validation Rules

> **CONFIGURABLE VALUES:** Values marked "Yes — Admin" must be exposed as configurable settings in the admin System Settings panel. The Development Lead must confirm which values are hard-coded vs. configurable per OD-10.

### 17.1 Authentication & Session

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
|---|---|---|---|---|
| TC-01 | JWT access token — customer | 15 minutes | No (security) | Silent refresh via TC-02. If refresh also expired, redirect to login. Cart state preserved in session storage. |
| TC-02 | Refresh token — customer | 30 days | Yes — Admin | User must re-authenticate. Token rotated on every use — old token immediately invalidated. |
| TC-03 | JWT access token — admin | 15 minutes | No (security) | Silent refresh via TC-04. |
| TC-04 | Refresh token — admin | 8 hours | Yes — Super Admin | Admin must re-authenticate. Auto-save drafts every 60 seconds. |
| TC-05 | Admin inactivity timeout | 30 minutes | Yes — Super Admin | Session terminated on inactivity. Countdown warning at 5 minutes remaining. |
| TC-06 | Email verification link | 24 hours | No | Link shows 'Expired' page with 'Resend' button. Max 3 resend attempts per hour. |
| TC-07 | Password reset link | 1 hour | No | Link shows 'Expired or already used'. Previous link invalidated on new request. |
| TC-08 | OAuth state token (CSRF) | 10 minutes | No | OAuth callback after 10 min fails with generic error. User must restart social login flow. |
| TC-09 | S3 specification PDF presigned URL | 15 minutes | No | URL returns HTTP 403. Customer reloads product page to generate a fresh URL. |
| TC-10 | Customer login lockout | 5 failed → 15-min lockout | No | 'Too many attempts. Try again in X minutes.' Counter resets after successful login. |
| TC-11 | Admin login lockout | 3 failed → 30-min lockout | No | Super Admin receives email notification. Super Admin can manually unlock via admin panel. |

### 17.2 Cart & Checkout

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
|---|---|---|---|---|
| TC-12 | Guest cart session lifetime | 30 days from last activity | No | Redis session and cart cookie expire silently. |
| TC-13 | Inventory hold (checkout initiated) | 15 minutes | No | Hold released. Countdown shown at ≤5 min. At 0:00: redirect to cart with 'Reservation expired.' |
| TC-14 | Checkout session hard expiry | 30 minutes total | No | Full checkout session expires. Prevents stale prices/tax/lead times. Customer returns to cart. |
| TC-15 | Stripe API confirmation timeout | 30 seconds | No | Show: 'Payment confirmation taking longer than expected. Check order history before retrying.' Prevents duplicate charges. |
| TC-16 | Stripe idempotency key window | 24 hours | No (Stripe default) | Same key within 24h returns original result — prevents duplicate charges on browser retry. |

### 17.3 Orders & Fulfillment

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
|---|---|---|---|---|
| TC-17 | Order confirmation SLA | 4 business hours from order placement | Yes — Admin | Order escalated to Super Admin dashboard with 'Overdue' badge + email alert. |
| TC-18 | Order dispatch SLA (in-stock items) | 48 business hours from confirmation | Yes — Admin | Order flagged 'SLA Breached' with red badge. Super Admin receives email alert. |
| TC-TRA-01 | Trade account application review SLA | 2 business days from submission | Yes — Admin | Application flagged 'Overdue Review'. Super Admin receives email escalation. |

### 17.4 Returns & Refunds

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
|---|---|---|---|---|
| TC-19 | Return eligibility window | 30 days from delivery | Yes — Admin (subject to Legal) | 'Request Return' button hidden. Server-side validation at submission. |
| TC-20 | Customer return shipping deadline | 14 days from approval | Yes — Admin | Admin alerted if no return update. Not auto-cancelled — admin decides. |
| TC-21 | Admin return review SLA | 2 business days from submission | Yes — Admin | Return flagged 'Overdue Review' with red badge. Super Admin receives email alert. |
| TC-22 | Admin refund initiation SLA | 1 business day from item receipt | Yes — Admin | 'Refund Due' alert shown in admin panel. Super Admin notified. |
| TC-23 | Total refund resolution target | ≤12 business days | Informational | Business benchmark only. Not a hard system enforced timer. |
| TC-30 | Stripe webhook expected receipt | Alert if not received within 24h | No | Admin alerted to verify refund status in Stripe dashboard. |

### 17.5 Reviews, Notifications & Compliance

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
|---|---|---|---|---|
| TC-24 | Review prompt email delay | 7 days after delivery | Yes — Admin | BullMQ scheduled job. One email per order. |
| TC-25 | Review submission window | 180 days from delivery | Yes — Admin | 'Write a Review' option hidden after 180 days. |
| TC-26 | Review moderation SLA | 48h; auto-approve at 72h | Yes — Admin | Reviews not actioned within 48h flagged. Auto-approve fires at 72h by default. |
| TC-27 | Low-stock alert cooldown | 24 hours per product | Yes — Admin | Prevents alert flood if stock fluctuates around threshold. |
| TC-28 | Back-in-stock notification | Within 1 hour (hourly BullMQ job) | No | Customers on 'Notify Me' list emailed. List cleared after notification. |
| TC-29 | Transactional email retry | 3× at 1 min; 1× at 15 min | No | If all retries fail: admin alert. Customer can re-request confirmation from 'My Orders'. |
| TC-31 | Customer PII data retention | 3 years from last activity | Legal — confirm | Monthly BullMQ job identifies inactive accounts. 30-day warning email sent before deletion. |
| TC-32 | Order / financial records retention | 7 years from order date | Legal — confirm | Cannot be deleted on customer erasure request during window. PII fields anonymised. |
| TC-33 | Server / application log retention | 90 days rolling | DevOps config | Auto-purged after 90 days. |
| TC-34 | GDPR erasure processing deadline | Within 30 days of erasure request | No (legal maximum) | Automated erasure job: anonymises PII, retains financial records. Confirmation email within 30 days. |
| TC-36 | Cookie consent validity | 12 months from consent date | No (GDPR best practice) | Consent re-displayed after 12 months. Immediately invalidated on material policy change. |
| TC-37 | CCPA opt-out effect | Immediate (same page load) | No (legal requirement) | Tracking pixels (GA4) disabled within same page load on opt-out. |
| TC-38 | Marketing unsubscribe processing | Within 10 business days | No (legal maximum) | Suppression list maintained. Email address blocked from re-addition. |

---

## 18. Domain-Specific Considerations

### 18.1 Product Configuration Complexity & DB Implications

- Premium design furniture has multiple independent variant dimensions: material (e.g. walnut, oak, lacquered steel), finish (e.g. natural oil, matte black, polished brass), fabric grade (e.g. Grade A linen, Grade C leather), base type (e.g. 4-star, sled, solid wood), and size.
- Each unique combination of configuration options produces a distinct orderable SKU with its own price, lead time, weight, and stock level — requiring a `product_configurations` table linked to a parent product.
- Configuration options that are out of production or temporarily unavailable must be surfaced at the configurator level, not just at cart level, to prevent frustration.
- Specification PDFs are associated to parent product model via a `product_assets` table; served via TC-09: 15-minute S3 presigned URLs. Full-resolution versions available to verified trade accounts only.
- Lead times are stored per configuration, not just per product. A sofa in standard fabric may be 6–8 weeks; the same sofa in a special-order leather may be 12–16 weeks.

### 18.2 Shipping & White-Glove Complexity

- **Small accessories** (<15 lbs): standard parcel carriers (FedEx/UPS for US; DHL/DPD for EU). 3–7 business day delivery.
- **Medium furniture** (15–80 lbs, e.g. chairs, side tables): threshold carriers with 2-person delivery. 5–10 business day delivery.
- **Large furniture** (>80 lbs, e.g. sofas, beds, wardrobes): LTL freight + specialist white-glove partner. 7–21 business day delivery window. Room-of-choice delivery and packaging removal included as standard.
- White-glove service zone eligibility checked by ZIP/postal code at product page and at checkout address step. Customers outside zone offered 'threshold delivery' or 'contact us' for bespoke logistics.
- Made-to-order items have a production lead time before dispatch SLA (TC-18) begins. Lead time and estimated delivery date displayed and locked at checkout.

### 18.3 Tax Handling

- **United States:** Sales tax calculated at checkout by state/county via Stripe Tax or TaxJar. Nexus states confirmed per CR-10. Design services and trade purchases may have different tax treatment — legal confirmation required.
- **European Union:** VAT included in displayed price. Per-country VAT rates in DB. Trade accounts with valid VAT registration numbers: VAT-exempt invoice generated at checkout (B2B reverse charge mechanism applies in some EU jurisdictions — legal confirmation required).
- **United Kingdom:** Standard UK VAT (20%). Separate market with GBP pricing and UK shipping zones. Post-Brexit import duty considerations for EU→UK shipments to be confirmed by legal.

### 18.4 Inventory States & Lead Time Behaviour

| State | Definition | Time / Timer Behaviour |
|---|---|---|
| In Stock | `stock_qty > low_stock_threshold` for this configuration | TC-27: Low-stock alert with 24h cooldown when stock falls to threshold. |
| Low Stock | `stock_qty ≤ threshold` (admin-configurable) | 'Only X left in this configuration' badge. Add to Cart still enabled. |
| Out of Stock | `stock_qty = 0`, no active hold, no pre-order | 'Notify Me' button. TC-28: Back-in-stock email sent within 1 hour of restock. |
| Held (Checkout) | `stock_qty > 0` but `inventory_hold` exists in Redis | TC-13: Hold expires after 15 minutes. Other checkout attempts see reduced available qty. |
| Made to Order | `stock_qty = 0` but item is producible to order | Lead time displayed per configuration. Order placed with supplier on purchase confirmation. TC-18 dispatch SLA starts from production completion. |
| Discontinued | `is_archived = true` in DB | Product page 301-redirects to parent collection immediately on archive. Not purchasable or searchable. |

---

## 19. Key Performance Indicators (KPIs)

### 19.1 Business KPIs

| KPI | Definition | Target (6 Months) | Source |
|---|---|---|---|
| Online Revenue Contribution | % of total revenue from online channel (B2C + B2B) | ≥ 35% | Admin Reports |
| Average Order Value (AOV) | Average revenue per completed order | ≥ $1,800 / €1,600 | Admin Reports |
| Trade Revenue Share | % of online orders from verified trade accounts | ≥ 20% of online channel by Month 12 | Admin Reports |
| Trade Account Growth | Number of active verified trade accounts | ≥ 500 by Month 6 | Admin Reports |
| New vs. Returning Customer Ratio | % of orders from new vs. returning buyers | Track baseline Month 2 | Admin / GA4 |

### 19.2 Product & Operational KPIs

| KPI | Definition | Target (6 Months) | Source |
|---|---|---|---|
| Conversion Rate | % of sessions resulting in a completed order | ≥ 1.8% (premium AOV) | GA4 + Admin |
| Cart Abandonment Rate | % of carts not completed at checkout | ≤ 60% | Custom Analytics |
| Order Confirmation SLA Compliance | % of orders confirmed within TC-17: 4 biz hrs | ≥ 95% | Admin Reports |
| Order Dispatch SLA Compliance | % of in-stock orders shipped within TC-18: 48 biz hrs | ≥ 95% | Admin Reports |
| Trade Application Review SLA | % of trade applications reviewed within 2 business days | ≥ 95% | Admin Reports |
| Return Rate | % of delivered orders returned | ≤ 5% (premium lower expectation) | Returns Reports |
| Avg. Product Rating | Mean star rating across all reviewed products | ≥ 4.5 / 5.0 | Review Module |
| Page Load Time (LCP) | Largest Contentful Paint on product pages | < 2.5 seconds | Core Web Vitals |

---

## 20. Stakeholder Register & RACI

### 20.1 Stakeholder Register

| Stakeholder | Role | Key Responsibilities | Influence | Interest |
|---|---|---|---|---|
| Business Owner | Project Sponsor | Approves scope, budget, all Open Business Decisions (OD-05 through OD-10). Signs off trade portal scope. | High | High |
| Product Owner / BA | Requirements Lead | Owns BRD, SRS, backlog, and all TC definitions. Bridge between business intent and technical execution. | High | High |
| Development Lead | Technical Architect | Architecture decisions, tech stack, API design, DB schema for product configuration and trade portal, feasibility review of all TC values. | High | High |
| UX/UI Designer | Design Lead | Wireframes, design system, product configurator UX, checkout countdown timer UI, trade portal flows. | Medium | High |
| QA Lead | Quality Assurance | Test strategy, test cases for all TC values and trade portal flows, regression testing, pre-launch UAT sign-off. | Medium | High |
| Trade & Sales Manager | Trade Channel Lead | Validates trade portal requirements, trade pricing tiers (OD-05), VAT exemption flow (OD-06), account manager assignment. | High | High |
| Warehouse / Ops Team | Fulfillment Operations | Validate order management, SLA thresholds (TC-17, TC-18), lead time data accuracy (CR-07), returns process. | Low | High |
| Legal / Compliance | Compliance Advisory | GDPR, CCPA, returns policy (OD-07, OD-08), VAT exemption legal basis (OD-06), data retention values TC-31 to TC-38. | Medium | High |
| Finance | Financial Oversight | Currency model (OD-02), trade pricing tiers, tax nexus, 7-year financial record retention (TC-32). | Medium | Medium |

### 20.2 RACI Matrix — Key Project Activities

| Activity | Biz Owner | PO/BA | Dev Lead | UX/UI | QA | Trade Mgr | Legal | Finance |
|---|---|---|---|---|---|---|---|---|
| BRD v4.0 Approval | A | R | C | C | I | C | C | I |
| Trade Portal Scope & OD-05 / OD-06 | A/R | R | C | I | I | R | C | C |
| Open Business Decisions (OD-07 to OD-10) | A/R | R | C | I | I | C | C | C |
| TC values Legal review (TC-31 to TC-38) | A | C | I | I | I | I | R | C |
| SRS Sign-Off | A | R | C | C | C | C | I | I |
| Product Configurator UX & Design | I | C | C | R/A | C | I | I | I |
| Backend / API Development | I | C | R/A | I | C | I | I | I |
| GDPR / Legal Review | A | C | I | I | I | I | R | I |
| UAT Sign-Off | A | C | C | C | R | C | I | I |
| Go-Live Approval | A/R | C | C | C | C | C | C | I |

*R = Responsible &nbsp; A = Accountable &nbsp; C = Consulted &nbsp; I = Informed*

---

## 21. Approvals

By signing below, the named stakeholder confirms they have reviewed BRD v4.0 — including all time constraints and validation rules in Section 17 and the trade portal scope in Section 8.3 — and approve it as the basis for proceeding to the Software Requirements Specification (SRS) phase.

> **NOTE:** Before signing: Legal must confirm data retention values TC-31 to TC-38 and returns policy OD-07/OD-08. The Development Lead must confirm technical feasibility of all TC values. Business Owner must resolve Open Decisions OD-05 through OD-10. Trade Manager must confirm trade pricing tiers and VAT exemption approach.

| Role | Name | Title | Signature | Date |
|---|---|---|---|---|
| Project Sponsor / Business Owner | | | | |
| Product Owner / BA | | | | |
| Development Lead | | | | |
| UX/UI Lead | | | | |
| QA Lead | | | | |
| Trade & Sales Manager | | | | |
| Legal / Compliance | | | | |
| Finance | | | | |

---

*— End of Document — HomeStyle BRD v4.0 | Premium Design Furniture | Custom Web Application | Confidential*
