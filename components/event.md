# Event
Is one of the **3 Interactive Component** of Loyalty Engine. Event is the entry point of any Loyalty actions. It can be triggered by external system, such as POS, Operation Portal / Admin Panel or via API for custom campaign.

## Define Input Schema

https://json-schema.org/

### Setup UI

![Loyalty Engine Setup UI For RewardSchemeUnit](../img/ui-for-setup-event-input-schema.png)

### Readable Data Type for Loyalty Engine
 - `string` - text, email, store code, etc
 - `number` - order amount, date of birth, etc
 - `boolean` - is birthday, is first purchase, etc
 - `datetime` - trigger time, order time, etc
 - `array-of-objects`
     - Data structure that contain multiple fields
     - Can be order items object which include `sku`, `price`, `quantity`, etc

## Model and Fields

### Event Scheme
|Field|Explainations|
|---|---|
|id|Primary Key|
|code|Unique Identifier|
|input_schema|Define Input |
|activate_at|Won't accept trigger **before** it|
|expire_at|Won't accept trigger **after** it|

### Event
|Field|Explainations|
|---|---|
|id|Primary Key|
|account_id|Reference to the account holder|
|input|Data submitted along with the Event trigger, will pass along to mission for progress calculations|
|triggered_at|Time when the event is triggered|