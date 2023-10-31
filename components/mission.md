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

``````