# Scenarios
Before any actual work, we might have to understand client's loyalty program scenarios(If there any), and try to translate it into Loyalty Engine components.

## Basic conversion
Some common loyalty terms to Loyalty Engine components
| Loyalty Term | Loyalty Engine Component Schemes |
| --- | --- |
| Member Points - 會員積分 / 電子印花 | PointScheme |
| Member Tiers - 會員等級 | TierScheme |
| E-Vouchurs - 電子優惠券 | ItemScheme |
| Redemption by points - 積分換領 | RewardScheme with RewardSchemeCost |
| Welcome / Birthday gift - 迎新 / 生日獎賞 | EventScheme -> MissionScheme -> RewardScheme  |
| Referral - 推薦計劃 | EventScheme -> MissionScheme -> RewardScheme  |

## Real world scenarios
Lets try to briefly translate some real world scenarios into Loyalty Engine components.

### Decathlon
- https://www.decathlon.com.hk/zh/member-benefits_lp-6NSFUJ
- `PointScheme` 分
- `RewardScheme` with `ItemScheme` and `RewardSchemeCost` for redemption
- Order `EventScheme` with `amount` input
- `MissionScheme` with `formula` x0.1 on `amount`


### Laneige
- https://hk.laneige.com/member/member.html
- `PointScheme` 分
- `RewardScheme` with `ItemScheme` and `RewardSchemeCost` for redemption
- `TierScheme` for Tiers (Basic, Silver, Diamond)
- Order `EventScheme` with `amount` input
- `MissionScheme` with `formula` x0.05(Basic) x0.1(Silver) x0.15(Diamond) on `amount`
- `MissionScheme` for each Tier
    - Year-end expiry always be 12-31 of the year
    - With **on-archeve** checkpoint to upgrade to Silver, Diamond, and a promotion gift `RewardScheme`
    - With **on-fail** checkpoint to downgrade to Basic, Silver if not fulfil the condition
- `MissionScheme` for birthday (1 year periodic)
- `MissionScheme` for recycle program with `formula` x2 point for each bottle

### Slowood
- https://www.slowood.hk/zh/pages/slofolk-membership-program
- `PointScheme` 'Seed' with expiry on 12-31 of the year
- `RewardScheme` with `ItemScheme` and `RewardSchemeCost` for redemption
- `TierScheme` for Tiers (Sprout, Seedling, Sapling, Plant)
- Order `EventScheme` with `amount` input
- `MissionScheme` with `formula` x1, x2(On wednesday / birth month with some tiers), on `amount`
- `MissionScheme` for each Tier
    - Year-end expiry always be 12-31 of the year
    - With **on-archeve** checkpoint to upgrade, and a promotion gift `RewardScheme` for some tiers
- `MissionScheme` for birthday (1 year periodic)

Matching real world scenarios to Loyalty Engine components can be a practice or validating the features of Loyalty Engine.

## Try not to miss hidden logics
Please do make sure the membership program is fully understood. Try to run through the scenarios with client, and try to find out the hidden logics. e.g.: By which spending amount to upgrade to next tier? How to retain the tier? etc.