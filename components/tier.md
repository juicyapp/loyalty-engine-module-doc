# Tier
One of the **3 Basics Components** of Loyalty Engine. Tier is used to classify account holder. Tier can be earn by mission's reward, or even by redeem (but not practical).

## What Tier can do
 - **Special Redemption** - Redeem a reward which only available for specific Tier
 - **Integration** - Integrate with other system, such as POS, to provide special discount or service
 - **Mission Condition** - As a condition in formula builder for Mission (Can check in mission topic)
 - **Checkpoint Reward** - Some mission checkpoint can only be earned by specific Tier

## Determination of Current Tier
A account only have one Tier at a time. The current Tier will be determined in the following steps:
 1. Not expired
 2. Status is `active`
 3. Highest rank

If there are no Tier records match the above conditions, the account will be considered as **Base Tier**.

## Model and Fields

### Tier Scheme
|Field|Explanations|
|---|---|
|id|Primary Key|
|code|Unique Identifier|
|expiry_setting|When will the tier expire from **earning time**|
|rank|Start from 0, higher means higher Tier, which mostly used for sorting or display, and some guard calculations|
|initial|Will be **base tier** when it is true, any new account without any tier will automatic became base tier member. **One Loyalty Program only have One base tier**|

### Tier
|Field|Explanations|
|---|---|
|id|Primary Key|
|account_id|Reference to the account holder|
|source|`direct` - Issue to account directly <br />`reward` - Earned from `mission` or `reward` issue|
|status|`active`, `disable` - For controlling availability<br /> `replaced` - Similar with disable, exist tier will replaced when newer tier is assigned|
|issued_at|Time of point record added to account|
|expire_at|Will return to `initial tier` **after** it|