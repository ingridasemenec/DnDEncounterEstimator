# DnDFireballProject
Dungeons and Dragons (D&amp;D) is a massively popular table-top role-playing game where players embody characters on a fantasy adventure. Player versus monster combat is a key component in D&amp;D gameplay and relies on complex interactions between player and monster statistics, actions, and outcomes. These combat encounters determine the adventure's trajectory and vary in difficulty to the players. Encounter difficulty is managed by a Dungeon Master (DM), who selects the monsters, controls their actions, and creates an engaging battle. However, the existing system to gauge encounter difficulty, known as challenge-rating, has significant limitations and fails to address the nuances of combat dynamics. Encounters can vary widely in actual versus intended challenge, leading to frustration among players and DMs alike. To remedy this, we embarked on a data-driven exploration using FIREBALL: a dataset comprising 25,000 real gameplay combat encounters collected via the Avrae Discord bot. We aimed to develop a more sophisticated tool to assist DMs in crafting balanced and engaging combat encounters.

[Download the FIREBALL dataset!](https://datasets.mechanus.zhu.codes/fireball-anonymized-nov-28-2022-kfdjl.tar.gz) 
FIREBALL is released for research purposes only under a CC-BY-4.0 license.

## Team Members:
- [Ingrida Semenec](https://www.linkedin.com/in/ingrida-semenec/)
- [Kane York](https://www.linkedin.com/in/alxandr-kane-york-6583b7a3/)
- [Noah Waller](https://www.linkedin.com/in/noahcwaller/)
- [Saswat Mishra](https://www.linkedin.com/in/saswat-mishra-b3171535/)
- [Douglas Staffer](https://www.linkedin.com/in/douglas-stauffer-r/)


## This Repository

Within this repository, we provide scripts to extract, filter, and manipulate data sourced from the raw FIREBALL data files. Following data processing, we offer a collection of scripts aimed at modeling specific features to predict combat success (measured by the ratio of post-combat health points to pre-combat health points) or the probability of a total party kill (TPK).


## Data Processing

[extractor.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/extractor.ipynb) : processes the raw FIREBALL data in JSON format, extracting the desired features and storing them as a new database in a CSV file.

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
We were particularly interested in `command` and `combat_state_update` events. The `command` event contained information for players and monsters joining combat initiatives, which was a tool to count active participants in a given combat. The `combat_state_update` events contained all active combatant information such as their character sheet statistics, as well as the `human_readable` element, which contained a string with updated player and monster health point (HP) ratios. By checking the last instance of `combat_state_update` we were able to extract the last recorded HP of each player post-combat. 

&#8595;

[filtering.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/filtering.ipynb) : processes the extracted CSV file, applying predefined filters and performing one-hot encoding for all player classes. Filtering is essential to eliminate evident outliers, "homebrew" game content (i.e. non-standard, custom rules that fall outside the intended game limitations), and instances where information was improperly recorded in the raw data.

The Filters removed instances of:
 * non-standard player classes 
 * combat instances with no combat
 * combats with no HP information
 * extreme pre-combat HP (> 350)
 * extreme post-combat HP (> 350)
 * party sizes 10 and above
 * parties with player levels above 20
 * parties with either no HP or too high HP
 * parties with ACs above 38
 * parties with an ability score above 22

&#8595;

[scaling.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/scaling.ipynb) : script processes the extracted and filtered CSV file, applying scaling to the monster level and spell slots. Scaling for these factors accounts for non-linear power increases in monster level and spell slot level, as outlined in the [Dungeon Master's Guide](https://dnd.wizards.com/products/dungeon-masters-guide).

Monster-level scaling utilizes encounter multipliers as outlined in the [Dungeon Master's Guide](https://dnd.wizards.com/products/dungeon-masters-guide) on page 82.

Spell-slot scaling utilizes spell point values provided in the [Dungeon Master's Guide](https://dnd.wizards.com/products/dungeon-masters-guide) on page 289. 

Once the scaling is implemented, the script generates new columns for the `weighted_monster_level` and the `weighted_spell_slots`, appending them to the database. This results in the final version of the CSV file prepared for analysis.


## EDA
[CR_ClassicalModels_levelranges.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/CR_ClassicalModels_levelranges.ipynb) : script is designed to test the Challenge Rating system using the processed data. The notebook contains: 
* EDA regarding the `party_total_level`, `weighted_monster_level`, `party_size`, `monster_number`
* Classical Modeling including 'Linear Regression', 'Random Forest Regressor', 'Gradient Boosting Regressor', and 'XGBoost Regressor'.
 * Splitting the data into level ranges to investigate different types of correlations at certain stages of gameplay. 

## Modeling
### Classical Models
[DnD Classical Models.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/DnD%20Classical%20Models.ipynb) : utilizes measures, metrics, and correlation to investigate and validate features before modeling.
* PyCarat was used for directional guidance for what models to build 
* The Final Classical Models Used:
* XgBoost
* AdaBoost
* GradientBoost
* Random Forest
* LightGBM
* PLS
* OLS
* MSE, RMSE, and R2 were used for the scoring for the model

### Dimensionality Reduction
[dimensionality_reduction.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/dimensionality_reduction.ipynb) : script reduces the features to attempt to improve model fitting. It primarily employs PCA and PLS.

### Neural Network
[NeuralNetwork.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/NeuralNetwork.ipynb) : script reads the filtered and scaled data and trains two deep neural networks with different architectures. Both the models are trained with validation early-stopping and dropout. Each of these are then followed by SHAP analysis to obtain the feature importance.
The OnePathNN model trains with 40 input features while the TwoPathNN model separates the player-related features and monster-related features as shown in the schematics.

![OnePathNN](https://github.com/ingridasemenec/DnDFireballProject/blob/main/NN_files/one_path_nn.svg)
![TwoPathNN](https://github.com/ingridasemenec/DnDFireballProject/blob/main/NN_files/two_path_nn.svg)


### TPK Classification
[TPK_classification.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/TPK_classification.ipynb) : script tests two binary classification methods to determine the probability of total party kill (TPK). The data contains fewer cases of TPK, therefore the no-TPK cases were randomly undersampled. Logistic Regression (LR) and Random Forest classifiers (RF) were tested. When looking at the ROC curve the LR model performed better than RF. The classifier can be improved with more data that includes TPKs. 

![rocurve](https://github.com/ingridasemenec/DnDFireballProject/blob/main/roccurve.png){width=50%}

