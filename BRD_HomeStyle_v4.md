  
**HOMESTYLE**

Furniture & Home Décor

**Business Requirements Document**

Custom Web Application — Full-Stack Build

Target Market: United States & Europe

| Version | 4.0 |
| :---- | :---- |
| Status | Draft — Pending Client Approval |
| Date | April 2026 |
| Build Type | Custom Web Application (Full-Stack) |
| Author | Product Owner / BA |
| Classification | Confidential |

# **1\. Document Revision History**

| Version | Date | Description | Author |
| :---- | :---- | :---- | :---- |
| 1.0 | Apr 2026 | Initial Draft | PO / BA |
| 2.0 | Apr 2026 | Full revision: GDPR gaps, Returns flow, Guest Checkout decision, Currency model, Phase roadmap, Shopify platform incorporated | PO / BA |
| 3.0 | Apr 2026 | Platform changed from Shopify to custom-built web application. Tech stack section added. All platform-specific references replaced. | PO / BA |
| 4.0 | Apr 2026 | Time constraints and validation rules fully integrated throughout document. New Section 17 added (Time Constraints Reference). Auth, cart, checkout, orders, returns, reviews, compliance, and NFR sections updated with 38 time-based requirements (TC-01 to TC-38). | PO / BA |

*Change Control: All post-approval changes must be submitted via a Change Request (CR) form, reviewed by the Project Sponsor, and versioned incrementally. No scope changes take effect without written approval.*

# **2\. Table of Contents**

1\.  Document Revision History

2\.  Table of Contents

3\.  Executive Summary & Business Case

4\.  Business Objectives & Goals

5\.  Platform Decision — Custom Web Application

6\.  Target Market & User Personas

7\.  User Roles

8\.  Project Scope & Phase Roadmap

9\.  Open Business Decisions (Pending Client Sign-Off)

10\. Client Responsibilities & Deliverables

11\. Business Assumptions & Constraints

12\. Key Business Processes

13\. Business Capabilities (High-Level Requirements)

14\. System Email & Notification Triggers

15\. Returns & Exchange — Business Rules

16\. Non-Functional Requirements

17\. Time Constraints & Validation Rules  ← NEW in v4.0

18\. Domain-Specific Considerations

19\. Key Performance Indicators (KPIs)

20\. Stakeholder Register & RACI

21\. Approvals

# **3\. Executive Summary & Business Case**

### **3.1 Why Now — The Business Opportunity**

Global furniture e-commerce revenue is projected to surpass $800 billion by 2027, driven by post-pandemic shifts in home investment and the growing consumer expectation to research, visualize, and purchase furniture entirely online. Yet most mid-sized furniture retailers remain locked in offline operations or poorly optimized digital channels — losing customers to Amazon, Wayfair, and IKEA's digital-first experience.

HomeStyle has an established product catalogue, warehouse infrastructure, and supplier relationships. The missing piece is a modern, conversion-optimized digital platform. This project delivers a fully custom-built web application — owned entirely by the business, with no platform licensing dependency — designed to scale with HomeStyle's long-term growth ambitions.

### **3.2 Why Custom-Built — Strategic Rationale**

| Dimension | Platform (e.g. Shopify) | Custom Web Application |
| :---- | :---- | :---- |
| Ownership | Platform-owned; subject to pricing & policy changes | 100% client-owned IP; no vendor lock-in |
| Customization | Constrained by platform limits and app ecosystem | Unlimited — any feature can be built as specified |
| Total Cost of Ownership | Lower upfront; recurring monthly fees | Higher upfront; lower long-term operating cost |
| Scalability | Platform-managed; limited infrastructure control | Full control over architecture, hosting, scaling |
| Data Ownership | Data stored on platform; limited export control | All data owned and controlled by the client |
| Future Roadmap | Phase 2+ features constrained by platform | Phase 2/3 features fully buildable as planned |

### **3.3 Business Impact & ROI Narrative**

| Metric | Current State (Offline) | Target State (12 Months Post-Launch) |
| :---- | :---- | :---- |
| Revenue Channel | 100% offline / in-store | 30–40% from online channel |
| Customer Reach | Local / regional only | Nationwide US \+ key EU markets |
| Order Processing | Manual (phone / email / in-store) | Fully automated via custom admin panel |
| Customer Data | Fragmented / unstructured | Unified in proprietary database — client-owned |
| Platform Dependency | N/A | Zero — fully owned infrastructure |

### **3.4 Proposed Solution**

A two-sided custom web application:

* Customer Storefront: SSR web application (Next.js) delivering a premium furniture shopping experience — room-based navigation, rich product visualization, multi-currency checkout with time-enforced inventory holds, order tracking, and a full returns portal.

* Admin & Operations Panel: Custom-built back-office web application for product catalogue, orders, inventory, shipping, customers, promotions, content, and reporting — with granular role-based access and configurable SLA timers.

* REST API Layer: Versioned RESTful API (/api/v1/) serving both applications, with Stripe payment integration, shipping carrier APIs, transactional email service, and time-based background job scheduler.

# **4\. Business Objectives & Goals**

| \# | Objective | Success Metric |
| :---- | :---- | :---- |
| BO-01 | Establish a fully owned online sales channel | Online channel contributes ≥30% of total revenue within 12 months |
| BO-02 | Reach US and European customers | 10,000 registered customers within 6 months of launch |
| BO-03 | Automate order & fulfillment operations | Order processing time reduced by 60% vs. manual baseline; SLA compliance ≥ 95% |
| BO-04 | Deliver superior customer experience | CSAT ≥ 4.5/5; cart abandonment ≤ 65%; refund resolution ≤ 12 business days |
| BO-05 | Enable data-driven business decisions | Weekly reports consumed by management via admin dashboard |
| BO-06 | Achieve full legal compliance (GDPR / CCPA) | Zero violations; GDPR erasure processed within 30 days; cookie consent valid 12 months |
| BO-07 | Own all customer and business data outright | All data stored in client-controlled database; zero vendor data lock-in |
| BO-08 | Establish customer retention baseline | New vs. returning customer ratio tracked from Month 2 onward |

# **5\. Platform Decision — Custom Web Application**

### **5.1 Decision Summary**

Following evaluation of available e-commerce platforms, the client has selected a fully custom web application. All functionality will be designed, developed, tested, and deployed as proprietary software. There is no dependency on any e-commerce SaaS platform.

| DECISION CONFIRMED | Fully custom-built web application. Final and approved by Project Sponsor. All downstream documents (SRS, API Spec, Architecture Diagram, Database Schema) reflect a custom-build approach. |
| :---- | :---- |

### **5.2 Recommended Technology Stack**

| Layer | Recommended Technology | Rationale |
| :---- | :---- | :---- |
| Frontend (Storefront) | Next.js (React) \+ TypeScript | SSR/SSG for SEO; React ecosystem; strong TypeScript support; excellent DX |
| Admin Panel | React \+ TypeScript (SPA) | Rich interactive UI; reuse of frontend component library; role-based routing |
| Backend / API | Node.js \+ Express or NestJS | JavaScript full-stack consistency; mature REST API ecosystem; strong ORM support |
| Database (Primary) | PostgreSQL | ACID compliance; strong relational model for orders/products/inventory; JSON support |
| Database (Cache) | Redis | Session storage; cart caching; rate limiting; real-time inventory lock during checkout |
| File / Image Storage | AWS S3 \+ CloudFront CDN | Scalable object storage; global CDN for fast image delivery; signed URLs for PDFs |
| Payment Processing | Stripe (REST API) | Industry-leading payment API; PCI DSS compliance; multi-currency; Apple Pay / Google Pay |
| Email Service | SendGrid or AWS SES | Reliable transactional email; template management; delivery analytics |
| Search Engine | Elasticsearch or Algolia | Full-text search with typo tolerance, faceted filters, and relevance ranking |
| Tax Calculation | Stripe Tax or TaxJar API | Automated US sales tax by state \+ EU VAT calculation at checkout |
| Shipping API | EasyPost or ShipEngine | Multi-carrier rate shopping; label generation; real-time tracking webhooks |
| Authentication | JWT \+ OAuth 2.0 | Stateless API auth; social login (Google, Facebook) via OAuth 2.0 |
| Hosting / Infra | AWS (ECS / RDS / S3 / CloudFront) | Production-grade infrastructure; auto-scaling; managed DB; global CDN |
| CI/CD Pipeline | GitHub Actions | Automated testing, build, and deployment pipeline; environment separation |
| **Layer** | **Recommended Technology** | **Rationale** |

### **5.3 System Architecture Overview**

* Presentation Layer: Next.js storefront (SSR/SSG) and React admin SPA, communicating exclusively with the backend via REST API (/api/v1/).

* Application Layer: NestJS REST API handling all business logic — authentication (with token lifecycle management), cart, checkout, inventory hold, orders, returns, refunds, promotions, and all scheduled background jobs.

* Data Layer: PostgreSQL as primary relational database; Redis for caching, session tokens, cart state, inventory holds (TTL keys), and job queues; AWS S3 for product images, 360° sequences, and assembly PDFs (presigned URL delivery).

* Third-party integrations: Stripe (payments, refunds, webhooks), EasyPost/ShipEngine (shipping), SendGrid/SES (email with retry), Elasticsearch/Algolia (search), Google/Facebook OAuth (social login), Google Analytics 4 (consent-gated).

# **6\. Target Market & User Personas**

### **6.1 Target Market**

| Dimension | United States | Europe (EU \+ UK) |
| :---- | :---- | :---- |
| Currency | USD ($) | EUR (€) and GBP (£) |
| Tax Model | Sales tax added at checkout by state | VAT included in displayed price |
| Data Privacy Law | CCPA (California) | GDPR — full compliance required |
| Payment Methods | Visa, MC, Amex, Apple Pay, Google Pay, PayPal | Visa, MC, iDEAL (NL), SEPA Direct Debit, Apple Pay |
| Shipping Carriers | FedEx, UPS, LTL freight carriers | DHL, DPD, LTL freight carriers |

### **6.2 Persona 1 — The Home Improver (Primary US, v1)**

| Sarah, 34 — Marketing Manager, Austin TX *Purchase frequency: 1–2×/year  |  Budget: $500–$2,000/order  |  Channel: Google Search, Instagram* Goals: Redecorate living room; wants to visualize before buying. Pain Points: Fears buying unseen; worried about delivery damage; needs firm delivery dates. Key Needs: 360° images, verified reviews, easy returns portal with clear timelines, real-time tracking, white-glove delivery. |
| :---- |

### **6.3 Persona 2 — The New Homeowner (Primary EU, v1)**

| James, 29 — Software Engineer, Berlin, Germany *Purchase frequency: 3–5× in first 6 months  |  Budget: €200–€800/order  |  Channel: Google, Pinterest* Goals: Furnish entire apartment consistently within budget. Pain Points: Overwhelmed by choice; needs full cost upfront (VAT \+ delivery); wants reliable stock. Key Needs: Room-based browsing, wishlist, EUR with VAT included, per-variant stock availability. |
| :---- |

### **6.4 Persona 3 — The Interior Designer  ⚠ Phase 2 — Not in v1 Scope**

| Maria, 42 — Interior Designer, London UK  |  Phase 2 Persona Phase 2 Features (deferred): Trade/B2B portal, bulk pricing, project wishlists, spec sheets, VAT exemption. |
| :---- |

# **7\. User Roles**

| Role | Description & Permissions |
| :---- | :---- |
| Guest / Visitor | Unauthenticated user. Can browse products, search, view product details, check delivery availability, view reviews, and add items to a session-based cart. Guest cart is preserved via a server-side session token and merged with account on login. Guest checkout subject to OD-01 decision. |
| Registered Customer | Authenticated via JWT. All guest permissions plus: persistent cart, order history, order tracking, wishlist, returns portal, post-purchase reviews, and account management (profile, addresses, password). |
| Super Admin | Full access to all admin panel modules: user management, product catalogue, orders, inventory, shipping config, promotions, reports, CMS, email templates, roles & permissions, and system settings. |
| Admin — Operations | Role-based access: orders, inventory, shipping, returns, and customer management. No access to financial configuration, system settings, or other admin user accounts. |
| Admin — Content | Role-based access: product catalogue, collections, CMS pages, blog, email templates, promotions, and review moderation. No access to orders or financial data. |

# **8\. Project Scope & Phase Roadmap**

### **8.1 Phase Delivery Roadmap**

| SCOPE BOUNDARY | Features not listed under Phase 1 are explicitly deferred. Any request to include deferred features requires a formal Change Request (CR) and re-estimation. |
| :---- | :---- |

| Phase | Timeline | Features Included | Status |
| :---- | :---- | :---- | :---- |
| Phase 1 — MVP | Months 1–6 | Full custom storefront \+ admin panel, product catalogue, cart with time-enforced inventory holds, checkout with SLA timers, Stripe payments, multi-currency, order management with SLA tracking, customer accounts (JWT auth), wishlist, ratings & reviews, returns & exchange portal with full status lifecycle, shipping config, discount codes, CMS pages, GDPR consent, system email notifications with retry logic, admin dashboard & reports, role-based access. | In Scope |
| Phase 2 — Growth | Months 7–12 | WebAR visualization, B2B trade portal, loyalty program, multi-language (DE, FR), AI product recommendations, live chat. | Deferred |
| Phase 3 — Scale | Year 2+ | Native mobile app, multi-vendor marketplace, BNPL payments, advanced personalization, ERP integration. | Future |

### **8.2 Phase 1 — In-Scope: Customer Storefront**

* User registration and login (email/password \+ social OAuth via Google and Facebook)

* Email verification flow and password reset

* Product browsing: room-based category navigation, subcategories, collections/series

* Full-text product search with auto-suggest, filters (price, material, size, color, rating, availability), and sorting

* Product detail page: multi-image gallery, 360° image carousel, dimension table (inches \+ cm), material/color/size variant selector, assembly PDF download, delivery availability checker

* Shopping cart: add/remove/update quantity; session-based guest cart merged on login

* Wishlist: save products, share via link, move items to cart

* Guest checkout (pending OD-01 — see Section 9\)

* Multi-step checkout: address → delivery method selection → payment

* White-glove delivery option at checkout for eligible large items

* Multi-currency display and checkout (USD / EUR / GBP)

* US sales tax calculation via Stripe Tax / TaxJar API

* EU VAT-inclusive pricing; VAT breakdown displayed at checkout

* Payment via Stripe: credit/debit cards, Apple Pay, Google Pay, iDEAL (NL), SEPA Direct Debit (EU)

* Discount code input at checkout

* Order confirmation page and confirmation email

* Order history and order detail view

* Real-time shipment tracking via carrier tracking link

* Refund status visible in customer order view

* Returns & exchange request portal (see Section 15\)

* Post-purchase product reviews (one per customer per product, post-delivery unlock)

* Social media product sharing

* GDPR cookie consent banner and preference centre (non-essential cookies blocked until consent)

* Contact/support form with admin email notification

* CMS-managed pages: About Us, FAQ, Delivery Info, Returns Policy, Privacy Policy, Terms & Conditions

### **8.3 Phase 1 — In-Scope: Admin Panel**

* Secure admin login with mandatory 2FA (TOTP via authenticator app)

* Dashboard: real-time KPI cards (today's revenue, order count, new customers, low-stock alerts), sales trend chart (daily/weekly/monthly)

* Product management: full CRUD, multi-image upload to S3, variant management (color/material/size), SKU assignment, per-currency pricing, stock levels, assembly PDF upload

* Category, subcategory, and collection/series management

* Inventory management: real-time stock per SKU variant, configurable low-stock thresholds, bulk stock update via CSV import

* Order management: list/search/filter, status updates, shipment creation (carrier \+ tracking number), packing slip print, invoice download

* Returns management: view requests, approve/reject, issue Stripe refund, track return status through full lifecycle

* Customer management: view/search, order history, activate/deactivate accounts, manual password reset

* Promotions: discount code CRUD — percentage and fixed, minimum order value, usage limits, expiry date, stacking rules (per OD-04)

* Shipping configuration: zones (US states / EU countries), flat-rate and weight-based rates, white-glove option toggle per product category

* Review moderation: approve, reject, flag, or respond to customer reviews before storefront publication

* CMS content management for all static pages

* Email template management: edit transactional email content with live preview

* Staff user management: create/edit/deactivate admin users with granular module-level permissions

* Reports: revenue by date range/product/category, order volume, customer acquisition (new vs. returning), top-selling products — exportable to PDF and Excel

* Admin notification settings: email alerts for new orders, low stock, new returns, pending reviews

### **8.4 Out of Scope — Phase 1**

* WebAR / 3D room placement (Phase 2\)

* B2B / Trade account portal (Phase 2\)

* Multi-language storefront — German, French (Phase 2\)

* Loyalty / rewards program (Phase 2\)

* Native iOS / Android mobile app (Phase 3\)

* Multi-vendor marketplace (Phase 3\)

* Buy Now Pay Later / installment payments (Phase 3\)

* AI-powered product recommendations (Phase 3\)

* Live chat or AI chatbot (Phase 2\)

* ERP / accounting software integration (Phase 3\)

# **9\. Open Business Decisions (Pending Client Sign-Off)**

The following decisions must be confirmed in writing before the SRS can be finalized. Each has a direct impact on system architecture, development effort, and time constraint configuration.

| ACTION REQUIRED | All items below must be confirmed by the client within 2 weeks of BRD approval. Unresolved decisions will block SRS sign-off. |
| :---- | :---- |

| \# | Decision | Options | Dev Impact | Owner |
| :---- | :---- | :---- | :---- | :---- |
| OD-01 | Guest Checkout | A) Allow checkout without accountB) Require registration before checkout | Option A: guest order model in DB; session cart merge logic required | Business Owner |
| OD-02 | Currency / Exchange Rate Model | A) Live rates via Fixer.io API (auto-converted)B) Admin manually sets fixed prices per currency | Option A: scheduled rate-sync job required | Biz Owner \+ Finance |
| OD-03 | Pre-Order Functionality | A) Allow pre-orders with restock date shownB) Show 'Out of Stock' only — no pre-orders v1 | Option A: pre-order inventory state \+ backorder queue required | Business Owner |
| OD-04 | Discount Code Stacking | A) One code per order onlyB) Codes can stack with sale pricesC) Codes cannot stack with sale prices | Determines promotion engine logic; Option B most complex | Biz Owner \+ Marketing |
| OD-05 | Delivery Availability Checker | A) ZIP/postal lookup against DB tableB) Real-time carrier API query | Option B requires carrier API at product page level | Biz Owner \+ Logistics |
| OD-06 | Review Edit / Delete Policy | A) Customers can edit or delete own reviewsB) Reviews permanent once submitted | Option A: review versioning or soft-delete in DB required | Business Owner |
| OD-07 | Data Retention Period (GDPR) | Confirm retention period for customer PII (default: 3 years last activity) and anonymization approach | Automated purge job required; affects DB schema for PII fields (TC-31, TC-32) | Biz Owner \+ Legal |
| OD-08 | Social Login GDPR Legal Basis | Confirm legal basis: Legitimate Interest vs. Explicit Consent for Google/Facebook data | Determines consent flow design for EU users (TC-06, TC-08) | Legal Team |
| OD-09 | Return Window Duration | Confirm exact return eligibility window (default: 30 days from delivery — see TC-19) | Affects server-side eligibility calculation and all customer-facing return messaging | Biz Owner \+ Legal |
| OD-10 | Admin-Configurable Time Values | Confirm which TC values are hard-coded vs. configurable via System Settings panel (see Section 17\) | Configurable values require a System Settings module in admin panel | Biz Owner \+ Dev Lead |

# **10\. Client Responsibilities & Deliverables**

The following assets must be provided by the client to the development team. Late delivery directly impacts the project timeline.

| \# | Deliverable | Specification | Required By |
| :---- | :---- | :---- | :---- |
| CR-01 | Product Photography | Min. 6 high-res images per product (all angles). Room context shots strongly recommended. Min 2048×2048px, JPEG/WebP format. | Month 2 |
| CR-02 | 360° Image Sequences | Pre-rendered image sequence (36 frames minimum) per product for 360° viewer. Delivered as numbered JPEG files per product SKU. | Month 2 |
| CR-03 | Assembly Instruction PDFs | One PDF per product model, filename matching SKU code (e.g., HS-SOFA-001.pdf). Max 10MB per file. | Month 3 |
| CR-04 | Product Data CSV | Full catalogue: product name, description, SKU, price (USD/EUR/GBP), dimensions (W×D×H in inches and cm), weight (lbs and kg), materials, colour variants, stock qty per variant. | Month 1 |
| CR-05 | Stripe Account | Active Stripe account with identity verification completed. Client provides restricted API keys to dev team. | Month 2 |
| CR-06 | Cloud Hosting Account | AWS account created and dev team granted programmatic access. S3 buckets, RDS instance, and domain configured. | Week 2 |
| CR-07 | Domain & SSL | Domain name registered and DNS access granted. SSL certificate provisioned (AWS ACM recommended). | Week 2 |
| CR-08 | Legal Documents | Privacy Policy, Terms & Conditions, Returns Policy, Cookie Policy — fully drafted and approved by client's Legal team before launch. | Month 4 |
| CR-09 | GDPR Records (RoPA) | Record of Processing Activities and confirmed data retention periods. Required for GDPR Article 30 compliance. | Month 4 |
| CR-10 | US Tax Nexus Info | List of US states where business has sales tax nexus. Required for Stripe Tax / TaxJar configuration. | Month 2 |
| CR-11 | EU VAT Registration | VAT registration numbers per EU country where business is VAT-registered. Required for VAT-inclusive pricing configuration. | Month 2 |
| CR-12 | Shipping Carrier Accounts | Active carrier accounts with FedEx/UPS (US) and DHL/DPD (EU) including API credentials for EasyPost / ShipEngine integration. | Month 3 |
| CR-13 | Brand Guidelines | Logo (SVG \+ PNG), brand colors (hex), typography (font files or Google Fonts references), and any visual identity standards. | Week 2 |
| CR-14 | CMS Page Content | Written and approved content for: About Us, FAQ, Delivery Info, Returns Policy, Privacy Policy, and Terms & Conditions pages. | Month 4 |
| CR-15 | Email Marketing Account | SendGrid or AWS SES account provisioned. Domain authentication (SPF/DKIM) configured for transactional email deliverability. | Month 3 |

# **11\. Business Assumptions & Constraints**

### **11.1 Assumptions**

* Physical warehouse and inventory are pre-established and operational before launch.

* The development team has full authority over all technical and architectural decisions within the agreed stack.

* Products will be managed with unique SKU codes as defined by the admin team.

* Primary language of the platform is English for all Phase 1 markets.

* Stripe handles PCI DSS compliance for all card payment processing. No card data stored on application servers.

* US sales tax via Stripe Tax or TaxJar. EU VAT included in displayed prices with country-level rate table managed in admin.

* AWS will be used as the primary cloud infrastructure provider.

* Client procures and maintains all third-party API subscriptions (Stripe, EasyPost, SendGrid/SES, Algolia).

* All time constraint default values in Section 17 are working defaults pending client confirmation per OD-10.

### **11.2 Constraints**

* Timeline: Phase 1 (MVP) launch target is 6 months from project kickoff.

* Budget: All development, infrastructure, and third-party API costs within approved budget.

* Compliance: GDPR and CCPA compliance are non-negotiable prerequisites for EU and California customer data processing.

* Security: PCI DSS compliance mandatory. No card data stored on application servers. Admin 2FA mandatory.

* Accessibility: WCAG 2.1 Level AA required across all customer-facing pages.

* API Design: REST API versioned from day one (/api/v1/) for future mobile app and third-party integrations.

* Time constraints: All TC values in Section 17 must be reviewed by the Development Lead for technical feasibility before SRS sign-off.

### **11.3 Risk Register**

| Risk | Likelihood | Impact | Mitigation Strategy |
| :---- | :---- | :---- | :---- |
| Scope creep delaying Phase 1 MVP | High | High | Formal CR process; all new requests go to Phase 2 backlog. |
| Client delays in providing assets | High | High | Hard deadlines in Section 10; block dependent sprints until assets confirmed. |
| Underestimation of custom build complexity | Medium | High | Detailed SRS \+ effort estimation sprint before development begins. |
| Inventory oversell during high-traffic checkout | Medium | High | TC-13: Redis TTL inventory hold enforced server-side. Load tested pre-launch. |
| GDPR non-compliance (EU storefront) | Low | Critical | Legal review of cookie consent, privacy policy, social login legal basis before EU launch. |
| Data breach or payment security incident | Low | Critical | PCI DSS via Stripe; TLS 1.3; no card data server-side; TC-03/TC-11 admin security. |
| Third-party API instability (Stripe, carriers) | Medium | High | TC-15: 30s API timeout; circuit breakers, retry logic, fallback states. |
| SLA breach damaging customer trust | Medium | Medium | TC-17/TC-18 escalation alerts to Super Admin; SLA dashboard visible to ops team. |

# **12\. Key Business Processes**

### **12.1 Customer Purchase Journey (To-Be — with Timer Events)**

| \# | Stage | Process Detail \+ Time Constraints |
| :---- | :---- | :---- |
| 1 | Discovery | Customer arrives via organic search, paid ad, or direct URL. Next.js serves SSR product/category pages for SEO. |
| 2 | Product Evaluation | Views product detail page: images, 360° carousel, variant selector, dimension table, assembly PDF download (TC-09: 15-min presigned URL — regenerated on each page load), delivery checker, reviews. |
| 3 | Add to Cart / Wishlist | Item added to cart. For guests: stored in Redis session (TC-12: 30-day TTL). For registered users: persisted in DB, no expiry. Cart is NOT an inventory reservation at this stage. |
| 4 | Authentication Check | Guest proceeds to checkout (if OD-01 approved) — server creates guest session with JWT. Registered: TC-01 JWT access token (15 min) validated; silent refresh via TC-02 refresh token (30 days) if expired. |
| 5 | Checkout — Address \[TC-13 starts\] | On checkout page load: system creates inventory\_hold record in Redis with TC-13: 15-minute TTL. Visible countdown timer shown at ≤5 minutes remaining. Customer enters/selects delivery address. Selects delivery method (standard or white-glove). |
| 6 | Checkout — Payment \[TC-14: 30-min hard expiry\] | Entire checkout subprocess expires after TC-14: 30 minutes total. Stripe PaymentIntent created server-side. Customer enters payment via Stripe Elements (PCI iframe). Discount code re-validated server-side at submission. Tax calculated via Stripe Tax. TC-15: 30-second Stripe API confirmation timeout — prevents duplicate charges on retry. |
| 7 | Order Confirmation | Payment confirmed. Order record created in DB. Inventory decremented (hold released and replaced with actual decrement). Cart cleared. Confirmation email dispatched (TC-29: retry 3× at 1 min, 1× at 15 min on failure). |
| 8 | Fulfillment \[TC-17: 4-hr SLA\] | Admin receives new order alert. Must confirm within TC-17: 4 business hours. SLA breach → escalation to Super Admin. Admin reviews, verifies stock, confirms order → status: Confirmed. |
| 9 | Dispatch \[TC-18: 48-hr SLA\] | Admin packs order and enters shipment details within TC-18: 48 business hours of confirmation. SLA breach → 'SLA Breached' flag in admin order list. Shipping confirmation email auto-dispatched with tracking link. |
| 10 | Post-Purchase | Order status: Delivered. TC-24: 7 days later, review prompt email sent (scheduled BullMQ job). TC-19: Return portal available for 30 days from delivery. TC-25: Review submission window open for 180 days. |

### **12.2 Returns & Exchange Process (with Timer Events)**

* Customer navigates to 'My Orders'. 'Request Return' button visible only if within TC-19: 30-day window from delivery (server-side validated, not just UI-gated).

* Customer selects return reason and uploads photos. Eligibility re-validated server-side at submission to prevent race condition at window edge.

* System creates return request record. Admin receives dashboard alert and email. TC-21: Admin must review within 2 business days — SLA breach triggers Super Admin alert.

* If approved: admin sends return label or schedules pickup. TC-20: Customer must ship item back within 14 days of approval. System alerts admin if no tracking update after 14 days.

* Admin confirms receipt and inspects condition. TC-22: Admin must initiate Stripe refund within 1 business day of marking 'Item Received'.

* Stripe refund processed: 5–10 business days for cards; up to 5 days for iDEAL/SEPA. TC-30: If Stripe refund.succeeded webhook not received within 24 hours, admin alert triggered.

* Refund status visible to customer in 'My Orders' at every stage. TC-23: Total resolution target ≤12 business days from request to refund completed.

### **12.3 Admin Order Fulfillment Process**

* New order notification: dashboard counter increment \+ email alert. Admin has TC-17: 4 business hours to confirm before escalation.

* Admin reviews order, verifies stock, confirms → status: Confirmed. Packing slip printed.

* Admin packs and ships within TC-18: 48 business hours. Enters carrier \+ tracking number. System auto-dispatches shipping email to customer.

* Status: Shipped. TC-24: BullMQ job scheduled to send review prompt 7 days after status transitions to Delivered.

# **13\. Business Capabilities (High-Level Requirements)**

Requirements expressed as Business Capabilities. Detailed functional requirements with acceptance criteria are defined in the SRS. TC references indicate time constraints from Section 17 that apply to each capability.

### **13.1 Product Discovery & Browsing**

| ID | Capability | Business Value \+ Time Constraints | Priority |
| :---- | :---- | :---- | :---- |
| BC-01 | Room-based category navigation | Customers shop by context, reducing drop-off. | Critical |
| BC-02 | Full-text search with faceted filters | Narrows 500+ SKUs instantly. Search index synced within 60 seconds of product update. | Critical |
| BC-03 | Rich product detail page | All purchase-decision info in one view. TC-09: Assembly PDF served via 15-min S3 presigned URL. | Critical |
| BC-04 | Delivery availability checker | Reduces checkout abandonment by confirming delivery feasibility upfront. | High |
| BC-05 | 360° product image viewer | Reduces purchase uncertainty. Viewer loads from S3 CDN — no link expiry (public images). | High |

### **13.2 Purchase Experience**

| ID | Capability | Business Value \+ Time Constraints | Priority |
| :---- | :---- | :---- | :---- |
| BC-06 | Frictionless checkout | TC-12: Guest cart 30-day TTL. TC-13: 15-min inventory hold with countdown. TC-14: 30-min checkout session expiry. TC-15: 30s payment timeout. | Critical |
| BC-07 | Multi-currency checkout (USD / EUR / GBP) | TC-16: 24-hour Stripe idempotency key prevents duplicate charges across currency contexts. | Critical |
| BC-08 | White-glove delivery option | Differentiates HomeStyle; premium pricing. Lead time clearly displayed at checkout. | High |
| BC-09 | Discount code application | Server-side re-validation at checkout submission (not just entry) prevents race condition exploits. | High |
| BC-10 | Wishlist with share & cart transfer | TC-12: Guest cart 30-day TTL. Wishlist permanent for registered users. | High |

### **13.3 Order, Returns & Post-Purchase**

| ID | Capability | Business Value \+ Time Constraints | Priority |
| :---- | :---- | :---- | :---- |
| BC-11 | Customer self-service order tracking | Reduces support contacts 40%+. TC-29: email retry ensures delivery confirmation received. | Critical |
| BC-12 | Refund status visible in customer order view | Eliminates 'where is my refund?' tickets. TC-23: ≤12 business day total resolution target. | Critical |
| BC-13 | Returns & exchange request portal | TC-19: 30-day window enforced server-side. TC-20: 14-day ship-back deadline. TC-21: 2-day admin review SLA. | Critical |
| BC-14 | Verified post-purchase review system | TC-24: Prompt sent 7 days post-delivery (BullMQ job). TC-25: 180-day submission window. TC-26: 48-hr moderation SLA. | High |

### **13.4 Administration & Operations**

| ID | Capability | Business Value \+ Time Constraints | Priority |
| :---- | :---- | :---- | :---- |
| BC-15 | Product catalogue management | Admin manages catalogue without dev involvement. Search index sync within 60 seconds of change. | Critical |
| BC-16 | Order processing & fulfillment | TC-17: 4-hr confirm SLA. TC-18: 48-hr dispatch SLA. SLA breach dashboard alerts to Super Admin. | Critical |
| BC-17 | Returns management & refund processing | TC-21: 2-day review SLA. TC-22: 1-day refund initiation SLA. TC-30: webhook alert at 24h. | Critical |
| BC-18 | Promotions & discount management | Discount codes validated at checkout submission time — not just at entry. | High |
| BC-19 | Business intelligence reporting | Real-time revenue data; SLA compliance metrics visible in dashboard. | High |
| BC-20 | Staff role & permission management | Granular module access. Admin session governed by TC-03 to TC-05. | High |

# **14\. System Email & Notification Triggers**

### **14.1 Customer-Facing Emails**

| \# | Trigger | Content Summary | TC Ref | Retry / Timing |
| :---- | :---- | :---- | :---- | :---- |
| E-01 | Registration — Email Verification | Welcome \+ verification link | TC-06 | Link expires 24h. Max 3 resend/hr. |
| E-02 | Password Reset | Secure reset link | TC-07 | Link expires 1h. Single use. Previous invalidated on new request. |
| E-03 | Order Confirmation | Order \#, items, totals, estimated delivery | TC-29 | Sent immediately on payment. Retry 3× at 1 min, 1× at 15 min. |
| E-04 | Order Confirmed by Admin | Order confirmed; being prepared | TC-17 | Sent when admin marks Confirmed (target: within 4 biz hrs). |
| E-05 | Shipping Confirmation | Carrier, tracking number, estimated delivery | TC-18, TC-29 | Sent immediately on Shipped status. TC-29 retry policy applies. |
| E-06 | Out for Delivery | Order out for delivery today | — | Via carrier webhook. Best-effort — not guaranteed. |
| E-07 | Delivery Confirmed \+ Review Prompt | Delivered confirmation. Review prompt sent 7 days later. | TC-24 | Scheduled BullMQ job fires 7 days post-Delivered status. One email per order. |
| E-08 | Return Request Received | Return \#XXXX received; under review | TC-21 | Sent within 5 min of submission. Alert if delayed \>15 min. |
| E-09 | Return Approved \+ Instructions | Return authorized; label attached / pickup scheduled | TC-20 | Customer must ship within TC-20: 14 days of approval. |
| E-10 | Return Rejected | Request declined with reason | — | Sent immediately on admin rejection. |
| E-11 | Refund Initiated | Refund of $XX initiated; 5–10 business days | TC-22, TC-30 | Sent on admin Stripe refund trigger. TC-30: alert if webhook not received in 24h. |
| E-12 | Wishlist Item Back in Stock | Item in wishlist now available | TC-28 | BullMQ hourly job. One notification per restock event per customer. |
| E-13 | Account Deactivation | Account deactivated; contact support if error | — | Sent immediately on admin deactivation. |

### **14.2 Admin & Internal Notifications**

| \# | Trigger | Notification Method | TC Ref | Recipient |
| :---- | :---- | :---- | :---- | :---- |
| A-01 | New order placed | Dashboard counter \+ email | TC-17 | Admin — Operations |
| A-02 | Order confirmation SLA breach | Dashboard 'Overdue' badge \+ Super Admin email | TC-17 | Super Admin |
| A-03 | Order dispatch SLA breach | Dashboard 'SLA Breached' red badge \+ Super Admin email | TC-18 | Super Admin |
| A-04 | Low stock threshold reached | Dashboard badge \+ email (TC-27: 24h cooldown per product) | TC-27 | Admin — Operations |
| A-05 | New return request | Dashboard alert \+ email | TC-21 | Admin — Operations |
| A-06 | Return review SLA breach | Dashboard 'Overdue Review' \+ Super Admin email | TC-21 | Super Admin |
| A-07 | Refund initiation SLA breach | Dashboard 'Refund Due' alert \+ email | TC-22 | Admin — Operations |
| A-08 | Stripe webhook not received | Dashboard alert \+ email (24h after refund trigger) | TC-30 | Admin — Operations |
| A-09 | New review pending moderation | Dashboard alert \+ email | TC-26 | Admin — Content |
| A-10 | Payment failed on order | Dashboard alert \+ email | — | Admin — Operations |

# **15\. Returns & Exchange — Business Rules**

| PENDING DECISION | Return window duration (default TC-19: 30 days) subject to client Legal confirmation per OD-09. All values below use 30 days as the working default. |
| :---- | :---- |

### **15.1 Eligibility Rules**

* TC-19: Return window is 30 days from confirmed delivery date. Calculated as: delivered\_at \+ 30 days. 'Request Return' button visible only while within window; hidden (not disabled) after. Server-side validation re-checks window at submission time to prevent race conditions.

* Items must be unassembled and in original packaging to qualify for a full cash refund.

* Assembled items: eligible for exchange or store credit only (subject to Legal confirmation).

* Damaged on arrival: full refund or replacement regardless of assembly status. Photo upload required.

* Custom-sized or special-order items: non-returnable (Phase 1 scope excludes custom items).

### **15.2 Return Reasons (Customer-Selectable)**

* Damaged on arrival

* Wrong item received

* Item does not match description or images on website

* Changed my mind

* Item arrived too late

* Other (free text field, required)

### **15.3 Time-Enforced Business Rules**

| TC Ref | Time Value | Business Rule |
| :---- | :---- | :---- |
| TC-19 | 30 days from delivery | Return eligibility window. Enforced server-side at both UI render and request submission. |
| TC-20 | 14 days from approval | Customer must ship item back. Admin alerted if no tracking update after 14 days. Return not auto-cancelled — admin decides. |
| TC-21 | 2 business days from submission | Admin review SLA. Breach → 'Overdue Review' badge \+ Super Admin email. |
| TC-22 | 1 business day from item receipt | Admin must initiate Stripe refund. Breach → 'Refund Due' alert in admin panel. |
| TC-23 | ≤12 business days total | Target resolution time: 2 days review \+ 1 day initiation \+ up to 10 days Stripe processing. |
| TC-30 | 24h after refund trigger | If Stripe refund.succeeded webhook not received → admin alert to verify in Stripe dashboard. |

### **15.4 Refund Rules**

* Full refund to original payment method for eligible returns within TC-19 window.

* Partial refund if item returned damaged or used — admin sets amount in admin panel.

* Store credit offered as alternative for assembled item returns or borderline cases.

* Shipping cost refunded only if return is due to business error (wrong item / damaged on arrival).

* Restocking fee may apply for large furniture returns (% to be confirmed by client).

### **15.5 Return Status State Machine**

| Status | Definition | Time Trigger |
| :---- | :---- | :---- |
| Return Requested | Customer submitted request. Pending admin review. | At submission (must be within TC-19: 30 days of delivery) |
| Under Review | Admin reviewing. SLA: TC-21: 2 business days. | At request creation |
| Return Approved | Admin approved. Return instructions sent. | On admin approval action |
| Return Rejected | Admin rejected with reason. | On admin rejection action |
| Item in Transit | Customer shipped item back. | Within TC-20: 14 days of approval |
| Item Received | Admin confirmed receipt at warehouse. | On admin confirmation action |
| Refund Initiated | Stripe refund triggered by admin. | Within TC-22: 1 business day of Item Received |
| Refund Completed | Stripe refund.succeeded webhook received. | TC-30: alert if webhook not received within 24h of initiation |

# **16\. Non-Functional Requirements**

| ID | Category | Requirement | Priority |
| :---- | :---- | :---- | :---- |
| NFR-01 | Performance | LCP \< 2.5s on product pages. Next.js SSR \+ CloudFront CDN. PageSpeed ≥ 80 on mobile. | Critical |
| NFR-02 | Scalability | 500 concurrent users at launch; scale to 5,000 within 12 months. Redis handles inventory holds (TC-13) atomically under load. | High |
| NFR-03 | Security | TLS 1.3 enforced. PCI DSS via Stripe — no card data on application servers. TC-01/TC-03: 15-min JWT. TC-10/TC-11: login lockout. Admin 2FA mandatory. | Critical |
| NFR-04 | Availability | 99.9% uptime SLA. TC-29 email retry on failure. TC-30 Stripe webhook monitoring with 24h alert. | High |
| NFR-05 | Accessibility | WCAG 2.1 Level AA. Validated via axe-core automated \+ manual audit pre-launch. | Critical |
| NFR-06 | SEO | Next.js SSR for all product/category pages. Auto-generated sitemap. Configurable meta tags. Canonical URLs. Structured data (JSON-LD). | High |
| NFR-07 | GDPR Compliance | No non-essential cookies before consent (TC-36: 12-month validity). Right to erasure processed within TC-34: 30 days. TC-35: backup purge within 90 days. | Critical |
| NFR-08 | CCPA Compliance | TC-37: 'Do Not Sell' opt-out takes effect immediately (same page load). Privacy policy updated. | Critical |
| NFR-09 | Mobile Responsiveness | Mobile-first CSS. All pages functional from 375px. Tested on iOS Safari 17+ and Android Chrome. | Critical |
| NFR-10 | API Design | REST API versioned from day one (/api/v1/). OpenAPI 3.0 spec required. JSON responses. Rate limiting on all public endpoints. | High |
| NFR-11 | Session Security | TC-01: JWT access token 15 min. TC-02: Refresh token 30 days with rotation (old token invalidated on use). TC-05: Admin inactivity timeout 30 min with 5-min countdown warning. | Critical |
| NFR-12 | Checkout Integrity | TC-13: Inventory hold uses Redis atomic SETNX operation. Concurrent checkout for same SKU resolved by first-write-wins. TC-16: Stripe idempotency key prevents duplicate charges. | Critical |
| NFR-13 | Data Retention | TC-31: customer PII retained 3 years from last activity. TC-32: financial records 7 years. TC-33: server logs 90 days rolling. Automated purge jobs required. | Critical |
| NFR-14 | Browser Support | Latest 2 versions of Chrome, Firefox, Safari, and Edge. IE11 not required. | High |

# **17\. Time Constraints & Validation Rules**

This section defines all time-based constraints for the HomeStyle platform. These requirements are integrated throughout this BRD (see Sections 8, 12, 13, 14, 15, and 16). This section serves as the single consolidated reference for developers, QA engineers, and test case authors.

| CONFIGURABLE VALUES | Values marked 'Yes — Admin' must be exposed as configurable settings in the admin System Settings panel. The Development Lead must confirm which values are hard-coded constants vs. configurable per OD-10. |
| :---- | :---- |

| LEGAL CONFIRMATION REQUIRED | Values marked 'Legal — confirm' in the Configurable column must be validated by the client's Legal team before implementation. Incorrect retention periods may constitute a GDPR violation. |
| :---- | :---- |

### **17.1 Authentication & Session**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-01 | JWT access token — customer | 15 minutes | No (security) | Silent refresh via TC-02 refresh token. If refresh also expired, redirect to login. Cart/page state preserved in session storage during redirect. |
| TC-02 | Refresh token — customer | 30 days | Yes — Admin (7/14/30/60 days) | User must re-authenticate. Token is rotated on every use — old token immediately invalidated. |
| TC-03 | JWT access token — admin | 15 minutes | No (security) | Silent refresh via TC-04. Shorter than customer token due to elevated privileges. |
| TC-04 | Refresh token — admin | 8 hours | Yes — Super Admin | Admin must re-authenticate. All unsaved form data lost — admin panel must auto-save drafts every 60 seconds. |
| TC-05 | Admin inactivity timeout | 30 minutes | Yes — Super Admin (15/30/60 min) | Session terminated on no mouse/keyboard activity. Countdown warning shown at 5 minutes remaining. |
| TC-06 | Email verification link | 24 hours | No | Link shows 'Expired' page with 'Resend' button. Max 3 resend attempts per hour per email address. |
| TC-07 | Password reset link | 1 hour | No | Link shows 'Expired or already used' page. Previous link invalidated when new one is requested. |
| TC-08 | OAuth state token (CSRF) | 10 minutes | No | OAuth callback after 10 minutes fails with generic error. User must restart social login flow. |
| TC-09 | S3 assembly PDF presigned URL | 15 minutes | No | URL returns HTTP 403\. Customer reloads product page to generate a fresh URL. New URL generated on each product page load. |
| TC-10 | Customer login lockout | 5 failed attempts → 15-minute lockout | No | 'Too many attempts. Try again in X minutes.' Counter resets after successful login. |
| TC-11 | Admin login lockout | 3 failed attempts → 30-minute lockout | No | Super Admin receives email notification. Super Admin can manually unlock via admin user management panel. |

### **17.2 Cart & Checkout**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-12 | Guest cart session lifetime | 30 days from last activity | No | Redis session and cart cookie expire silently. No notification — guest has no email on file. |
| TC-13 | Inventory hold (checkout initiated) | 15 minutes | No | Redis TTL key expires → hold released. Countdown shown at ≤5 min: 'Reserved for X:XX'. At 0:00: redirect to cart with 'Reservation expired — check availability.' |
| TC-14 | Checkout session hard expiry | 30 minutes total | No | Full checkout session (address \+ delivery \+ payment) expires. Prevent stale prices/tax. Customer returns to cart. |
| TC-15 | Stripe API confirmation timeout | 30 seconds | No | Show: 'Payment confirmation taking longer than expected. Check order history before retrying.' Prevents duplicate charges. |
| TC-16 | Stripe idempotency key window | 24 hours | No (Stripe default) | Same key within 24h returns original result — prevents duplicate charges on browser retry. |

### **17.3 Orders & Fulfillment**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-17 | Order confirmation SLA | 4 business hours from order placement | Yes — Admin | Order escalated to Super Admin dashboard with 'Overdue' badge. Super Admin receives email alert. |
| TC-18 | Order dispatch SLA | 48 business hours from confirmation | Yes — Admin | Order flagged 'SLA Breached' with red badge in admin order list. Super Admin receives email alert. |

### **17.4 Returns & Refunds**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-19 | Return eligibility window | 30 days from delivery | Yes — Admin (subject to Legal) | 'Request Return' button hidden. 'Return window closed on \[date\]' shown. Server-side validation at submission. |
| TC-20 | Customer return shipping deadline | 14 days from approval | Yes — Admin | Admin alerted if no tracking update. Return not auto-cancelled — admin decides whether to extend. |
| TC-21 | Admin return review SLA | 2 business days from submission | Yes — Admin | Return flagged 'Overdue Review' with red badge. Super Admin receives email alert. |
| TC-22 | Admin refund initiation SLA | 1 business day from item receipt | Yes — Admin | 'Refund Due' alert shown in admin panel. Super Admin notified. |
| TC-23 | Total refund resolution target | ≤12 business days | Informational | Business benchmark only. Not a hard system enforced timer. |

### **17.5 Reviews & Notifications**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-24 | Review prompt email delay | 7 days after delivery confirmation | Yes — Admin | BullMQ scheduled job fires 7 days post-Delivered. One email per order, not per product. |
| TC-25 | Review submission window | 180 days from delivery | Yes — Admin | 'Write a Review' option hidden after 180 days. No second reminder sent. |
| TC-26 | Review moderation SLA | 48 hours; auto-approve at 72h | Yes — Admin (can set to 'hold all') | Reviews not actioned within 48h are flagged. Auto-approve fires at 72h by default. |
| TC-27 | Low-stock alert cooldown | 24 hours per product | Yes — Admin | Prevents alert flood if stock fluctuates around the threshold. |
| TC-28 | Back-in-stock notification | Within 1 hour (hourly BullMQ job) | No | Customers on 'Notify Me' list emailed. List cleared after notification — must re-subscribe. |
| TC-29 | Transactional email retry | 3× at 1 min; 1× at 15 min | No | If all retries fail: admin alert. Customer can re-request confirmation from 'My Orders'. |
| TC-30 | Stripe webhook expected receipt | Alert if not received within 24h of refund trigger | No | Admin alerted to verify refund status in Stripe dashboard. |

### **17.6 Compliance & Data Retention**

| TC Ref | Constraint | Value | Configurable? | Behaviour on Expiry / Breach |
| :---- | :---- | :---- | :---- | :---- |
| TC-31 | Customer PII data retention | 3 years from last login or purchase | Legal — confirm | Monthly BullMQ job identifies inactive accounts. 30-day warning email sent. Deletes if no activity in warning window. |
| TC-32 | Order / financial records retention | 7 years from order date | Legal — confirm | Cannot be deleted on customer erasure request during window. PII fields anonymised; financial records retained. |
| TC-33 | Server / application log retention | 90 days rolling | DevOps config | Auto-purged after 90 days. IP addresses in request logs are PII under GDPR. |
| TC-34 | GDPR erasure processing deadline | Within 30 days of erasure request | No (legal maximum) | Automated erasure job: anonymises PII, retains financial records. Confirmation email sent within 30 days. |
| TC-35 | Backup purge after erasure | Within 90 days of erasure | No | Purged on next backup rotation cycle after TC-34. Documented in deletion audit log. |
| TC-36 | Cookie consent validity | 12 months from consent date | No (GDPR best practice) | Consent re-displayed after 12 months. Immediately invalidated on material policy change. |
| TC-37 | CCPA opt-out effect | Immediate (same page load) | No (legal requirement) | Tracking pixels (GA4, Meta Pixel) disabled within same page load on opt-out. |
| TC-38 | Marketing unsubscribe processing | Within 10 business days | No (legal maximum) | Suppression list maintained. Email address blocked from re-addition. |


# **18\. Domain-Specific Considerations**

### **18.1 Product Complexity & DB Implications**

* Products have multiple configurable variant dimensions: colour, material/fabric, finish, leg type, and size.

* Each variant combination has its own SKU, price delta, stock level, and weight — requiring a variants table linked to a parent product.

* Shopify supports up to 3 variant options natively; custom build has no such limit.

* Dimension tables (W × D × H in inches and centimetres) stored as structured data, not free text, to enable filtering.

* Assembly instruction PDFs associated to parent product model via a product\_assets table; served via TC-09: 15-minute S3 presigned URLs.

### **18.2 Shipping Complexity**

* Small accessories (\< 30 lbs): standard parcel carriers (FedEx/UPS for US; DHL/DPD for EU).

* Large furniture (sofas, beds, wardrobes — \> 100 lbs): LTL freight carriers; 7–14 business day lead times. Delivery window displayed at checkout.

* White-glove delivery: carrier delivers to room of choice, performs basic assembly, removes packaging. Premium charge; not available to all ZIP/postal codes.

* Remote area surcharges stored in admin-managed surcharge table or fetched from carrier API; reflected at checkout.

### **18.3 Tax Handling**

* United States: Sales tax calculated at checkout by state/county via Stripe Tax or TaxJar. Nexus states confirmed per CR-10.

* European Union: VAT included in displayed price. Per-country VAT rates in DB, managed via admin panel. EU OSS registration recommended.

* United Kingdom: Standard UK VAT (20%). Separate market with GBP pricing and UK shipping zones.

### **18.4 Inventory States & Time-Related Behaviour**

| State | Definition | Time / Timer Behaviour |
| :---- | :---- | :---- |
| In Stock | stock\_qty \> low\_stock\_threshold | TC-27: Low-stock alert with 24h cooldown when stock falls to threshold. No time-based state transition. |
| Low Stock | stock\_qty ≤ threshold (admin-configurable) | 'Only X left' badge. TC-27: Alert with 24h cooldown. Add to Cart still enabled. |
| Out of Stock | stock\_qty \= 0, no active hold | 'Notify Me' button. TC-28: Back-in-stock email sent within 1 hour of restock via hourly BullMQ job. |
| Held (Checkout) | stock\_qty \> 0 but inventory\_hold exists in Redis | TC-13: Hold expires after 15 minutes via Redis TTL. Other checkout attempts for same SKU see reduced available qty. |
| Pre-Order | stock\_qty \= 0 and pre\_order\_enabled \= true (pending OD-03) | Estimated restock date displayed. On restock: TC-28 applies. Pre-order queue processed FIFO. |
| Discontinued | is\_archived \= true in DB | Product page 301-redirects to parent category immediately on archive. Not purchasable or searchable. |

# **19\. Key Performance Indicators (KPIs)**

### **19.1 Business KPIs**

| KPI | Definition | Target (6 Months) | Source |
| :---- | :---- | :---- | :---- |
| Online Revenue Contribution | % of total revenue from online channel | ≥ 30% | Admin Reports |
| Average Order Value (AOV) | Average revenue per completed order | ≥ $350 / €320 | Admin Reports |
| New vs. Returning Customer Ratio | % of orders from new vs. returning buyers | Track baseline Month 2 | Admin Reports / GA4 |
| Customer Acquisition Cost (CAC) | Marketing spend ÷ new customers acquired | Establish baseline | Marketing \+ Reports |

### **19.2 Product KPIs**

| KPI | Definition | Target (6 Months) | Source |
| :---- | :---- | :---- | :---- |
| Conversion Rate | % of sessions resulting in a completed order | ≥ 2.5% | GA4 \+ Admin Reports |
| Cart Abandonment Rate | % of carts not completed at checkout | ≤ 65% | Custom Analytics |
| Checkout Timeout Rate | % of checkout sessions expired (TC-14) | ≤ 3% (monitor only) | Custom Analytics |
| Avg. Product Rating | Mean star rating across all reviewed products | ≥ 4.3 / 5.0 | Review Module |

### **19.3 Operational KPIs**

| KPI | Definition | Target (6 Months) | Source |
| :---- | :---- | :---- | :---- |
| Order Confirmation SLA Compliance | % of orders confirmed within TC-17: 4 biz hrs | ≥ 95% | Admin Order Reports |
| Order Dispatch SLA Compliance | % of orders shipped within TC-18: 48 biz hrs | ≥ 95% | Admin Order Reports |
| Return Rate | % of delivered orders returned | ≤ 8% | Returns Reports |
| Return Resolution Time | Time from return request to refund completed (vs. TC-23) | ≤ 12 business days | Returns Reports |
| Page Load Time (LCP) | Largest Contentful Paint on product pages | \< 2.5 seconds | Core Web Vitals |
| Support Contact Rate | % of orders generating a support contact | ≤ 5% | Support Logs |

# **20\. Stakeholder Register & RACI**

### **20.1 Stakeholder Register**

| Stakeholder | Role | Key Responsibilities | Influence | Interest |
| :---- | :---- | :---- | :---- | :---- |
| Business Owner | Project Sponsor | Approves scope, budget, all Open Business Decisions (Section 9\) including OD-09 (return window) and OD-10 (configurable TC values). | High | High |
| Product Owner / BA | Requirements Lead | Owns BRD, SRS, backlog, and all TC definitions. Bridge between business intent and technical execution. | High | High |
| Development Lead | Technical Architect | Architecture decisions, tech stack, API design, DB schema, feasibility review of all TC values, effort estimation. | High | High |
| UX/UI Designer | Design Lead | Wireframes, design system, checkout countdown timer UI (TC-13), SLA breach UI indicators in admin panel. | Medium | High |
| QA Lead | Quality Assurance | Test strategy, test cases for all TC values, regression testing, pre-launch UAT sign-off. | Medium | High |
| Marketing Manager | Growth & Content | SEO requirements, promotions, CMS content, email template copy approval. | Medium | Medium |
| Warehouse / Ops Team | Fulfillment Operations | Validate order management, SLA thresholds (TC-17, TC-18), and returns process requirements. | Low | High |
| Legal / Compliance | Compliance Advisory | GDPR, CCPA, Privacy Policy, Terms. Must confirm TC-31 to TC-38 data retention values before implementation. | Medium | High |
| Finance | Financial Oversight | Currency model (OD-02), refund policy, tax nexus, confirmation of TC-32 (7-year financial record retention). | Medium | Medium |
| End Customers | Primary Users | Usability testing; post-launch satisfaction surveys; review system users. | Low | High |

### **20.2 RACI Matrix — Key Project Activities**

| Activity | Biz Owner | PO/BA | Dev Lead | UX/UI | QA | Legal | Finance |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| BRD v4.0 Approval | A | R | C | C | I | C | I |
| Open Business Decisions (incl. OD-09, OD-10) | A/R | R | C | I | I | C | C |
| TC values Legal review (TC-31 to TC-38) | A | C | I | I | I | R | C |
| TC values Dev feasibility review | I | C | R/A | I | C | I | I |
| SRS Sign-Off | A | R | C | C | C | I | I |
| Tech Stack & Architecture | I | C | R/A | C | C | I | I |
| UI/UX Design & Wireframes | I | C | C | R/A | C | I | I |
| Backend / API Development | I | C | R/A | I | C | I | I |
| GDPR / Legal Review | A | C | I | I | I | R | I |
| UAT Sign-Off | A | C | C | C | R | I | I |
| Go-Live Approval | A/R | C | C | C | C | C | I |

*R \= Responsible   A \= Accountable   C \= Consulted   I \= Informed*

# **21\. Approvals**

By signing below, the named stakeholder confirms they have reviewed BRD v4.0 — including all time constraints and validation rules in Section 17 — and approve it as the basis for proceeding to the Software Requirements Specification (SRS) phase.

| NOTE | Before signing, Legal must confirm data retention values TC-31 to TC-38. The Development Lead must confirm technical feasibility of all TC values. Business Owner must resolve Open Decisions OD-09 and OD-10. |
| :---- | :---- |

| Role | Name | Title | Signature | Date |
| :---- | :---- | :---- | :---- | :---- |
| Project Sponsor / Business Owner |  |  |  |  |
| Product Owner / BA |  |  |  |  |
| Development Lead |  |  |  |  |
| UX/UI Lead |  |  |  |  |
| QA Lead |  |  |  |  |
| Legal / Compliance |  |  |  |  |
| Finance |  |  |  |  |

*— End of Document — HomeStyle BRD v4.0  |  Custom Web Application  |  Confidential*
