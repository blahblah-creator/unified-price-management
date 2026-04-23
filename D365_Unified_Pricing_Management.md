# Dynamics 365 Unified Pricing Management - Complete Guide

## Overview

Unified Pricing Management in Dynamics 365 is a comprehensive system that allows organizations to manage pricing across multiple channels, customer segments, and geographies from a single, centralized platform.

---

## 1. HIGH-LEVEL SYSTEM ARCHITECTURE

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DYNAMICS 365 UNIFIED PRICING SYSTEM                      │
└─────────────────────────────────────────────────────────────────────────────┘

                              ┌──────────────────────┐
                              │   PRICE ENGINE       │
                              │   (Core Service)     │
                              └──────────┬───────────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    │                    │                    │
                    ▼                    ▼                    ▼
         ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
         │ PRICING RULES    │  │  PRODUCT PRICE   │  │ CUSTOMER PRICING │
         │ & CONDITIONS     │  │  MANAGEMENT      │  │ HIERARCHIES      │
         └──────────────────┘  └──────────────────┘  └──────────────────┘
                    │                    │                    │
                    └────────────────────┼────────────────────┘
                                         │
                              ┌──────────▼───────────┐
                              │  PRICING DECISION    │
                              │  ENGINE              │
                              └──────────┬───────────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    │                    │                    │
                    ▼                    ▼                    ▼
         ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
         │  SALES ORDER     │  │   INVOICE        │  │  POINT OF SALE   │
         │  MANAGEMENT      │  │  GENERATION      │  │  (POS)           │
         └──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## 2. PRICING RULES & HIERARCHY SETUP

```
┌──────────────────────────────────────────────────────────────────────────┐
│                      PRICING HIERARCHY STRUCTURE                         │
└──────────────────────────────────────────────────────────────────────────┘

                         ┌─────────────────────┐
                         │   BASE PRODUCT      │
                         │   PRICE (LIST)      │
                         └──────────┬──────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
        ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
        │ CUSTOMER GROUP  │ │   CUSTOMER      │ │   VOLUME        │
        │ PRICING         │ │   SPECIFIC      │ │   DISCOUNTS     │
        │ (Tier-based)    │ │   PRICING       │ │   (Qty > X)     │
        └────────┬────────┘ └────────┬────────┘ └────────┬────────┘
                 │                   │                   │
                 └───────────────────┼───────────────────┘
                                     │
                              ┌──────▼──────┐
                              │ TRADE DEALS │
                              │ MIX & MATCH │
                              │ BUNDLES     │
                              └──────┬──────┘
                                     │
                              ┌──────▼──────────┐
                              │ FINAL PRICE     │
                              │ CALCULATION     │
                              └─────────────────┘
```

### Key Components:
- **Base Product Price**: List price for all products
- **Customer Group Pricing**: Tiered pricing based on customer classification
- **Customer-Specific Pricing**: Individual pricing agreements
- **Volume Discounts**: Quantity-based pricing adjustments
- **Trade Deals**: Special promotions and bundle pricing
- **Final Price**: Result of all applied rules and conditions

---

## 3. PRICING RULE ENGINE - DATA FLOW

```
┌──────────────────────────────────────────────────────────────────────────┐
│                     PRICING CALCULATION PROCESS                          │
└──────────────────────────────────────────────────────────────────────────┘

INPUT DATA:
┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│  PRODUCT    │  │  CUSTOMER   │  │  QUANTITY   │  │  CHANNEL    │
│  ID         │  │  ID         │  │  ORDERED    │  │  / STORE    │
└──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
       │                │                │                │
       └────────────────┼────────────────┼────────────────┘
                        │
                   ┌────▼─────┐
                   │  LOOKUP   │
                   │  PRICING  │
                   │  RULES DB │
                   └────┬─────┘
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
   ┌─────────┐    ┌─────────┐    ┌──────────┐
   │ Apply   │    │ Apply   │    │ Apply    │
   │ BASE    │    │ DISCOUNT│    │ TAXES    │
   │ PRICE   │    │ RULES   │    │ & FEES   │
   └────┬────┘    └────┬────┘    └────┬─────┘
        │              │              │
        └──────────────┼──────────────┘
                       │
            ┌──────────▼───────────┐
            │  RECALCULATE &       │
            │  VALIDATE PRICING    │
            └──────────┬───────────┘
                       │
            ┌──────────▼───────────┐
            │  FINAL PRICE OUTPUT  │
            │  (Unit + Line Total) │
            └──────────────────────┘
```

### Process Steps:
1. **Input Collection**: Gather product, customer, quantity, and channel data
2. **Rules Lookup**: Query pricing rules database
3. **Base Price Application**: Start with list price
4. **Discount Rules**: Apply customer, volume, and promotional discounts
5. **Tax Calculation**: Calculate jurisdictional taxes
6. **Validation**: Ensure pricing meets constraints
7. **Final Output**: Return calculated unit and line prices

---

## 4. PRICING COMPONENTS & MODULES

```
┌──────────────────────────────────────────────────────────────────────────┐
│              UNIFIED PRICING MANAGEMENT COMPONENTS                       │
└──────────────────────────────────────────────────────────────────────────┘

    ┌─────────────────────────────────────────────────────────┐
    │           PRICING CONFIGURATION MODULE                 │
    │                                                         │
    │  ┌────────────────┐  ┌────────────────┐               │
    │  │ Price Lists    │  │ Price Rules    │               │
    │  │ - Base Prices  │  │ - Formulas     │               │
    │  │ - Date ranges  │  │ - Conditions   │               │
    │  └────────────────┘  └────────────────┘               │
    │                                                         │
    │  ┌────────────────┐  ┌────────────────┐               │
    │  │ Discounts      │  │ Trade Deals    │               │
    │  │ - Customer     │  │ - Mix & Match  │               │
    │  │ - Volume       │  │ - Bundle       │               │
    │  │ - Seasonal     │  │ - Attributes   │               │
    │  └────────────────┘  └────────────────┘               │
    │                                                         │
    └────────┬─────────────────────────────────────────────┘
             │
             │ Configuration Data
             │
    ┌────────▼─────────────────────────────────────────────┐
    │      PRICING DATABASE / DATA LAYER                   │
    │                                                       │
    │  • Price Master Records                              │
    │  • Customer Price Groups                             │
    │  • Channel-specific Pricing                          │
    │  • Historical Pricing Data                           │
    │                                                       │
    └────────┬─────────────────────────────────────────────┘
             │
             │ Query & Retrieve
             │
    ┌────────▼─────────────────────────────────────────────┐
    │    PRICING CALCULATION ENGINE (Core Logic)          │
    │                                                       │
    │  1. Identify applicable rules                        │
    │  2. Calculate discounts                              │
    │  3. Apply taxes & surcharges                         │
    │  4. Validate pricing constraints                     │
    │  5. Return calculated price                          │
    │                                                       │
    └────────┬─────────────────────────────────────────────┘
             │
             │ Final Price
             │
    ┌────────▼─────────────────────────────────────────────┐
    │     EXECUTION LAYER (Integration Points)            │
    │                                                       │
    │  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
    │  │  Sales   │  │  Retail  │  │ B2B      │           │
    │  │  Orders  │  │  POS     │  │ Commerce │           │
    │  └──────────┘  └──────────┘  └──────────┘           │
    │                                                       │
    └─────────────────────────────────────────────────────┘
```

### Component Breakdown:

#### Pricing Configuration Module
- **Price Lists**: Define base prices with date ranges
- **Price Rules**: Business formulas and conditions
- **Discounts**: Customer, volume, and seasonal adjustments
- **Trade Deals**: Mix & match, bundles, and attribute-based promotions

#### Pricing Database/Data Layer
- Master records for all pricing data
- Customer segmentation and pricing groups
- Channel and geography-specific pricing
- Historical pricing for auditing

#### Pricing Calculation Engine
- Core logic for price determination
- Rule evaluation and application
- Tax and fee calculations
- Constraint validation

#### Execution Layer
- Integration with sales orders
- POS systems
- E-commerce platforms
- B2B portals

---

## 5. CHANNEL-SPECIFIC PRICING SETUP

```
┌──────────────────────────────────────────────────────────────────────────┐
│              MULTI-CHANNEL UNIFIED PRICING ARCHITECTURE                  │
└──────────────────────────────────────────────────────────────────────────┘

                      ┌────────────────────────┐
                      │  PRICING ENGINE CORE   │
                      │  (Unified Platform)    │
                      └────────────┬───────────┘
                                   │
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
        ▼                          ▼                          ▼
   ┌─────────────┐         ┌─────────────┐         ┌─────────────┐
   │   D365      │         │   RETAIL    │         │   E-COM     │
   │   COMMERCE  │         │   STORE/POS │         │   PORTAL    │
   │   (B2B/B2C) │         │   CHANNEL   │         │   CHANNEL   │
   └──────┬──────┘         └──────┬──────┘         └──────┬──────┘
          │                       │                       │
   ┌──────▼──────┐         ┌──────▼──────┐         ┌──────▼──────┐
   │ Catalog     │         │ Retail      │         │ Online      │
   │ Prices      │         │ Prices      │         │ Prices      │
   │ - List      │         │ - Markdown  │         │ - Dynamic   │
   │ - Tiered    │         │ - Promotions│         │ - A/B Test  │
   │ - Customer  │         │ - Store-Specific      │ - Geo-based │
   └──────┬──────┘         └──────┬──────┘         └──────┬──────┘
          │                       │                       │
          └───────────────┬───────┴───────────────┬───────┘
                          │                       │
                   ┌──────▼──────────────┐
                   │ PRICE SYNC & AUDIT  │
                   │ (Real-time Updates) │
                   └────────────────────┘
```

### Channel Configurations:

#### D365 Commerce (B2B/B2C)
- Catalog-based pricing
- List prices and tiered pricing
- Customer-specific agreements
- Integration with CRM

#### Retail Store/POS
- Store-specific markdown rules
- Real-time promotional pricing
- Inventory-driven pricing
- Local market adjustments

#### E-Commerce Portal
- Dynamic pricing algorithms
- A/B testing capabilities
- Geographic-based pricing
- Personalization rules

#### Price Sync & Audit
- Real-time price updates across channels
- Price consistency monitoring
- Audit trails and change history
- Exception handling

---

## 6. STEP-BY-STEP SETUP PROCESS

```
┌──────────────────────────────────────────────────────────────────────────┐
│           UNIFIED PRICING MANAGEMENT - IMPLEMENTATION STEPS             │
└──────────────────────────────────────────────────────────────────────────┘

PHASE 1: FOUNDATION
├─ Step 1: Define Pricing Strategy & Rules
│  │   └─ Identify pricing tiers, discounts, taxes
│  │
├─ Step 2: Create Price Lists
│  │   └─ Set base prices for products
│  │
└─ Step 3: Configure Price Dimensions
      └─ Customer, Channel, Geography, Time

PHASE 2: CONFIGURATION
├─ Step 4: Set Up Customer Pricing Groups
│  │   └─ Assign customers to price tiers
│  │
├─ Step 5: Configure Discounts & Trade Deals
│  │   └─ Volume discounts, bundling rules
│  │
└─ Step 6: Define Channel Pricing
      └─ Store-specific or online pricing

PHASE 3: RULES ENGINE
├─ Step 7: Create Pricing Rules & Formulas
│  │   └─ Rules for automatic price calculation
│  │
├─ Step 8: Set Up Validation Rules
│  │   └─ Min/Max price constraints
│  │
└─ Step 9: Configure Tax & Fee Rules
      └─ Jurisdictional tax calculations

PHASE 4: TESTING & DEPLOYMENT
├─ Step 10: Test Pricing Calculations
│  │   └─ Verify on sample orders
│  │
├─ Step 11: Enable Pricing Engine
│  │   └─ Activate unified pricing
│  │
└─ Step 12: Monitor & Audit
      └─ Track pricing activities
```

### Implementation Phases:

#### Phase 1: Foundation
Establish the core pricing strategy and data foundation:
1. **Define Pricing Strategy**: Determine business rules and pricing models
2. **Create Price Lists**: Set up product master prices
3. **Configure Dimensions**: Define pricing variation parameters

#### Phase 2: Configuration
Set up the pricing hierarchy and channel rules:
4. **Customer Pricing Groups**: Create tiers and customer classifications
5. **Discounts & Trade Deals**: Configure promotional and volume discounts
6. **Channel-Specific Rules**: Customize pricing per sales channel

#### Phase 3: Rules Engine
Build the automated calculation logic:
7. **Pricing Formulas**: Create rule-based price calculations
8. **Validation Rules**: Set price constraints and limits
9. **Tax Rules**: Configure tax calculations by jurisdiction

#### Phase 4: Testing & Deployment
Validate and activate the system:
10. **Comprehensive Testing**: Verify pricing accuracy
11. **Go-Live**: Enable unified pricing in production
12. **Monitoring**: Track performance and audit changes

---

## 7. PRICING CALCULATION EXAMPLE

```
┌──────────────────────────────────────────────────────────────────────────┐
│                    PRICING CALCULATION FLOW EXAMPLE                     │
└──────────────────────────────────────────────────────────────────────────┘

SCENARIO: Customer orders 150 units of Product X

INPUT:
┌──────────────────────────────┐
│ Product: Widget X            │
│ Customer: Acme Corp          │
│ Quantity: 150 units          │
│ Channel: D365 Commerce       │
│ Date: 2026-04-22             │
└──────────────────────────────┘
         │
         ▼
    STEP 1: LOOKUP BASE PRICE
    ┌──────────────────────────────┐
    │ Product X Base Price: $100    │
    │ Unit Price = $100             │
    └──────────────────────────────┘
         │
         ▼
    STEP 2: APPLY CUSTOMER PRICING
    ┌──────────────────────────────┐
    │ Customer: Acme Corp (Tier 2) │
    │ Customer Discount: -5%        │
    │ Price = $100 × 0.95 = $95     │
    └──────────────────────────────┘
         │
         ▼
    STEP 3: APPLY VOLUME DISCOUNT
    ┌──────────────────────────────┐
    │ Quantity: 150 units          │
    │ Qty Bracket: 100-199 units   │
    │ Volume Discount: -8%         │
    │ Price = $95 × 0.92 = $87.40  │
    └──────────────────────────────┘
         │
         ▼
    STEP 4: APPLY PROMOTIONAL RULES
    ┌──────────────────────────────┐
    │ Active Promo: Spring Sale    │
    │ Additional Discount: -3%      │
    │ Price = $87.40 × 0.97=$84.78 │
    └──────────────────────────────┘
         │
         ▼
    STEP 5: CALCULATE TAXES
    ┌──────────────────────────────┐
    │ Subtotal: $84.78 × 150 units │
    │ = $12,717.00                 │
    │ Tax Rate: 8% (jurisdiction)  │
    │ Tax = $12,717 × 0.08 =$1,017 │
    └──────────────────────────────┘
         │
         ▼
OUTPUT:
┌──────────────────────────────┐
│ Unit Price: $84.78           │
│ Line Total: $12,717.00       │
│ Tax Amount: $1,017.36        │
│ Final Total: $13,734.36      │
└──────────────────────────────┘
```

### Calculation Breakdown:

| Step | Operation | Price | Calculation |
|------|-----------|-------|-------------|
| 1 | Base Price | $100.00 | List price |
| 2 | Customer Tier (Tier 2) | $95.00 | $100 × 0.95 (-5%) |
| 3 | Volume Discount (100-199) | $87.40 | $95 × 0.92 (-8%) |
| 4 | Promotional Discount | $84.78 | $87.40 × 0.97 (-3%) |
| **5** | **Subtotal (150 units)** | **$12,717.00** | **$84.78 × 150** |
| 6 | Tax (8%) | $1,017.36 | $12,717 × 0.08 |
| **FINAL** | **Order Total** | **$13,734.36** | **$12,717 + $1,017.36** |

---

## 8. KEY CONFIGURATION PARAMETERS

```
┌──────────────────────────────────────────────────────────────────────────┐
│                    KEY PRICING CONFIGURATION FIELDS                      │
└──────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────┐
│  PRICE LIST SETUP       │
├─────────────────────────┤
│ • Price List ID         │
│ • Effective Date        │
│ • Expiration Date       │
│ • Currency              │
│ • Include Tax (Y/N)     │
│ • Rounding Rule         │
└─────────────────────────┘

┌─────────────────────────┐
│  DISCOUNT RULES         │
├─────────────────────────┤
│ • Discount Type         │
│   - Percentage          │
│   - Fixed Amount        │
│ • Min Quantity          │
│ • Max Quantity          │
│ • Customer Group        │
│ • Product Category      │
│ • Start/End Date        │
│ • Priority (if overlap) │
└─────────────────────────┘

┌─────────────────────────┐
│  CHANNEL SETTINGS       │
├─────────────────────────┤
│ • Channel ID            │
│ • Catalog Pricing       │
│ • Store-Specific Rules  │
│ • Pricing Updates       │
│ • Sync Frequency        │
│ • Markdown Rules        │
└─────────────────────────┘
```

### Parameter Details:

#### Price List Setup
- **Price List ID**: Unique identifier for the pricing structure
- **Effective/Expiration Dates**: Define pricing validity period
- **Currency**: Multi-currency support
- **Tax Inclusion**: Option to include or exclude tax in base price
- **Rounding Rules**: Define price precision and rounding

#### Discount Rules
- **Discount Type**: Percentage-based or fixed amount
- **Quantity Ranges**: Min/max thresholds for quantity discounts
- **Customer Segments**: Apply discounts to specific groups
- **Product Categories**: Category-specific discount rules
- **Priority**: Resolution order for overlapping discounts

#### Channel Settings
- **Channel-Specific Configuration**: Unique rules per channel
- **Catalog vs. Store Pricing**: Different strategies for different channels
- **Sync Frequency**: How often prices update across channels
- **Markdown Rules**: Promotional and clearance pricing

---

## 9. SYSTEM INTEGRATION MAP

```
┌──────────────────────────────────────────────────────────────────────────┐
│            UNIFIED PRICING - SYSTEM INTEGRATION LANDSCAPE               │
└──────────────────────────────────────────────────────────────────────────┘

EXTERNAL SYSTEMS          PRICING ENGINE          D365 MODULES
                                │
┌──────────────────┐           │           ┌──────────────────┐
│ Catalog / CMS    │───────────┼───────────│ Sales Orders     │
│ (Product Data)   │           │           │ Management       │
└──────────────────┘           │           └──────────────────┘
                               │
┌──────────────────┐           │           ┌──────────────────┐
│ CRM (Customer    │───────────┼───────────│ Invoicing &      │
│ Data)            │           │           │ Billing          │
└──────────────────┘           │           └──────────────────┘
                               │
┌──────────────────┐      ┌────▼────┐     ┌──────────────────┐
│ Tax Authority    │──────│ UNIFIED  │─────│ Reporting &      │
│ Services         │      │ PRICING  │     │ Analytics        │
└──────────────────┘      │ ENGINE   │     └──────────────────┘
                          └────┬────┘
┌──────────────────┐           │           ┌──────────────────┐
│ Promotion Mgmt   │───────────┼───────────│ E-Commerce /     │
│ (Rules)          │           │           │ POS Systems      │
└──────────────────┘           │           └──────────────────┘
                               │
┌──────────────────┐           │           ┌──────────────────┐
│ Inventory System │───────────┼───────────│ Financial Ledger │
│ (Availability)   │           │           │ (GL Accounts)    │
└──────────────────┘           │           └──────────────────┘
```

### Integration Points:

#### External Systems
- **Catalog/CMS**: Product information and attributes
- **CRM**: Customer data and relationships
- **Tax Authority**: Tax calculation services
- **Promotion Management**: Promotional rule engines
- **Inventory**: Product availability and stock levels

#### D365 Modules
- **Sales Orders Management**: Price application in orders
- **Invoicing & Billing**: Price-based invoice generation
- **Reporting & Analytics**: Pricing performance metrics
- **E-Commerce/POS**: Real-time pricing updates
- **Financial Ledger**: Price-to-revenue reconciliation

---

## 10. BEST PRACTICES & CONSIDERATIONS

```
┌──────────────────────────────────────────────────────────────────────────┐
│              BEST PRACTICES FOR PRICING SETUP                            │
└──────────────────────────────────────────────────────────────────────────┘

✓ DESIGN
  • Start simple, scale gradually
  • Document all pricing rules
  • Establish clear priority when rules overlap
  • Version control pricing changes

✓ PERFORMANCE
  • Index frequently queried fields
  • Cache pricing calculations
  • Regular database cleanup
  • Monitor calculation response times

✓ COMPLIANCE
  • Audit all pricing changes
  • Maintain pricing history
  • Document business rules
  • Regular compliance reviews

✓ TESTING
  • Test with real product/customer data
  • Test edge cases & boundary conditions
  • Validate tax calculations
  • A/B test pricing changes

✓ MONITORING
  • Track pricing calculation errors
  • Monitor pricing rule effectiveness
  • Alert on price anomalies
  • Regular reconciliation with GL
```

### Best Practice Details:

#### Design Principles
- **Simplicity First**: Build foundational rules before complex logic
- **Documentation**: Maintain comprehensive rule documentation
- **Priority Management**: Clear rule precedence prevents conflicts
- **Version Control**: Track all pricing changes for compliance

#### Performance Optimization
- **Database Indexing**: Optimize frequently accessed pricing fields
- **Calculation Caching**: Cache results for common pricing scenarios
- **Data Maintenance**: Regular cleanup of obsolete pricing data
- **Response Monitoring**: Track calculation performance metrics

#### Compliance & Governance
- **Change Auditing**: Log all pricing modifications
- **History Maintenance**: Keep historical pricing records
- **Rule Documentation**: Document business logic and rationale
- **Compliance Reviews**: Regular validation against regulations

#### Testing Strategy
- **Real Data Testing**: Use production-like datasets
- **Edge Case Coverage**: Test boundary conditions and exceptions
- **Tax Validation**: Verify tax calculations across jurisdictions
- **A/B Testing**: Validate pricing strategy effectiveness

#### Monitoring & Maintenance
- **Error Tracking**: Monitor calculation failures
- **Rule Effectiveness**: Analyze discount and promotion impact
- **Anomaly Detection**: Alert on unusual pricing patterns
- **GL Reconciliation**: Verify pricing-to-revenue consistency

---

## 11. REAL-WORLD USE CASES (20 SCENARIOS)

### Use Case 1: Multi-Tier B2B Volume Pricing

**Scenario**: Manufacturing company selling industrial components

**Configuration**:
```
Product: Industrial Motor X-500
Base List Price: $5,000

Customer Tier 1 (Distributors)    | Quantity Brackets
├─ 1-49 units: -10%               ├─ 1-9: List Price
├─ 50-99 units: -15%              ├─ 10-49: -5%
└─ 100+ units: -20%               └─ 50+: -12%

Customer Tier 2 (Retailers)       | Quantity Brackets
├─ 1-19 units: -5%                ├─ 1-4: List Price
├─ 20-49 units: -8%               └─ 5+: -8%
└─ 50+ units: -12%
```

**Result**: A distributor ordering 150 units gets: $5,000 × 0.80 = $4,000/unit

---

### Use Case 2: Dynamic E-Commerce Pricing

**Scenario**: Online retailer adjusting prices based on demand

**Configuration**:
```
Product: Smart Watch Pro
Base Price: $299

Demand Level          Adjustment    Inventory Level      Adjustment
├─ Low (<100/day)     -10%          ├─ High (>500 units) -5%
├─ Medium (100-500)   Base Price    ├─ Medium (100-500)  Base Price
└─ High (>500/day)    +15%          └─ Low (<100)        +10%

Flash Sale Rules:
├─ Monday 9 AM - 2 PM: -25%
├─ Thursday Evening: -20%
└─ Sunday Afternoon: -15%
```

**Result**: Product priced at $299, but dynamically adjusted based on real-time demand and inventory

---

### Use Case 3: Geographic Price Variation

**Scenario**: Global SaaS company with region-specific pricing

**Configuration**:
```
Software License - Enterprise Plan
Base Price (USD): $100,000/year

North America         │ Europe          │ Asia-Pacific    │ Emerging Markets
├─ USA: Base          │ ├─ EUR: +8%     │ ├─ AUS: -5%     │ ├─ India: -40%
├─ Canada: +2%        │ ├─ UK: +5%      │ ├─ JP: Base      │ ├─ Brazil: -25%
└─ Mexico: -15%       │ └─ DE: Base     │ └─ CN: -30%      │ └─ Vietnam: -35%
```

**Result**: Same product costs different amounts based on geographic region

---

### Use Case 4: Customer Loyalty Pricing

**Scenario**: Retail chain rewarding long-term customers

**Configuration**:
```
All Products (Automatic Loyalty Tier Detection)

Loyalty Status       Annual Spend    Discount Applied
├─ Bronze (1-2yrs)  <$5,000         3% discount
├─ Silver (2-5yrs)  $5,000-$25,000  7% discount
├─ Gold (5-10yrs)   $25,000-$100k   12% discount
└─ Platinum (10+)   $100,000+       18% discount

Birthday Month Bonus:
└─ +5% additional discount during birthday month
```

**Result**: Long-time customer automatically receives appropriate loyalty pricing

---

### Use Case 5: Bundle/Mix & Match Pricing

**Scenario**: Technology company selling bundled software solutions

**Configuration**:
```
Bundle: Complete Office Suite

Individual Pricing:
├─ Word Premium: $129
├─ Excel Advanced: $139
├─ PowerPoint Pro: $119
└─ Teams Enterprise: $149
Total if Separate: $536

Bundle Pricing Rules:
├─ Buy 2 products: 10% off bundle
├─ Buy 3 products: 20% off bundle
├─ Buy all 4 products: 30% off bundle

Applied Price: $536 × 0.70 = $375.20
```

**Result**: Customers incentivized to purchase complete bundle

---

### Use Case 6: Seasonal/Holiday Pricing

**Scenario**: Retail apparel company with seasonal inventory management

**Configuration**:
```
Winter Coat - Model 2024

Normal Season (Jan-Sep):
└─ List Price: $199

Pre-Season (Oct-Nov):
├─ Early Birds: Base + 5%
└─ Regular: Base Price

Peak Season (Dec):
├─ Black Friday Week: -40%
├─ Cyber Monday: -45%
├─ Christmas Week: -30%
└─ Regular December: Base + 10%

Clearance (Jan-Feb):
├─ January Sale: -60%
└─ Final Clearance: -70%
```

**Result**: Optimized pricing throughout the product lifecycle

---

### Use Case 7: Contract/Negotiated Pricing

**Scenario**: Enterprise software vendor with multi-year contracts

**Configuration**:
```
Customer: Acme Corp
Contract Period: 3 years (2024-2026)

Year 1 (2024): $500,000 (discounted from $600k)
Year 2 (2025): $525,000 (2% escalation)
Year 3 (2026): $551,250 (2.5% escalation)

Fixed Pricing Rule:
├─ Contract ID: CNT-2024-001
├─ Valid From: 2024-01-01
├─ Valid Until: 2026-12-31
├─ Auto-escalation: Yes (2% base, 2.5% year 3)
└─ All orders for this customer: Fixed contract rate
```

**Result**: Fixed negotiated pricing protects both parties

---

### Use Case 8: Cost-Plus Pricing with Margin Control

**Scenario**: Manufacturing company using cost-plus formulas

**Configuration**:
```
Product: Custom Assembly Unit

Cost Components:
├─ Material Cost: $150
├─ Labor Cost: $80
├─ Overhead: $40
├─ Contingency (10%): $27
└─ Total Cost: $297

Pricing Formula:
├─ Minimum Margin: 25%
├─ Target Margin: 35%
├─ Maximum Discount: 20% from target

Calculated Prices:
├─ Minimum (25% margin): $297 × 1.25 = $371.25
├─ Target (35% margin): $297 × 1.35 = $400.75
└─ Maximum Discount (20% off): $400.75 × 0.80 = $320.60
```

**Result**: Pricing always maintains minimum margin requirements

---

### Use Case 9: Subscription/Recurring Pricing

**Scenario**: Cloud service provider with monthly subscriptions

**Configuration**:
```
SaaS Platform - Subscription Tiers

Basic Plan:
├─ Monthly: $99
├─ Annual (prepay): $990 (-17% discount)
└─ 2-Year: $1,870 (-21% discount)

Professional Plan:
├─ Monthly: $299
├─ Annual (prepay): $2,990 (-0% discount)
└─ 2-Year: $5,680 (-5% discount)

Enterprise:
├─ Custom Pricing
├─ Volume discounts for seats
└─ Negotiated rates

Automatic Rules:
├─ Annual prepay: 2 months free
├─ 2-Year commit: 3 months free
└─ Add-on services: 10% discount if committed
```

**Result**: Encourages longer-term commitments with greater discounts

---

### Use Case 10: Promotional/Flash Sale Pricing

**Scenario**: E-commerce retailer running time-limited promotions

**Configuration**:
```
Flash Sale: "48-Hour Tech Sale"
Schedule: Friday 6 PM - Sunday 11:59 PM

Electronics Category:
├─ Laptops: -25% (Max discount: $500)
├─ Tablets: -20% (Max discount: $100)
├─ Accessories: -30% (Max discount: $50)
└─ Bundled Items: Additional -10%

Business Rules:
├─ Cannot go below cost
├─ Cannot combine with other promotions
├─ Limit 5 items per customer
├─ Notification: Email 24 hours before
└─ Auto-revert pricing after window closes
```

**Result**: Limited-time offers drive urgency and sales volume

---

### Use Case 11: Geographic Channel Pricing

**Scenario**: Automotive parts distributor with different channel strategies

**Configuration**:
```
Auto Part: Premium Oil Filter

Direct to Consumer (Web):
├─ List Price: $24.99
├─ Qty 1-5: List Price
├─ Qty 6-10: -10%
└─ Qty 11+: -15%

Authorized Retailers:
├─ Wholesale Cost: $14.99 (40% off retail)
├─ Suggested Retail: $24.99
├─ Minimum Markup: 30%

Commercial Fleet Accounts:
├─ Volume: 500+ units/month
├─ Price: $12.99 (-48% from retail)
├─ Auto-replenishment: Additional 2% discount

Warehouse Distribution:
├─ Bulk Orders (1000+ units): $11.99 (-52%)
└─ Pallet Orders (5000+ units): $11.49 (-54%)
```

**Result**: Each channel gets appropriate margin structure

---

### Use Case 12: Trade Deal Pricing (EDI)

**Scenario**: Consumer goods manufacturer managing trade deals

**Configuration**:
```
Product: Snack Bar (Case of 24)

Base Trade Price to Retailer: $14.00/case

Active Trade Deals (Q2 2024):

Deal 1: "Spring Promotion"
├─ Period: April 1-30
├─ Discount: $1.50/case (-10.7%)
├─ Minimum Order: 10 cases
└─ Trade Price: $12.50/case

Deal 2: "Quarterly Bonus"
├─ Period: Entire Q2
├─ Volume Threshold: 100 cases/week
├─ Discount: $2.00/case (-14.3%)
└─ Trade Price: $12.00/case

Deal 3: "New Customer Incentive"
├─ Period: First 90 days
├─ Discount: $2.50/case (-17.9%)
└─ Trade Price: $11.50/case

Rule Priority:
└─ Customer gets best applicable deal
```

**Result**: Competitive trade pricing while managing margins

---

### Use Case 13: Referral Pricing

**Scenario**: SaaS company incentivizing customer referrals

**Configuration**:
```
Referral Program - Shared Discounts

Referrer Benefits:
├─ 1 Successful Referral: $100 credit
├─ 3 Referrals: 10% lifetime discount
├─ 5+ Referrals: 15% lifetime discount + free upgrade
└─ VIP (10+): 20% discount + dedicated support

Referred Customer Benefits:
├─ First Purchase: 20% discount
├─ Annual Subscription: 3 months free (when billed annually)
└─ Locked-in Price: Pricing never increases for 2 years

Both Parties Trigger:
└─ Automatic application (no promo code needed)

Example:
├─ Referrer has 5 referrals: Gets 15% discount
├─ New referred customer: Gets 20% + locked rates
└─ System automatically applies both discounts
```

**Result**: Viral growth through incentivized referrals

---

### Use Case 14: B2B Price Protection

**Scenario**: Distributor protecting customer profits during market volatility

**Configuration**:
```
Customer: Regional Distributor ABC
Product: Commodity Component

Protection Agreement:
├─ Period: Q2 2024 (90 days)
├─ Base Price: $50/unit
├─ Price Cap: $52/unit (max +4%)
├─ Price Floor: $48/unit (min -4%)

Market Conditions:
├─ If market price rises to $60:
│  └─ Customer still pays: $52 (capped)
│  └─ You absorb extra cost (margin compression)
│
└─ If market price drops to $40:
   └─ Customer still pays: $48 (floored)
   └─ You capture extra margin

Renegotiation:
└─ Automatic review if market moves >10% from base
```

**Result**: Protects customer relationships during volatile markets

---

### Use Case 15: Multi-Currency Pricing

**Scenario**: International E-commerce with real-time currency conversion

**Configuration**:
```
Base Product: Premium Laptop
Base Currency: USD
Base Price: $1,500

Currency Conversion Rules:
├─ EUR: $1,500 ÷ 1.08 = €1,389
├─ GBP: $1,500 ÷ 1.27 = £1,181
├─ JPY: $1,500 × 148.5 = ¥222,750
├─ AUD: $1,500 × 1.52 = A$2,280
└─ CAD: $1,500 × 1.36 = C$2,040

Local Markup/Markdown:
├─ EUR Markets: +3% (VAT considerations)
├─ UK Market: +2% (import duties)
├─ Japan: -5% (market competition)
└─ Australia: +8% (remote market premium)

Final Prices:
├─ EUR: €1,431 (1,389 × 1.03)
├─ GBP: £1,205 (1,181 × 1.02)
├─ JPY: ¥211,612 (222,750 × 0.95)
└─ AUD: A$2,462 (2,280 × 1.08)
```

**Result**: Localized pricing reflecting local market conditions

---

### Use Case 16: Inventory-Driven Pricing

**Scenario**: Retail managing excess inventory through dynamic pricing

**Configuration**:
```
Product: Winter Coat
SKU: WC-2024-001

Base Price: $199

Inventory Management Triggers:

Stock Level: >500 units
├─ Status: Healthy inventory
├─ Adjustment: Base Price
└─ Days until restock: 90+

Stock Level: 200-500 units
├─ Status: Normal
├─ Adjustment: Base Price
└─ Action: Standard replenishment

Stock Level: 50-199 units
├─ Status: Low inventory
├─ Adjustment: Base Price (+5%)
├─ Reason: Premium scarcity pricing
└─ Message: "Only X left in stock!"

Stock Level: <50 units
├─ Status: Critical/Clearance
├─ Adjustment: -40% (Dynamic discount)
├─ Goal: Quick clearance before next season
└─ Message: "Final sale - 60% off!"

Seasonal End Date: Jan 31
├─ <30 days remaining: -50%
├─ <14 days remaining: -60%
└─ <7 days remaining: -70%
```

**Result**: Automatically optimizes pricing to maintain inventory balance

---

### Use Case 17: Competitive Price Matching

**Scenario**: Retailer matching competitor pricing automatically

**Configuration**:
```
Product: 4K Television Model X-850

Retail Price List: $799

Competitor Pricing Feed (Daily Update):
├─ Competitor A: $749
├─ Competitor B: $779
├─ Competitor C: $789
└─ Competitor D: $769

Price Matching Rules:
├─ Match lowest competitor: Enabled
├─ Match +/-: -$10 (beat competitor by $10)
├─ Minimum Price Floor: $600 (cost + 10%)
├─ Update Frequency: Real-time
└─ Notification: Price adjustment logged

Calculated Price:
├─ Lowest competitor price: $749
├─ Our match price: $749 - $10 = $739
├─ Above minimum floor: Yes ✓
└─ Final Price: $739 (automatically updated)

Business Rules:
├─ Don't advertise "lowest price guarantee"
├─ Only match 3 major competitors
├─ Exclude loss-leader products
└─ Manual override capability
```

**Result**: Competitive pricing without constant manual adjustments

---

### Use Case 18: Customer Segment Pricing

**Scenario**: B2B company with distinct customer segments

**Configuration**:
```
Product: Enterprise Software License
List Price: $250,000/year

Segment 1: Startup/SMB
├─ Revenue Profile: <$50M
├─ Discount: 40% off list
├─ Price: $150,000/year
├─ Support: Standard (8x5)
└─ Onboarding: Self-service

Segment 2: Mid-Market
├─ Revenue Profile: $50M-$500M
├─ Discount: 25% off list
├─ Price: $187,500/year
├─ Support: Premium (24x7)
└─ Onboarding: Guided (2 weeks)

Segment 3: Enterprise
├─ Revenue Profile: $500M+
├─ Discount: 15% off list
├─ Price: $212,500/year
├─ Support: Premium (24x7) + Named Account Manager
├─ Onboarding: Full deployment (6 weeks)
└─ Custom features: Available

Segment 4: Non-Profit/Government
├─ Discount: 60% off list
├─ Price: $100,000/year
├─ Support: Standard
└─ Custom deployment: Available
```

**Result**: Pricing optimized for each customer segment's value perception

---

### Use Case 19: Usage-Based Pricing

**Scenario**: Cloud platform charging based on consumption

**Configuration**:
```
Platform: Cloud Analytics Service

Base Pricing:
├─ Monthly Subscription: $500 (includes 1M API calls)

Usage Tiers (per month):
├─ 1-1M API calls: Included in subscription
├─ 1M-10M calls: +$0.50 per 1000 calls
├─ 10M-50M calls: +$0.35 per 1000 calls
├─ 50M+ calls: +$0.20 per 1000 calls

Data Storage:
├─ First 100GB: Included
├─ 100GB-1TB: +$0.10/GB
├─ 1TB-10TB: +$0.05/GB
└─ 10TB+: +$0.02/GB

Overage Protection:
├─ Hard cap available: Yes (stop processing at limit)
├─ Alert at 80% threshold: Yes
├─ Grace period: 10% overage allowed per month
└─ Auto-billing: At end of month

Example Month:
├─ Base: $500
├─ API calls: 25M (exceeds by 24M)
│  └─ Cost: (10M × $0.50) + (14M × $0.35) = $9,900
├─ Storage: 500GB
│  └─ Cost: 100GB free + (400GB × $0.05) = $20
└─ Total: $10,420
```

**Result**: Fair pricing that scales with customer usage

---

### Use Case 20: Channel Partner Margin Management

**Scenario**: Manufacturer managing margins across channel partners

**Configuration**:
```
Product: Industrial Equipment
Manufacturer Cost: $8,000

Pricing Tier Structure:

Direct Sales (Manufacturer):
├─ List Price: $14,000
├─ Margin: 75% ($6,000)
└─ Channel: Direct to End-User

Authorized Distributor:
├─ Wholesale Price: $10,500
├─ Margin: 31% ($3,500)
├─ Min Retail Suggestion: $14,000
└─ Max Discount: 10% ($1,400)

Certified Reseller:
├─ Wholesale Price: $11,200
├─ Margin: 40% ($3,200)
├─ Min Retail Suggestion: $14,000
└─ Max Discount: 5% ($700)

Systems Integrator:
├─ Wholesale Price: $9,800
├─ Margin: 22.5% ($1,800)
├─ Service bundle bundled pricing
└─ Custom quotes available

Margin Protection Rules:
├─ Distributor cannot sell below $10,500
├─ Reseller cannot sell below $11,200
├─ Integrator custom quotes approved by mgmt
├─ Price monitoring: Monthly
└─ Violations trigger: Channel rep contact

Volume Incentives (Annual):
├─ $500K-$1M revenue: 1% rebate
├─ $1M-$2M revenue: 2% rebate
├─ $2M+ revenue: 3% rebate
└─ Auto-calculated quarterly
```

**Result**: Healthy partner margins while protecting brand pricing

---

### Summary of Use Cases

| # | Use Case | Type | Primary Benefit |
|----|----------|------|-----------------|
| 1 | Multi-Tier B2B Volume | B2B | Incentivizes bulk orders |
| 2 | Dynamic E-Commerce | Retail | Maximizes revenue/margin |
| 3 | Geographic Variation | Global | Localizes pricing strategy |
| 4 | Loyalty Pricing | CRM | Rewards customer retention |
| 5 | Bundle/Mix & Match | Retail | Increases basket size |
| 6 | Seasonal Pricing | Retail | Optimizes inventory |
| 7 | Contract Pricing | B2B | Secures long-term revenue |
| 8 | Cost-Plus Formulas | Manufacturing | Ensures margin floors |
| 9 | Subscription Tiers | SaaS | Encourages commitment |
| 10 | Flash Sales | Retail | Drives urgency/traffic |
| 11 | Channel Pricing | Multi-Channel | Channel-appropriate margins |
| 12 | Trade Deals | B2B | Maintains trade relationships |
| 13 | Referral Pricing | Growth | Incentivizes advocacy |
| 14 | Price Protection | B2B | Protects partnerships |
| 15 | Multi-Currency | Global | Supports international sales |
| 16 | Inventory-Driven | Retail | Balances stock levels |
| 17 | Competitive Matching | Retail | Maintains competitiveness |
| 18 | Segment Pricing | B2B/SaaS | Optimizes value capture |
| 19 | Usage-Based | SaaS | Fair consumption pricing |
| 20 | Partner Margins | Distribution | Protects channel health |

---

## Summary

Unified Pricing Management in Dynamics 365 provides a centralized, flexible platform for managing complex pricing across multiple channels and customer segments. By following these setup guidelines and best practices, organizations can:

- **Standardize pricing** across all sales channels
- **Reduce manual pricing errors** through automated rules
- **Improve profitability** through strategic pricing
- **Enhance customer relationships** with transparent pricing
- **Maintain compliance** with audit trails and history

The system's multi-channel architecture ensures consistency while allowing channel-specific customization, making it ideal for organizations with complex, distributed pricing needs.

The 20 use cases provided demonstrate the system's versatility in handling diverse business scenarios, from simple B2B volume discounts to complex subscription-based pricing and real-time inventory management.

---

**Document Version**: 2.0  
**Last Updated**: April 23, 2026  
**Status**: Complete with 20 Real-World Use Cases
