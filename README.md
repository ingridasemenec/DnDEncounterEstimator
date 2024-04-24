# DnDFireballProject
Dungeons and Dragons (D&amp;D) is a renowned table-top role-playing game noted for its complex combat encounters. These encounters determine the adventure's trajectory and vary in difficulty. However, the existing system to gauge encounter difficulty, known as challenge-rating, has significant limitations and fails to address the nuances of combat dynamics. To remedy this, we embarked on a data-driven exploration using FIREBALL: a dataset comprising 25,000 real gameplay combat encounters collected via the Avrae Discord bot. We aimed to develop a more sophisticated tool to assist Dungeon Masters (DMs) in crafting balanced and engaging encounters.

[Download the FIREBALL dataset!](https://datasets.mechanus.zhu.codes/fireball-anonymized-nov-28-2022-kfdjl.tar.gz) 
FIREBALL is released for research purposes only under a CC-BY-4.0 license.

## Team Members:
- [Ingrida Semenec](https://www.linkedin.com/in/ingrida-semenec/)
- [Kane York](https://www.linkedin.com/in/alxandr-kane-york-6583b7a3/)
- [Noah Waller](https://www.linkedin.com/in/noahcwaller/)
- [Saswat Mishra](https://www.linkedin.com/in/saswat-mishra-b3171535/)
- [Douglas Staffer](https://www.linkedin.com/in/douglas-stauffer-r/)


## This Repository

In this repository, we present scripts to extract, filter, and process data from the raw FIREBALL data files.


## Data Processing

[extractor.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/extractor.ipynb) script takes in the raw FIREBALL data json files and extracts the wanted features, saving them as a new database in a csv file.
Each saved instance starts with the associated `combat_id` and for each `combat_id` script looks for `event_type`. Each event is one of the following :
```text
- message
- alias_resolution
- snippet_resolution
- command
- button_press
- automation_run
- combat_state_update
```
We were mostly interested in `command` and `combat_state_update` events. The `command` event contained information for players and monsters joining combat initiatives, which was a tool to count active participants in a given combat. The `combat_state_update` events contained all active combatant information such as their character sheet statistics, as well as the `human_readable` part, which had a string containing the updated player and monster hp ratios. This way by checking the last instance of `combat_state_update` we were able to extract the last recorded hp of each player post-combat. 

&#8595;

[filtering.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/filtering.ipynb) script takes the extracted csv file applies the set filters and one-hot encodes all the player classes.

The Filters removed instances of:
 * non-standard player classes 
 * combat instances with no combat
 * combats with no health information
 *  extreme pre-combat hp
 *  extreme post-combat hp
 *  party sizes 10 and above
 *  parties with levels above 20
 *  parties with either no or too high health
 *  parties with ACs above 38
 * parties with an ability score above 22

&#8595;

[scaling.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/scaling.ipynb) script takes the extracted and filtered csv file and applies scaling to the monster level, and spell slots. 

The scaling to monster level is applied using encounter multipliers described in Dungeon Master's Guide page 82. 

The spell slot scaling was applied using spell point values listed in the Dungeon Master's Guide on page 289. 

After the scaling is applied the script creates new columns with the `weighted_moster_level` and the scaled spell slot levels and adds it to the database producing the last version of the csv file ready to be analyzed.

## Modeling



