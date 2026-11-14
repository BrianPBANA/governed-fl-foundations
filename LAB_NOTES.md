# BANA 690A Federated Learning I: Lab Notes

**Name:MYONG Pak **
**GitHub username:BrianPBana**

One to three sentences per prompt, in your own words, with a number from your
own run where the prompt asks for one. Commit this file to your fork by the
end of class (see README, "Turning in your notes").

## Lab 0: The world
The coordinator holds the global model and a neutral evaluation set (500 consented, de-identified rows per hospital). It never holds any hospital's patient records; those stay at home.

## Lab 1: FedAvg from scratch
[Hospital] gains the most from federating, going from [local-only] to [FedAvg] accuracy (about [+X] points); small hospitals gain most. To get Northgate to join, I would offer it a locally fine-tuned version of the model, a seat on the steering committee, and charter protections against poisoning and data leakage.

## Lab 2: McMahan's knobs
Local epochs (E) cut rounds the most: at C=1.0, E=1 needs 27 rounds and E=20 needs only 2, while changing C barely matters (27/28/27). The price is more local computation (27 to 40 total local epochs, 80 at C=0.34) and more drift when hospitals differ.

## Lab 3: Heterogeneity
Label skew hurt the most: Kestrel (readmission rate 0.48) fell from 0.786 local-only to 0.730 under FedAvg; concept skew also hurt Kestrel (0.828 to 0.804), while quantity skew actually helped tiny Kestrel (0.730 to 0.772). A few epochs of local fine-tuning fixed it every time (Kestrel 0.792 under label skew, 0.828 under concept skew).

## Lab 4: Vertical federation
Only partial scores and residuals crossed the wire (108,000 numbers, 0 feature values), and vertical FL matched the forbidden all-columns model (0.834 vs 0.806 for the hospital alone). But the residuals gave the insurer 100% of the labels and the partial scores leak a projection of the claims data, so neither party should own the model alone: the agreement should name a joint controller and restrict its uses.

## Lab 5: Threats
FL keeps raw data at home, but the updates can leak it: from a one-patient update the coordinator reconstructed the patient exactly, and membership inference reached 65% on Kestrel's local model (52% on the federated model). Update norms would have caught boost (10.37) and the backdoor (8.12) against honest norms of 0.23 to 1.79, but not label flipping (1.04 to 1.79), even though flipping by the 35% hospital dropped accuracy to 0.727.

## Lab 6: Defenses
Default aggregator: trimmed mean, because it cut backdoor success from 57% to 8% (clipping alone only reached 28%) and cost almost nothing with no attack (0.813 vs 0.814). Minimum participants: at least 3 per round, because with only two hospitals each can subtract its own update from the sum and recover the other's exactly. Noise multiplier: 0.3, because in the five-hospital grid it cost no accuracy (0.814, same as no noise) while still hiding each hospital.

## Lab 7: FL as a governance control
I chose robust aggregation over secure aggregation: v1.0 with secure aggregation was destroyed by the boost attack (0.525, BLOCKED), and v1.1 with coordinator-side rejection of updates above norm 6.0 removed the attacker and PASSED (0.793 global, Kestrel 0.75, gap 0.082). Trimmed mean alone was BLOCKED because it under-served tiny Kestrel (gap 0.118). My compensating control is DP noise of 0.3, since the coordinator can now see individual updates. The clause I would add: any member whose update is rejected for exceeding the norm limit is suspended pending a committee review, and every rejection is recorded in the audit log.

## Declaration
I ran these labs myself in my own Codespace. Myong Brian Pak 