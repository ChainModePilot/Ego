# User Workflow

## For Everyday Users

```
Step 1: Install Ego Pod
  $ pip install ego-pod

Step 2: Initialize Configuration
  $ ego init
  → Interactive guide: select base model, configure data source paths, choose editions
  → Like setting up a new phone, just follow the prompts step by step

Step 3: Start Training
  $ ego train
  → Auto data collection → corpus building → LoRA training → multi-version Ego output
  → In the time it takes to drink a coffee, your personalized AI is ready

Step 4: Use Ego
  $ ego run --edition normal
  → Load base + LoRA, start an AI model aligned with you
  → Try chatting with it, see if it "sounds like you"

Step 5: Update Ego (after data changes)
  $ ego train --update
  → Incremental data collection → retrain → overwrite old version
  → Learned something new recently? Forge again, and Ego catches up
```

## iFay Integration

The iFay body tells Ego the data source addresses through configuration files. Ego Pod automatically completes training, and the produced LoRA adapters are loaded by Fayger.

> For example: Your iFay runs on your phone, periodically syncing new data to a designated directory, then triggering Ego Pod to retrain. After training completes, the new LoRA adapter is automatically pushed to your phone, and Ego "evolves" — the entire process is invisible to you.
