# Item
One of the **3 Basics Components** of Loyalty Engine. item represents a physical reward, such as discount, voucher, gift, etc. Item can be redeemed by Point, earned by Mission, or issued by Reward.

## What Item can do
 - **Physical reward** 's redemption records (use operator portal)
 - **Integration** with POS / Ecommerce system for further usage

<!-- ```mermaid
---
title: Item - Coupon integration with Eats365 POS
---
sequenceDiagram
    participant L as Loyalty Engine
    participant P as Eats365 POS

``` -->

## Model and Fields

### Item Scheme
|Field|Explainations|
|---|---|
|id|Primary Key|
|code|Unique Identifier|
|expiry_setting|When will the item expire from **earning time**|
|activation_setting|When will the item activate from **earning time**|
|usage_count|How many times an item(record) with this scheme can be used (Only affects records after it set) **Null means infinity usages**|

### Item
|Field|Explainations|
|---|---|
|id|Primary Key|
|account_id|Reference to the account holder|
|token|Unique Code, can be use like a coupon code, for generate QR Code|
|usage_count|**Null means infinity usages**|
|source|`direct` - Issue to account directly <br />`reward` - Earned from `mission` or `reward` issue|
|status|`active`, `disable` For controlling availablilty|
|issued_at|Time of point record added to account|
|activate_at|Won't able to use **before** it|
|expire_at|Won't able to use **after** it|

### Item Usage
|Field|Explainations|
|---|---|
|id|Primary Key|
|account_id|Referring the account who use it|
|item_id|Referring the item record to use|
|used_at|When the usage created|