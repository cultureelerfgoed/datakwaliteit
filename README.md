# datakwaliteit pipline

Dit is een demo opstelling waarmee datakwaliteit middels een datapipeline gemeten wordt. De coden wordt ontwikkeld in Jupyter notebooks.

De code wordt uitgevoer in een lokale DuckLake met een architectuur van medallion lakehouse. Het is een single-user oplossing, maar er is potentie om hier een multi-user oplossing van te maken.

Het configureren van de pipeline is technisch laagdrempelig gehouden. Middels parameters in functies worden kwaliteitsmetigen geconfigureerd.

Voor het maken van nieuwe functies is wel Python en SQL kennis vereisd.

## notebooks

In de demo opstelling worden vier notebooks gebruikt:

- start_ducklake.ipynb
  - DuckLake wordt geinstalleerd en opgestart
    - dit notebook aangeroepen door een `pipeline notebook`
- laad_data.ipynb
  - catalogi, schema's en tabellen worden aangemaakt
  - de data uit de bestanden in de folder `brondata` worden ingeladen
  - dit notebook aangeroepen door een `pipeline notebook`

- datakwaliteit_udf.ipynb
  - een notebook waarin voor elk type kwaliteitsmeting een functie is aangemaakt; User Defined Function (UDF)
  - elke kwaliteit meting functie:
    - heeft een unieke code
    - dit notebook aangeroepen door een `pipeline notebook`
- datakwaliteit_pipeline.brons_p.demo_dataset.ipynb
  - dit is `pipeline notebook` die de datakwaliteit gaat meten
  - de naamgeving van dit notebook bevat de naam van de `laag` en de naam van het `schema`
  - voordat meetingen beginnen wordt DuckLake geconfigureerd en gestart door de volgende notebooks te laden:
    - start_ducklake.ipynb
    - laad_data.ipynb
    - datakwaliteit_udf.ipynb
- start_ducklake_ui.ipynb
  - het starten en stoppen van een userinterface

## test data

De test data staat in de folder `brondata`. Het betreft hier een fictieve dataset die de demonstratie van deze oplossing ondersteunt.

- dataset_nederland.csv
  - een dataset
  - is een fictieve dataset
  - in de data zijn bewust fouten aangebracht t.b.v. het meten van de datakwaliteit
- referentiedata_gemeente.csv
  - een referentie databestand
- referentiedata_provincie.csv
  - een referentie databestand
- kwaliteit_log_template.csv

## catalogi, shema's en tabellen

Met het notebook `laad_data.ipynb` worden catalogi, shema's en tabellen aangemaakt.

**Catalogi**

- voor elke medallion architectuur laag wordt een catalogus aangemaakt
  - brons
  - zilver
  - goud
- voor het toepassen van OTAP kunnen de volgende postfixes toegevoegd worden (optioneel)
  - _p = productie
  - _t = test
  - _a = accesptatie
  - _o = ontwikkel

**Schema's**

- voor elke dataset (database) wordt een schema aangemaakt
- in deze demo opstelling worden de volgenden schema's aangemaakt:
  - brons_p.demo_dataset
  - brons_p.referentiedata
  - brons_p.datakwaliteit

**Tabellen**

- in deze demo opstelling worden de volgenden tabellen aangemaakt:
  - brons_p.demo_dataset.nederland
    - een dataset die gevuld worden met de data uit `./brondata/dataset_nederland.csv`
  - brons_p.referentiedata.gemeente_lijst
    - een referentietabel die gevuld worden met de data uit `./brondata/referentiedata_gemeente.csv`
  - brons_p.referentiedata.provincie_lijst
    - een referentietabel die gevuld worden met de data uit `./brondata/referentiedata_provincie.csv`
  - brons_p.datakwaliteit.log
    - een template tabel die gevuld worden met de data uit `./brondata/kwaliteit_log_template.csv`

## datakwaliteit meetresultaten

Van een kwaliteitsmeting wordt het resultaat weggeschereven in de `kwaliteit log` tabel. Tevens worden gevonden onregelmatigheden wegegschreven in csv bestanden die in de folder `datakwaliteit_meetwaarden` worden opgeslagen.

## user interface

DuckLake heeft de mogelijkheid om het lakehouse in een grafische omgeving te ontsluiten. Deze user infterface (UI) opent in de webbrowser.
Hiervoor wordt wordt gebruik gemaalt van de [UI Extension](https://duckdb.org/docs/lts/core_extensions/ui) van DuckDB. Het notebook `start_ducklake_ui.ipynb` bevat de code om deze UI te openen en weer te sluiten.

## SQL injecttion

Binnen het notebook `datakwaliteit_pipeline.brons_p.demo_dataset.ipynb` wordt een SQL statment opgebouwd uit parameters in een f-string. In theorie zouden deze parameters kwaadaardige code in een SQL statement kunnen laden. Hiervoor is gekozen omdat nog niet gekozen is voor een definitieve SQL-engine. Zodra deze keuze wel gemaakt is, kan een specifieke oplossing gekozen worden. De omgeving waarbinnen de code draait is voldoende afgescherm om SQL injecttion te voorkomen.

## multi-user oplossing

Door gebruik te maken van een centrale Postgres database en een centraal S3 compitible opslag systeem, zou deze oplossing ook multi-user gemaakt kunnen worden. Dit is iets wat nog getest moet worden.