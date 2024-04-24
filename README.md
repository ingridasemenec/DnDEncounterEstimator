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

&#8595;

[filtering.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/filtering.ipynb) script takes the extracted csv file

&#8595;

[scaling.ipynb](https://github.com/ingridasemenec/DnDFireballProject/blob/main/scaling.ipynb) script...

## Modeling



