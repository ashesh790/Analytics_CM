# Analytics System - Complete Documentation

## 📊 Table of Contents

1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Data Sources](#data-sources)
4. [Analytics Levels](#analytics-levels)
5. [Metrics & Calculations](#metrics--calculations)
6. [Data Flow](#data-flow)
7. [API Endpoints](#api-endpoints)
8. [Frontend Integration](#frontend-integration)
9. [Data Processing Pipeline](#data-processing-pipeline)
10. [Key Components](#key-components)

---

## Overview

The Analytics System in Collection-Manager provides comprehensive insights into **Products**, **Collections**, and **Store Performance** by aggregating data from multiple sources including Shopify Orders, Google Analytics, and Product Information.

### Key Features

- ✅ **Multi-level Analytics**: Product, Variant, Collection, and Date-level analytics
- ✅ **Google Analytics Integration**: Track views, add-to-carts, checkouts, and conversion rates
- ✅ **Financial Metrics**: Revenue, profit, margin, COGS, discounts
- ✅ **Time-based Analysis**: Daily, weekly, monthly aggregations
- ✅ **Export Capabilities**: CSV/Excel export with formatted data
- ✅ **Real-time Dashboard**: Live analytics on dashboard and dedicated pages

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Frontend Layer                           │
├─────────────────────────────────────────────────────────────────┤
│  Dashboard Analytics  │  Collection Analytics  │  Product Analytics │
└────────────┬──────────┴──────────┬─────────────┴──────────┬───────┘
             │                     │                        │
             └─────────────────────┼────────────────────────┘
                                    │
                    ┌───────────────▼───────────────┐
                    │     Analytics Routes           │
                    │   (analytics/routes.py)        │
                    └───────────────┬───────────────┘
                                    │
                    ┌───────────────▼───────────────┐
                    │   Analytics Utils & Others    │
                    │  (analytics/utils.py)          │
                    │  (analytics/others.py)         │
                    └───────────────┬───────────────┘
                                    │
        ┌──────────────────────────┼──────────────────────────┐
        │                          │                          │
┌───────▼────────┐      ┌─────────▼─────────┐    ┌──────────▼──────────┐
│  Preprocessing │      │  Data Aggregation │    │  Metric Calculation │
│  (preprocessing.py)   │  (utils.py)       │    │  (constants.py)     │
└───────┬────────┘      └─────────┬─────────┘    └──────────┬──────────┘
        │                         │                         │
        └─────────────────────────┼─────────────────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │     Data Sources          │
                    ├───────────────────────────┤
                    │ • Orders (Shopify)        │
                    │ • Google Analytics        │
                    │ • Products                │
                    │ • Collections             │
                    │ • Refunds                 │
                    │ • Transactions            │
                    │ • Other Expenses/Revenue │
                    └───────────────────────────┘
```

---

## Data Sources

### 1. **Order Data** (`Order` Model)
- Product sales information
- Line items, variants, quantities
- Pricing, discounts, shipping, taxes
- Order dates and status

### 2. **Google Analytics** (`Ga` Model)
- Product views
- Add to cart events
- Checkout initiations
- Conversion tracking

### 3. **Product Data** (`Product` Model)
- Product information (title, SKU, vendor, tags)
- Pricing (price, compare_at_price, cost)
- Inventory levels
- Product metadata

### 4. **Collection Data** (`Collection` Model)
- Collection-product relationships
- Collection metadata

### 5. **Refund Data**
- Refund amounts
- Returned quantities
- COGS returned
- Shipping/tax refunds

### 6. **Transaction Data**
- Payment fees
- Currency conversion rates
- Transaction status

### 7. **Other Expenses/Revenues**
- One-time expenses/revenues
- Recurring expenses/revenues

---

## Analytics Levels

The system supports analytics at multiple granularity levels:

```
┌─────────────────────────────────────────────────────────────┐
│                    Analytics Levels                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│  │  Date Level   │    │ Order Level  │    │Product Level │ │
│  │               │    │              │    │              │ │
│  │ • Daily       │    │ • Per Order  │    │ • Product    │ │
│  │ • Weekly      │    │ • Aggregated │    │ • Variant    │ │
│  │ • Monthly     │    │   Metrics    │    │ • Collection │ │
│  └──────────────┘    └──────────────┘    └──────────────┘ │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Level Details

| Level | Primary Keys | Use Case |
|-------|-------------|----------|
| **Date** | `date` | Time-series analysis, trends |
| **Order** | `id`, `date` | Order-level metrics |
| **Product** | `id`, `date` | Product performance |
| **Variant** | `variant_id`, `date` | Variant-specific metrics |
| **Collection** | `collection_id`, `date` | Collection performance |

---

## Metrics & Calculations

### 📈 Sales Metrics

```
┌─────────────────────────────────────────────────────────────┐
│                    Sales Metrics Flow                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Gross Sales = Total Line Items Price + Other Revenues     │
│                                                              │
│  Net Sales = Gross Sales - Discounts - Refunds              │
│                                                              │
│  Total Sales = Net Sales + Shipping + Taxes                 │
│                                                              │
│  Revenue = Price × Products Sold                            │
│                                                              │
│  Net Revenue = Revenue - (Discount × Products Sold)          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 💰 Profit Metrics

```
┌─────────────────────────────────────────────────────────────┐
│                    Profit Calculation Flow                  │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Gross Profit = Gross Sales                                 │
│                - Discounts                                  │
│                - Refunds                                    │
│                + Shipping                                   │
│                + Taxes                                      │
│                - COGS                                        │
│                - Payment Fees                               │
│                                                              │
│  Net Profit = Gross Profit - Other Expenses                 │
│                                                              │
│  Gross Margin = (Gross Profit / Gross Sales) × 100          │
│                                                              │
│  Net Margin = (Net Profit / Gross Sales) × 100              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 🛒 Google Analytics Metrics

```
┌─────────────────────────────────────────────────────────────┐
│              Google Analytics Conversion Funnel             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│         Views                                                │
│          │                                                   │
│          ▼                                                   │
│    Add to Carts ────► Cart to View = (ATC / Views) × 100    │
│          │                                                   │
│          ▼                                                   │
│     Checkouts ────► Checkout to View = (Checkouts/Views)×100│
│          │                                                   │
│          │         Checkout to Cart = (Checkouts/ATC)×100   │
│          │                                                   │
│          ▼                                                   │
│    Products Sold                                             │
│          │                                                   │
│          ├───► Buy to View = (Sold / Views) × 100           │
│          ├───► Buy to Cart = (Sold / ATC) × 100             │
│          └───► Buy to Checkout = (Sold / Checkouts) × 100  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Complete Metrics List

#### Order-Based Metrics
- `products_sold` - Quantity of products sold
- `revenue` - Price × Products Sold
- `net_revenue` - Revenue after discounts
- `gross_sales` - Total line items price
- `net_sales` - Gross sales minus discounts and refunds
- `total_sales` - Net sales plus shipping and taxes
- `total_discounts` - Sum of all discounts
- `total_refunds` - Sum of all refunds
- `total_shipping` - Shipping costs
- `total_taxes` - Tax amounts
- `total_cogs` - Cost of goods sold
- `total_payment_fees` - Payment processing fees
- `avg_order_value` - Average order value

#### Product-Based Metrics
- `price` - Product price
- `compare_at_price` - Original/compare price
- `cost` - Product cost
- `profit` - Revenue - Cost
- `true_profit` - Profit considering discounts
- `margin` - (Profit / Revenue) × 100
- `true_margin` - (True Profit / Revenue) × 100
- `discount` - Discount amount
- `discount_percent` - Discount percentage
- `inventory` - Stock quantity

#### Google Analytics Metrics
- `views` - Product page views
- `add_to_carts` - Add to cart events
- `checkouts` - Checkout initiations
- `cart_to_view` - Conversion rate: (ATC / Views) × 100
- `checkout_to_view` - Conversion rate: (Checkouts / Views) × 100
- `checkout_to_cart` - Conversion rate: (Checkouts / ATC) × 100
- `buy_to_view` - Conversion rate: (Sold / Views) × 100
- `buy_to_cart` - Conversion rate: (Sold / ATC) × 100
- `buy_to_checkout` - Conversion rate: (Sold / Checkouts) × 100

---

## Data Flow

### Complete Analytics Data Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Analytics Data Flow                         │
└─────────────────────────────────────────────────────────────────┘

1. REQUEST RECEIVED
   │
   ├─► User selects date range, metrics, level
   │
   ▼
2. DATA READING PHASE
   │
   ├─► read_data_for_analytics()
   │   ├─► Identifies required data types from metrics
   │   ├─► Reads Order data
   │   ├─► Reads Google Analytics data
   │   ├─► Reads Product data
   │   ├─► Reads Refund data
   │   ├─► Reads Transaction data
   │   └─► Reads Other Expenses/Revenues
   │
   ▼
3. DATA PREPROCESSING PHASE
   │
   ├─► prep_analytics_data()
   │   ├─► Filter cancelled orders
   │   ├─► Select date range
   │   ├─► Join order with other_info
   │   ├─► Join order with transactions
   │   ├─► Join order with refunds
   │   ├─► Process refunds
   │   ├─► Convert payment fees with currency rates
   │   └─► Aggregate to primary keys
   │
   ▼
4. METRIC CALCULATION PHASE
   │
   ├─► get_basic_analytics()
   │   ├─► Calculate base metrics (gross_sales, net_sales, etc.)
   │   ├─► Apply formulas from ANALYTICS_INFO
   │   └─► Fill missing values
   │
   ├─► get_basic_google_analytics()
   │   ├─► Aggregate GA data by primary keys
   │   ├─► Calculate GA metrics
   │   └─► Handle ratio metrics separately
   │
   ▼
5. DATA MERGING PHASE (Product Level Only)
   │
   ├─► get_product_basic_analytics()
   │   ├─► Merge order analytics with product data
   │   ├─► Merge Google Analytics data
   │   │   ├─► Determine GA primary key (sku/variant_id/id)
   │   │   ├─► Match GA data to products
   │   │   └─► Distribute product-level GA to variants if needed
   │   └─► Aggregate to requested level (product/variant)
   │
   ▼
6. COLLECTION AGGREGATION (Collection Level Only)
   │
   ├─► update_collection_analytics()
   │   ├─► Read product analytics
   │   ├─► Read collection-product relationships
   │   ├─► Aggregate product metrics to collection level
   │   └─► Save collection_analytics.pkl
   │
   ▼
7. RATIO METRICS CALCULATION
   │
   ├─► add_ratio_metrics()
   │   ├─► Calculate conversion rates
   │   └─► Handle division by zero (inf → NaN)
   │
   ▼
8. DATA FORMATTING PHASE
   │
   ├─► prepare_analytics_data()
   │   ├─► Select top N items (if requested)
   │   ├─► Sort by specified metric
   │   ├─► Format for table or line chart
   │   └─► Convert to JSON or DataFrame
   │
   ▼
9. RESPONSE
   │
   └─► Return formatted analytics data
```

### Product Analytics Specific Flow

```
┌─────────────────────────────────────────────────────────────┐
│           Product Analytics Processing Flow                  │
└─────────────────────────────────────────────────────────────┘

Product Data (Base)
    │
    ├─► Filter by dimensions (id, variant, sku, title, etc.)
    │
    ▼
Order Analytics
    │
    ├─► Filter orders by date range
    ├─► Remove cancelled orders
    ├─► Join with transactions, refunds
    ├─► Aggregate to variant_id + date level
    ├─► Calculate: revenue, products_sold, discounts, etc.
    │
    ▼
Google Analytics (if enabled)
    │
    ├─► Determine primary key (sku/variant_id/id) by overlap %
    ├─► Aggregate GA data by primary key + date
    ├─► Match to products:
    │   ├─► If SKU match → Direct merge
    │   ├─► If Variant ID match → Direct merge
    │   └─► If Product ID match → Distribute evenly to variants
    │
    ▼
Merge Order + GA Data
    │
    ├─► Outer join on variant_id + date
    ├─► Fill missing values with 0.0
    │
    ▼
Product Information Merge
    │
    ├─► Merge with product dimensions
    ├─► Add default date for products without orders/GA
    │
    ▼
Aggregation to Requested Level
    │
    ├─► If level = "product": Aggregate variants to product
    ├─► If level = "variant": Keep variant level
    ├─► Apply aggregation functions (sum/mean)
    │
    ▼
Final Analytics DataFrame
```

---

## API Endpoints

### 1. Dashboard Analytics
```
POST /app-collection-manager-dashboard-analytics
```
**Purpose**: Get analytics data for dashboard display

**Request Parameters**:
- `type`: "table" or "line"
- `level`: "collection" | "product" | "variant"
- `start_date`: "YYYY-MM-DD"
- `end_date`: "YYYY-MM-DD"
- `sort_metric`: Metric to sort by
- `max_num`: Number of top items (-1 for all)

**Response**: JSON with analytics data

---

### 2. Collection Analytics Page
```
GET /app-collection-manager-collection-analytics
```
**Purpose**: Render collection analytics page

**Response**: HTML template

---

### 3. Collection Analytics Data
```
POST /app-collection-manager-collection-analytics-list-data
```
**Purpose**: Get collection analytics data for DataTable

**Request Parameters**:
- `start_date`: "YYYY-MM-DD"
- `end_date`: "YYYY-MM-DD"
- `start`: Pagination start
- `length`: Page length
- `search[value]`: Search term
- `order[0][column]`: Sort column
- `order[0][dir]`: Sort direction

**Response**: DataTable JSON format

---

### 4. Product Analytics Page
```
GET /app-collection-manager-product-analytics
```
**Purpose**: Render product analytics page

**Response**: HTML template

---

### 5. Product Analytics Data
```
POST /app-collection-manager-product-analytics-list-data
```
**Purpose**: Get product analytics data for DataTable

**Request Parameters**: Same as collection analytics + `level`: "product" | "variant"

**Response**: DataTable JSON format

---

### 6. Export Files
```
GET /app-collection-manager-export-files
```
**Purpose**: Export analytics data to CSV/Excel

**Query Parameters**:
- `entity`: "products" | "collection analytics"
- `file_type`: "csv" | "xlsx"
- `start_date`, `end_date`: Date range
- `col_visible_ddl_text`: Visible columns

**Response**: File download

---

## Frontend Integration

### DataTable Integration

The analytics system uses a custom DataTable implementation (`Pulsar_Lens_DataTable`) that:

1. **Fetches Data**: Makes AJAX calls to analytics endpoints
2. **Renders Table**: Displays analytics in sortable, filterable tables
3. **Supports Export**: Allows CSV/Excel export
4. **Column Visibility**: Users can show/hide columns
5. **Date Range Picker**: Select custom date ranges

### Frontend Flow

```
┌─────────────────────────────────────────────────────────────┐
│              Frontend Analytics Flow                        │
└─────────────────────────────────────────────────────────────┘

User Action (Select Date Range / Change Filters)
    │
    ▼
JavaScript: Update analytics_params
    │
    ▼
AJAX Request to Analytics Endpoint
    │
    ▼
Backend: Process & Return Analytics Data
    │
    ▼
Frontend: Update DataTable
    │
    ├─► Render table rows
    ├─► Update pagination
    ├─► Apply sorting
    └─► Update charts (if line chart mode)
```

### Key Frontend Files

- `templates/analytics/collection_analytics.html` - Collection analytics page
- `templates/analytics/product_analytics.html` - Product analytics page
- `templates/dashboard.html` - Dashboard with analytics widget

---

## Data Processing Pipeline

### Preprocessing Functions

```
┌─────────────────────────────────────────────────────────────┐
│              Preprocessing Functions                         │
└─────────────────────────────────────────────────────────────┘

1. select_data_in_date_range()
   └─► Filters data by start_date and end_date

2. remove_rows_invalid_keys()
   └─► Removes rows with invalid keys (e.g., id = -1)

3. aggregate_data_to_level()
   └─► Aggregates data to specified level with group_by functions

4. add_other_info_to_data()
   └─► Joins order/refund with other_info tables

5. add_transaction_to_data()
   └─► Joins order/refund with transaction data

6. convert_payment_fees_with_currency_rate()
   └─► Converts payment fees using currency exchange rates

7. prep_order_data()
   └─► Preprocesses order data, aggregates to order level

8. prep_refund_data()
   └─► Preprocesses refund data, calculates refund metrics

9. prep_one_time_data()
   └─► Processes one-time expenses/revenues

10. prep_recurring_data()
    └─► Expands recurring expenses/revenues into date series
```

### Time Interval Conversion

```
┌─────────────────────────────────────────────────────────────┐
│         Time Interval Resampling                             │
└─────────────────────────────────────────────────────────────┘

Daily Data
    │
    ├─► Weekly: Group by week (Monday start)
    ├─► Monthly: Group by month (1st of month)
    └─► Aggregate metrics (sum for most, mean for AOV)

Example:
    Daily: [2024-01-01: $100, 2024-01-02: $150, ...]
    Weekly: [Week 1: $700, Week 2: $800, ...]
    Monthly: [Jan 2024: $3000, Feb 2024: $3500, ...]
```

---

## Key Components

### 1. `analytics/constants.py`

**Purpose**: Defines all metrics, formulas, and constants

**Key Structures**:
- `ANALYTICS_INFO`: Dictionary mapping levels → metrics → definitions
  - `data_type`: Required data sources
  - `columns`: Column names needed
  - `metrics`: Dependent metrics
  - `formula`: Calculation formula
- `RATIO_METRICS`: List of conversion rate metrics
- `PRODUCT_COLUMNS`: Product-specific columns
- `FORMAT_PRODUCT_EXPORT_COLUMNS`: Export formatting rules
- `FORMAT_COLLECTION_EXPORT_COLUMNS`: Collection export formatting

### 2. `analytics/utils.py`

**Purpose**: Core analytics calculation functions

**Key Functions**:
- `read_data_for_analytics()` - Reads all required data sources
- `prep_analytics_data()` - Main preprocessing function
- `get_basic_analytics()` - Calculates order-based metrics
- `get_basic_google_analytics()` - Calculates GA metrics
- `get_product_basic_analytics()` - Product-level analytics
- `update_collection_analytics()` - Updates collection analytics cache
- `add_ratio_metrics()` - Calculates conversion rates

### 3. `analytics/preprocessing.py`

**Purpose**: Data preprocessing utilities

**Key Functions**:
- `aggregate_data_to_level()` - Aggregates to specified level
- `select_data_in_date_range()` - Date filtering
- `add_transaction_to_data()` - Transaction joining
- `add_other_info_to_data()` - Other info joining
- `convert_into_time_intervals()` - Time resampling

### 4. `analytics/others.py`

**Purpose**: High-level analytics preparation

**Key Functions**:
- `prepare_analytics_data()` - Main entry point for analytics preparation
- `get_analytics_data()` - Wrapper function for analytics retrieval

### 5. `analytics/routes.py`

**Purpose**: Flask routes for analytics endpoints

**Key Routes**:
- `get_dashboard_analytics_data()` - Dashboard endpoint
- `collection_analytics()` - Collection page
- `collection_analytics_list_data()` - Collection data
- `product_analytics()` - Product page
- `product_analytics_list_data()` - Product data
- `export_files()` - Export endpoint

---

## Data Storage

### Pickle Files

Analytics data is cached in pickle files:

- `collection_analytics.pkl` - Cached collection analytics
- `product_analytics.pkl` - Cached product analytics (if saved)
- `raw_analytics.pkl` - Raw analytics data for export

### Storage Location

```
instance/static/collection-creator/static/outputs/{user_id}/
├── collection_analytics.pkl
├── product_analytics.pkl
└── raw_analytics.pkl
```

---

## Google Analytics Integration

### Primary Key Matching

The system intelligently matches Google Analytics data to products:

```
┌─────────────────────────────────────────────────────────────┐
│         GA Primary Key Selection Logic                      │
└─────────────────────────────────────────────────────────────┘

1. Calculate Overlap Percentages:
   ├─► SKU overlap: (GA SKUs ∩ Product SKUs) / Total
   ├─► Variant ID overlap: (GA Variant IDs ∩ Product Variant IDs) / Total
   └─► Product ID overlap: (GA Product IDs ∩ Product IDs) / Total

2. Select Highest Overlap:
   ├─► If SKU overlap > others → Use SKU as primary key
   ├─► If Variant ID overlap > others → Use Variant ID
   └─► Otherwise → Use Product ID

3. Match Strategy:
   ├─► SKU/Variant ID match → Direct 1:1 mapping
   └─► Product ID match → Distribute GA metrics evenly across variants
```

### GA Data Distribution

When GA data is at product level but analytics needs variant level:

```
Product A (3 variants)
├─► GA Views: 300
├─► GA Add to Carts: 30
└─► Distribution:
    ├─► Variant 1: Views=100, ATC=10
    ├─► Variant 2: Views=100, ATC=10
    └─► Variant 3: Views=100, ATC=10
```

---

## Export Functionality

### Export Process

```
┌─────────────────────────────────────────────────────────────┐
│              Export Data Flow                                │
└─────────────────────────────────────────────────────────────┘

1. User clicks Export button
    │
    ▼
2. Frontend: Collect visible columns
    │
    ▼
3. Request: GET /app-collection-manager-export-files
    │
    ├─► entity: "products" | "collection analytics"
    ├─► file_type: "csv" | "xlsx"
    ├─► start_date, end_date
    └─► col_visible_ddl_text: Comma-separated column names
    │
    ▼
4. Backend: Get analytics data
    │
    ├─► Call collection_analytics_list_data() or
    │   product_analytics_list_data()
    │
    ▼
5. Format Data
    │
    ├─► Apply format_export_column_value() for each column:
    │   ├─► Date columns: Format with timezone
    │   ├─► Price columns: Add currency symbol
    │   ├─► Percent columns: Format as percentage
    │   └─► Number columns: Format numbers
    │
    ▼
6. Generate File
    │
    ├─► Create DataFrame with formatted columns
    ├─► Select only visible columns
    ├─► Export to CSV or Excel
    │
    ▼
7. Return File Download
```

### Export Formatting

- **Dates**: Converted to user's timezone, formatted as "YYYY-MM-DD HH:mm:ss"
- **Prices**: Prefixed with currency symbol (e.g., "$100.00")
- **Percentages**: Formatted as "X.XX" (e.g., "25.50")
- **Numbers**: Formatted with appropriate decimal places

---

## Performance Considerations

### Caching Strategy

1. **Collection Analytics**: Cached in `collection_analytics.pkl`
   - Updated incrementally (only new dates)
   - Max back days: 120 days
   - Aggregated at collection + date level

2. **Product Analytics**: Calculated on-demand
   - No caching by default (can be saved if `is_save_file=True`)
   - Supports large datasets with pagination

### Optimization Techniques

- **Incremental Updates**: Only process new dates for collection analytics
- **Lazy Loading**: Data loaded only when needed
- **Pagination**: Large datasets paginated in DataTable
- **Selective Loading**: Only required data types loaded based on metrics

---

## Error Handling

### Common Scenarios

1. **No Data Available**
   - Returns empty DataFrame
   - Frontend shows "No data" message

2. **Google Analytics Not Connected**
   - GA metrics set to "N/A"
   - Order metrics still calculated

3. **Invalid Date Range**
   - Validated on frontend (max 120 days back)
   - Backend validates and adjusts if needed

4. **Missing Product Data**
   - Products without orders/GA get default date
   - Metrics set to 0 or NaN

---

## Future Enhancements

Potential improvements:

- [ ] Real-time analytics updates
- [ ] Advanced filtering and segmentation
- [ ] Custom metric definitions
- [ ] Scheduled analytics reports
- [ ] Multi-store analytics comparison
- [ ] Predictive analytics
- [ ] Anomaly detection

---

## Summary

The Analytics System in Collection-Manager is a comprehensive solution that:

✅ Aggregates data from multiple sources (Orders, GA, Products)  
✅ Calculates 30+ metrics across multiple levels  
✅ Provides real-time and cached analytics  
✅ Supports flexible date ranges and time intervals  
✅ Integrates seamlessly with frontend DataTables  
✅ Enables data export with proper formatting  

The system is designed to be scalable, maintainable, and extensible for future requirements.

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Maintained By**: Collection-Manager Team

