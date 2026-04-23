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

## Summary

Unified Pricing Management in Dynamics 365 provides a centralized, flexible platform for managing complex pricing across multiple channels and customer segments. By following these setup guidelines and best practices, organizations can:

- **Standardize pricing** across all sales channels
- **Reduce manual pricing errors** through automated rules
- **Improve profitability** through strategic pricing
- **Enhance customer relationships** with transparent pricing
- **Maintain compliance** with audit trails and history

The system's multi-channel architecture ensures consistency while allowing channel-specific customization, making it ideal for organizations with complex, distributed pricing needs.

---

**Document Version**: 1.0  
**Last Updated**: April 22, 2026  
**Status**: Complete
