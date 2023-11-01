# Mission
Is one of the **3 Interactive Component** of Loyalty Engine. All loyalty logics from events to rewards are defined in Mission. Mission can be assigned to account in the very beginning, or being rewarded by other missions, redeem.

## What Mission can do
- **Hook** - Listen to event and calculate the progress of the mission
- **Reward** - Issue reward when progress archived certain amount

## Mission Type
Mission will have different behavior based on its type. There are 3 types of missions:

- **One-Time** - Once the mission is completed, it will be archived and cannot be completed again.
    - e.g. Signup, First Order, etc.
- **Periodic** - The mission can be completed multiple times, but only once per period.
    - e.g. Birthday, Monthly Campaign, etc.
- **Repeatable** - The mission can be completed multiple times. No periodic limit.
    - e.g. Order, Visit, etc.

```mermaid
---
title: Comparison of different mission types
---
flowchart LR
subgraph "One-Time Mission"
direction LR
e1((Trigger Event)) --> c1{Complete?} --YES--> f1((Archive))
c1{Complete?} --NO--> e1
end
subgraph "Periodic Mission"
direction LR
e2((Trigger Event)) --> c2{Complete?} --YES--> f2((Archive)) --> pe2{Next Period?} --YES--> r2((Restart)) --> e2
c2{Complete?} --NO--> e2
pe2 --NO--> f2
end
subgraph "Repeatable Mission"
direction LR
e3((Trigger Event)) --> c3{Complete?} --YES--> r3((Restart)) --> e3
c3{Complete?} --NO--> e3
end
```

## Earn by Trigger vs. Expire
- **Trigger** - When a event is triggered, event data will pass to formula to calculate the progress of the mission, and archive the checkpoint if any.
- **Expire** - The moment before the mission is expired, the mission will check if any checkpoint failed or meet. If yes, the reward will be issued.

## From Event to Reward
A brief concept of how a Event undergoes the process of earning reward.

```mermaid
---
title: Event to Reward
---
stateDiagram-v2
    direction LR
    [*] --> Signup
    [*] --> Visit
    [*] --> Order
    Signup --> Missions
    Visit --> Missions
    Order --> Missions
    state Missions {
        state is_formula_pass <<choice>>
        state is_checkpoint_archive <<choice>>
        [*] --> Formula
        Formula --> is_formula_pass : Calculate condition by input (amount, sku, etc.)
        is_formula_pass --> Failed: (condition FALSE)
        is_formula_pass --> Progress: Passed formula (condition TRUE)
        Progress --> Checkpoints: Check if any checkpoint archived
        Checkpoints --> is_checkpoint_archive
        is_checkpoint_archive --> None:Nno checkpoint archived
        is_checkpoint_archive --> [*]: Checkpoint archived, earn reward
    }
    Missions --> Reward

```

## From Event - `MissionSchemeHook`

### Setup UI
Hook is a listener to event. It will be triggered when the event is fired. The hook will then calculate the progress of the mission and archive the checkpoint if any.

![Loyalty Engine Setup UI For RewardSchemeUnit](../img/ui-for-setup-mission-scheme-hooks.png)

 - Hooks can be multiple
 - A hook must bind to a `EventScheme`
 - A hook must set a `Formula`

### Formula Builder
A nested UI builder to build a formula for the hook. The formula will be used to calculate the progress of the mission. **Please consult a developer or loyalty expert when scenario is complicated.**

![Loyalty Engine Setup UI For RewardSchemeUnit](../img/ui-for-formula-builder-explained.png)

#### Formula Builder can be explained in 4 parts

- ##### Formula Type
    Controls what would the formula return. Settings might differ based on the type.
    There are 2 types of formula:
    - **Conditional** - Return value will be TRUE or FALSE
    - **Calculative** - Return value will be a number
- ##### Subject
    A selection which is the entry point of the formula, It can be a a math or logic operations on child formulas. It can be a value that may passed from Event.
    - **Operator** - A math or logic operation on child formulas, Different Formula Types will have different operators.
        - Conditional: `AND`, `OR`
        - Calculative: `SUM`, `MAX`(Pick highest amount), `MIN`(Pick lowest amount)
    - **Input** - A value that may passed from Event. Options are based on the `InputSchema` of Event Scheme. Different type of input will populate different `Comparator / Multiplier`
    - **Additional Info** - A value that passed from the context of the event that are not defined in the `InputSchema`. It is controlled in coding level and code change is required to add or remove options.
- ##### Comparator / Multiplier
    The part followed by the subject. It is used to compare the subject with a value. The value can be a constant or a formula.
    - Supported types are `Number`, `String`, `Boolean`, `DateTime` etc. will keep updating by adding new types.
    - ##### Aggregative Comparator / Multiplier
        When `subject` is a input type with list of `Object`s i.e. Order Items from POS. comparator will changed to `Aggregative` mode. With additional `Target` and `Method` inputs added before the comparator.
        ![Loyalty Engine Setup UI For RewardSchemeUnit](../img/ui-for-formula-aggregative-subject.png)
        - **Target** - A selection based on `Method` input, for subject of value to be calculated before comparing. A default option `quantity` for passing the quantity of the item. Other options are based on the properties of the aggregating Object.
        - **Method** - Math operation for calculating the value before comparing. Options are:
            - `COUNT` - Count the number of items
            - `MAX` - Pick the highest value among the items
            - `MIN` - Pick the lowest value among the items
            - `SUM` - Sum up the value of the items

- ##### Sub-Conditons
    A nested formula builder for building a sub-condition. It is only available on `Calculative` Formula or `Aggregative Comparator`

## To Reward - `MissionSchemeCheckpoint`

### Setup UI
![Loyalty Engine Setup UI For RewardSchemeUnit](../img/ui-for-setup-mission-scheme-checkpoints.png)

- **Checkpoint Types** - There are 3 types of checkpoint, `on-achieve` will be fired **during event trigger**, `on-meet` and `on-fail` will be fired **during mission expiry**.
    - `on-achieve` - The reward will be issued when the progress amount is **passed** the `Progress Amount`
    - `on-meet` - **v0.5.15**  The reward will be issued when the progress amount is **passed** the `Progress Amount` before the mission expired.
    - `on-fail` - The reward will be issued when the progress amount is **not passed** the `Progress Amount` before the mission expired.
- **Progress Amount** - The amount of progress to archive the checkpoint
- **Tier Scheme** - Only account with the tier will be able to earn the reward
- **Weight** - **v0.5.15** During expiry, the reward with higher weight will issue before the lower one, mostly used for deactiving other missions before other checkpoint calling (For `on-meet` only)
- **Reward Scheme** - The reward to be issued
- **Quantity** - The quantity of the reward to be issued
- **Notification** - A notification will be sent to the account when the checkpoint is archived. (Out of Loyalty Engine Scope)

## Model and Fields

### MissionScheme
|Field|Explainations|
|---|---|
|id|Primary Key|
|code|Unique Identifier|
|type|**Detail explaination on above** <br/>`one-time` `periodic` `repeatable`, |
|overflow_repeatable_progress|**repeatable only** The last repeated mission progress will truncated to 0 in a event trigger.|
|max_completion_count|**repeatable only** How many times the mission can be repeated|
|max_progress_amount|If passed the mission `status` will be `completed`|
|initial|If TRUE, account will be assigned automatically when created|
|expiry_setting|When will the missin expire from **earning time**|
|activate_at|All `Missions` belong to this `Scheme` won't operate **before** it|
|expire_at|All `Missions` belong to this `Scheme` won't operate **after** it|
|status|Golbal control of all `Missions` belong to this `Scheme`. <br/>`active` - Will distribute in initial and operate<br/>`disabled`, `removed` - Won't distribute in initial and won't operate|

### MissionSchemeHook
|Field|Explainations|
|---|---|
|id|Primary Key|
|event_scheme_id|The `EventScheme` it listen to|
|formula|**Detail explaination on above** Define `Formula`,|

### MissionSchemeCheckpoint
|Field|Explainations|
|---|---|
|id|Primary Key|
|type|**Detail explaination on above** `on-archive`, `on-meet`, `on-fail`|
|progress_amount|Amount to earn the reward|
|weight|**Detail explaination on above** Reward issue priority|
|reward_scheme_id|Reward to be issued|
|quantity|Amount of Reward to be issued|

### Mission
|Field|Explainations|
|---|---|
|id|Primary Key|
|account_id|Referring the account which it belongs|
|expire_at|won't operate **after** it|
|issued_at|Time of mission record added to account|
|source|`reward` - Earned from `mission` or `reward` issue / redeem <br/>`direct` - Direct  issue<br/>`initial` - Issued when account created <br/>`renewal` - Created from last repeated mission, or a new one from last periodic mission|
|status|`active` - normal and operating<br/>`completed` already completed and archived<br/>`disabled` - had been stopped from operating<br/>`renewed` - replaced by newer one and archived<br/>`expired` - had been processed by **expiry cron job** and archived|
|reprat_count|For consolidation when the mission had been completed multiple times in a Event trigger|

### MissionProgress
|Field|Explainations|
|---|---|
|id|Primary Key|
|amount|Actual Progress Amount|
|intended_amount|Should be equal to `amount` in normal situations. Not for calculation. Can be used when max progress reached but still want to calculate how much progress it gain|
|source|`direct` - Direct issue progres (via admin panel)<br/>`hook` - From hook listened from event trigger<br/>`inherited` - Passed from overflowed progress of a completed mission. **Please see reward component**<br/>`overflow` - A "remainder" progress from a last repeated mission, only on `repeatable` mission with `overflow_repeatable_progress` turn on<br/>`reward` - Issued by a reward with `mission_progress` Unit type|
|issued_at|Time of the progress added|