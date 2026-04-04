**VALDRIS — Project Charter**	CONFIDENTIAL  |  v1.0  |  April 2024

**VALDRIS**

*Precision Forged. Time Mastered.*

**PROJECT CHARTER**

E-Commerce Website — Luxury Watch Brand


|**Document type**|Project Charter|
| :- | :- |
|**Version**|1\.0 — Initial Release|
|**Date**|April 2024|
|**Prepared by**|Business Analyst — Valdris Digital|
|**Classification**|Confidential|
|**Project code**|VLD-ECOM-2024|



# **1. Executive Summary**
Valdris Watch Co. is a US-based luxury watch brand inspired by Swiss horological tradition. The company currently operates through a network of 3–5 boutique retail partners across the United States. This project charters the design and development of a direct-to-consumer (DTC) e-commerce website — valdriswatches.com — to establish Valdris as a digitally native luxury brand while preserving the white-glove experience its customers expect.

The e-commerce platform is not a commodity shopping site. It is a digital flagship store — the online equivalent of a boutique on Fifth Avenue. Every requirement, every design decision, and every system constraint in this project must reflect that positioning.

*Business rule: Valdris does not compete on price. The platform must never undermine brand equity through sitewide discount mechanics, flash sales, or low-cost UX patterns. Value is delivered through service, exclusivity, and experience.*

# **2. Brand Definition**
## **2.1 Brand Identity**

|**Brand name**|Valdris Watch Co.|
| :- | :- |
|**Tagline**|Precision Forged. Time Mastered.|
|**Website**|valdriswatches.com|
|**Segment**|Upper-luxury ($2,500 – $25,000 per timepiece)|
|**Heritage narrative**|Swiss-inspired precision engineering, American ownership|
|**Brand personality**|Authoritative, refined, understated, confident|
|**Visual language**|Deep navy, gold accents, clean white space, serif typography|

## **2.2 Product Collections**
Valdris organises its catalog into four distinct collections, each serving a different customer intent:

|**Collection**|**Positioning**|**Price range (USD)**|
| :- | :- | :- |
|**Meridian**|Dress watches — boardroom elegance|$2,500 – $8,000|
|**Apex**|Sport & diver — active precision|$3,500 – $12,000|
|**Heirloom**|Limited editions — numbered, collectible|$8,000 – $25,000|
|**Atelier**|Commission — engraving, custom dials|$15,000+ (bespoke quote)|

## **2.3 Target Customer Personas**

|**Persona A — The Established Collector**|**Persona B — The Aspirational Achiever**|
| :- | :- |
|Male, 42–55, net worth $2M+|Male/Female, 32–42, HHI $200k+|
|Owns 5+ luxury watches|First or second luxury watch purchase|
|Values provenance, limited editions|Values brand story, gifting experience|
|Expects personal advisor, fast checkout|Needs education, detailed product content|
|High-value reorder customer|Acquisition target — may become collector|

*System thinking: Two personas with opposite needs — the Collector wants speed and exclusivity, the Achiever wants education and confidence-building. Our system must serve both without compromising either. This tension will drive several UX and content requirements in the BRD.*


# **3. Project Objectives**
## **3.1 Primary Objectives**
- Launch valdriswatches.com as Valdris's primary DTC sales channel by Q4 2024
- Enable end-to-end online purchase of Valdris timepieces with serialized inventory tracking
- Deliver a luxury-grade digital experience that reflects the brand's physical boutique standard
- Establish the digital foundation for a retail partner portal (Phase 2)
- Achieve PCI-DSS compliance for all payment processing from day one

## **3.2 Secondary Objectives**
- Build a CRM-connected customer profile system to enable post-purchase clienteling
- Create editorial content infrastructure (brand story, collection pages, watchmaking craft)
- Enable appointment booking with personal advisors for high-value consultations
- Establish analytics baseline — traffic, conversion rate, AOV — within 60 days of launch

*Business driver: The primary KPI for this project is not traffic — it is Average Order Value (AOV) and conversion rate among qualified luxury buyers. A site with 500 high-intent visitors converting at 3% is more valuable than 50,000 visitors converting at 0.1%.*

# **4. Project Scope**
## **4.1 In-Scope (Phase 1)**

|**Customer-facing (frontend)**|**Admin & operations (backend)**|
| :- | :- |
|Homepage — brand editorial + featured collections|Product catalog management (serialized SKUs)|
|Collection listing pages with filter/sort|Order management & fulfillment workflow|
|Product detail page (PDP) — full specs, images, serial|Customer account management (CRM-lite)|
|Customer registration, login, account dashboard|Inventory management — per-serial tracking|
|Shopping cart and checkout flow|Payment management — Stripe integration|
|Online payment — credit/debit card, ACH|Shipping & logistics management|
|Order tracking and history|Retail partner management (read-only portal)|
|Advisor appointment booking (Calendly integration)|Reports & analytics dashboard|
|Authenticity certificate download|CMS — brand pages, editorial content|
|Wishlist and gift registry|Role-based admin access control|
|Brand story + watchmaking editorial pages|Email notification management|

## **4.2 Out of Scope (Phase 1)**
- Retail partner inventory sync (Phase 2 — partner portal)
- Mobile native application (iOS / Android)
- International shipping or multi-currency pricing
- Full Atelier commission workflow (online quotation engine)
- Real-time watch authentication via NFC / QR
- Pre-owned / secondary market trading
- Buy Now Pay Later (BNPL) payment options
- Live chat or AI chatbot

*System thinking: Out-of-scope is as important as in-scope. Every item on this list is a future scope decision, not a rejection. Documenting it here prevents scope creep and keeps the team aligned. Ask: if we added BNPL, which components change? Answer: checkout flow, payment gateway config, order model, and fraud rules. That's a large surface — correct to defer.*


# **5. Stakeholder Register**
The following stakeholders have been identified for this project. All sign-off requirements are listed in Section 9.

|**Role**|**Name / TBD**|**Responsibilities**|**Influence**|
| :- | :- | :- | :- |
|Project Sponsor|CEO, Valdris|Final approval, budget authority, brand decisions|High|
|Business Owner|VP Commercial|Requirements owner, acceptance sign-off|High|
|Project Manager|TBD|Timeline, resources, risk management|High|
|Business Analyst|TBD|Requirements elicitation, documentation|Medium|
|System Architect|TBD|Platform decisions, integration design|High|
|UX Lead|TBD|Wireframes, user journeys, brand alignment|Medium|
|Dev Lead|TBD|Technical delivery, effort estimation|Medium|
|QA Lead|TBD|Test strategy, acceptance criteria|Medium|
|Retail Partners (3–5)|Boutique owners|Inventory visibility requirements|Low|
|End Customers|Watch buyers|Usability, experience validation|Low|

# **6. Business Rules & Regulatory Requirements**
## **6.1 Core Business Rules**
The following rules are non-negotiable constraints that must be reflected in all system design decisions. They are not preferences — they are enforced policies.

|**Rule ID**|**Business Rule**|**Source**|**Priority**|**Impacted Areas**|
| :- | :- | :- | :- | :- |
|BR-001|Each watch has a unique serial number. One serial = one sellable unit.|Brand policy|Critical|Inventory, PDP, Order, ERD|
|BR-002|No sitewide discount codes or percentage-off promotions.|Brand policy|Critical|Checkout, Promotions module|
|BR-003|Orders above $10,000 USD trigger a manual fraud review before fulfillment.|Risk policy|Critical|Order mgmt, Checkout, Notifications|
|BR-004|A customer may not purchase the same limited-edition (Heirloom) SKU more than once.|Brand policy|High|Cart, Checkout, Order history|
|BR-005|Authenticity certificate PDF must be generated and available for every completed order.|Brand policy|High|Order, Product, Document gen|
|BR-006|Retail partner accounts have read-only inventory visibility. They cannot modify prices.|Channel policy|High|Partner portal, Product module|
|BR-007|Service pricing (strap replacement, servicing) is quoted manually — not automated.|Ops policy|Medium|After-sales, CMS|
|BR-008|All product prices are displayed and charged in USD only.|Market policy|Critical|All pages, Checkout, Reports|

## **6.2 Regulatory & Compliance Requirements**

|**Reg ID**|**Regulation / Standard**|**Jurisdiction**|**Priority**|**Impacted Areas**|
| :- | :- | :- | :- | :- |
|REG-001|PCI-DSS v4.0 — Payment card data security standard|US / Global|Critical|Checkout, Payment, Hosting|
|REG-002|CCPA — California Consumer Privacy Act (customer data rights)|California, US|Critical|Registration, CRM, Privacy policy|
|REG-003|CAN-SPAM Act — email marketing compliance|US Federal|High|Email module, Marketing|
|REG-004|ADA / WCAG 2.1 AA — Web accessibility standard|US Federal|High|All frontend pages|
|REG-005|FinCEN / BSA — AML reporting for luxury goods >$10,000|US Federal|Medium|Order mgmt, KYC workflow|
|REG-006|Sales tax collection — Nexus-based (TaxJar or Avalara)|US State|Critical|Checkout, Reporting|
|REG-007|SSL/TLS — HTTPS required across all pages|Industry standard|Critical|Infrastructure, Hosting|

*Regulatory note for portfolio: REG-001 (PCI-DSS) means Valdris must never store raw card numbers. Stripe handles card tokenization — the system only stores a Stripe payment method ID. This is a deliberate architectural decision that reduces compliance scope from SAQ D to SAQ A-EP. Document this in the SRS NFRs.*


# **7. Assumptions, Constraints & Risks**
## **7.1 Assumptions**
- Physical warehouse and inventory operations are already established and operational
- Valdris has an existing Stripe merchant account or will establish one before development begins
- Product photography (high-resolution, multi-angle) will be provided by the brand for all SKUs
- 3–5 retail partner boutiques have been identified and will be onboarded for the portal
- The development team has experience with luxury e-commerce platforms (Shopify Plus, commercetools, or custom)
- A domain name (valdriswatches.com) is owned or will be acquired before project kick-off

## **7.2 Constraints**
- Delivery deadline: Q4 2024 (October 31st target)
- Budget ceiling: TBD — to be confirmed by Project Sponsor at charter sign-off
- Platform must support US geography and USD currency only in Phase 1
- No cash-on-delivery, BNPL, or cryptocurrency payment options in Phase 1
- Brand visual guidelines are fixed — the platform must conform to Valdris style guide

## **7.3 Risk Register**

|**Risk**|**Likelihood**|**Impact**|**Mitigation**|
| :- | :- | :- | :- |
|Scope creep from luxury feature requests|High|High|Strict change control process; all new requests evaluated against Phase 1 scope|
|Payment fraud on high-value orders|Medium|Critical|BR-003 manual review + Stripe Radar fraud rules + order velocity checks|
|Photography assets delayed by brand team|Medium|Medium|Placeholder asset pipeline; brand team asset delivery milestone in project plan|
|PCI-DSS audit delays launch|Low|High|Use Stripe-hosted fields (SAQ A-EP scope) to minimize audit surface; engage QSA early|

# **8. High-Level Timeline**

|**Phase**|**Activities**|**Target dates**|**Key milestone**|
| :- | :- | :- | :- |
|Discovery|Brand definition, charter, BRD, domain research|Week 1|**BRD approved**|
|Design|SRS, BPMN, use case, ERD, wireframes|Week 2|**SRS & wireframes approved**|
|Development Sprint 1|Platform setup, product catalog, authentication|Weeks 3–5|**Catalog live (staging)**|
|Development Sprint 2|Cart, checkout, payment integration, order mgmt|Weeks 6–8|**End-to-end checkout (staging)**|
|Development Sprint 3|Admin panel, partner portal, analytics|Weeks 9–11|**Admin complete**|
|UAT & QA|Full test cycle, bug fixing, performance testing|Weeks 12–13|**UAT sign-off**|
|Launch Prep|Content load, SEO, DNS cutover, PCI review|Week 14|**Go-live: valdriswatches.com**|


# **9. Approval & Sign-Off**
By signing below, each approver confirms they have reviewed this Project Charter, agree with the stated objectives and scope, and authorize the project to proceed to the Business Requirements Document phase.

|**Role**|**Name**|**Date**|**Signature**|
| :- | :- | :- | :- |
|**Project Sponsor**| | | |
|**Business Owner**| | | |
|**Project Manager**| | | |
|**System Architect**| | | |
|**UX Lead**| | | |
|**QA Lead**| | | |

# **10. System Thinking Notes — BA Annotations**
This section documents the analytical reasoning behind key decisions in this charter. It is intended for BA practice and portfolio demonstration.

**10.1 Why the business model affects system architecture**

Valdris operates DTC + retail partners. This creates two distinct user types beyond the end customer: the admin (Valdris staff) and the retail partner (boutique owner). Every data model decision — product, inventory, order — must account for which actor can read vs. write it. This is not a simple admin panel; it is a multi-actor access control problem.

*System thinking: If a retail partner can see inventory, can they also see customer data? No — that's a privacy boundary. Defining this early prevents a data model design that accidentally exposes customer PII to third-party boutiques. This becomes a non-functional requirement in the SRS.*

**10.2 Why serialized inventory changes everything**

Most e-commerce systems treat inventory as a count: 50 units of SKU-XYZ. Valdris cannot. Each watch is unique — serial number, authenticity certificate, production batch. This means the inventory entity is not a counter; it is a registry. One row per physical watch. This affects the product data model, the cart (you reserve a specific serial, not a quantity), the order (you fulfill a specific serial), and the certificate generation (tied to a serial, not an order number).

*System thinking: What breaks if we ignored serialization and treated watches like apparel? Answer: two customers could technically 'buy' the same physical watch if inventory wasn't locked to a serial at cart-add time. Race condition at checkout. This is a critical concurrency requirement for the SRS.*

**10.3 The $10,000 fraud review rule is a process, not just a policy**

BR-003 says orders above $10,000 trigger manual review. This sounds simple but has wide system implications: the order status model needs a 'Pending Review' state. The fulfillment team needs a dashboard showing flagged orders. Notifications must fire to both the customer ('Your order is being verified — expect confirmation within 2 business hours') and the ops team. The review outcome (approve / reject) must update the order and trigger downstream actions. One business rule = multiple system components.

**10.4 Trade-off: platform choice**

A luxury DTC site of this complexity could be built on Shopify Plus (fast, high cost, some constraints), commercetools (flexible, headless, expensive to build), or a custom stack (maximum control, highest cost and risk). For Phase 1, Shopify Plus is the pragmatic choice — it handles PCI-DSS, Stripe, and core e-commerce out of the box. The BA's role is to document this trade-off explicitly so the business can make an informed decision. Not choosing a platform is itself a risk that should be resolved before Sprint 1.
Valdris Watch Co. — Proprietary & Confidential	Page — 
