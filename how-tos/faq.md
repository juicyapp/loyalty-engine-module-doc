# FAQ and Troubleshooting


### Event Trigger

- #### Trigger an Event but no reward / nothing happened
    Can be caused by number of reasons, please check the following:
    - Check all the `Schemes` (From `MissionScheme` to `RewardSchemeUnit`) make sure everything is set correctly
    - Check if the coorespponding mission exist and active in the Account
    - If mission active, look through its progresses and see if any progresses had been added by the event
    - See if any event had been added in the transaction

- #### `InvalidNumberOfOperandError` when Trigger an Event
    - This is an error caused by `FormulaBuilder`, mostly related to trigger data not compatible with the input subject of the `Formula`
    - Check all `EventSchemeHook` related to the mission and see if `FormulaBuilder` able to load
    - Make sure data from the Event trigger fullfilled the `InputSchema` of the `EventScheme`
    - Please check **ALL** `MissionScheme` even if the account doesn't have the mission, because the all `MissionSchemeHooks` will run.

- #### `InvalidExpirySetting` when Trigger an Event or Issue Reward
    - Check All RewardSchemeUnits, components in the coorespponding reward schemes, and see if the expiry setting is valid (Must be after the issue / trigger time, exp. "Fixed" time)

- #### Trigger an Event on a repeatable mission, `amount` is "0" / less than expected while `intended_amount` is correct.
    - Check `MissionScheme`'s `max_progress_amount` and `max_completion_count` and see if the account had reached the limit.
    - If the mission is repeatable, make sure the `overflow_repeatable_progress` is unchecked. (Checked means truncate progress amount to 0)

### Formula Builder

- #### `FormulaBuilder` in `EventSchemeHook` unable to load
    - `FormulaBuilder` unable to load some properties from the `EventScheme`

- #### `FormulaBuilder` couldn't found input subject of `EventScheme`, even it had been set
    - Navigate to `EventScheme` in panel, look at `InputSchema`
        - Check if the target property is set
        - If the target property type is supported
        - Make sure target property's "required" is checked.

### Others

- #### Issue a Point, `amount` is "0" / less than expected while `intended_amount` is correct.
    - Check `PointScheme`'s `max_amount` and see if the account had reached the limit.

- #### `RepeatableMissionMissingMaxAmount` when account sign up or and Reward Issue
    - If `Mission` is repeatable, make sure `max_progress_amount` is set.
