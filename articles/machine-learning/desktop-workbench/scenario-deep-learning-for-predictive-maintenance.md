---
title: Obsáhlý learning k reálné scénáře prediktivní údržby – Azure | Dokumentace Microsoftu
description: Zjistěte, jak replikovat kurz na obsáhlý learning a prediktivní Údržba s využitím Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 83e1f14db317f59ab2063a9d020adbdb6fe78e5f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644535"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Obsáhlý learning k reálné scénáře prediktivní údržby

Hloubkové učení je jedním z nejoblíbenějších trendy ve službě machine learning a má aplikací na mnoha oblastech, včetně:
- Standardizovaných automobilů a robotika.
- Rozpoznávání řeči a obrazu.
- Finanční Prognózování.

Také známé jako neuronových sítí (DNN), tyto metody jsou INSPIROVANÉ jednotlivé neurons, které jsou v raném (biologické neuronové sítě).

Neplánované prostoje zařízení můžou mít pro řadu firem vážné důsledky. Je důležité mít pole zařízení s maximalizovat výkon a využití a minimalizovat nákladná, neplánované výpadky. Včasné identifikaci problémů pomáhají přidělit prostředky omezené údržby v nákladově efektivní způsob a vylepšení kvality a zadat řetězec procesy. 

Strategie prediktivní údržby (PM) používá k určení stavu zařízení preventivně provádět údržbu, aby se zabránilo výkon negativní počítačů metody machine learning. V odp. data se shromažďují průběžně monitorovat stav počítače a pak analyzovat k nalezení vzorce a předvídat selhání. [Dlouhé paměti krátká období (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sítě jsou pro toto nastavení, protože jsou navržené ke Učte se od data sekvencí.

### <a name="cortana-intelligence-gallery-github-repository"></a>Úložiště GitHub Galerie Cortana Intelligence

Cortana Intelligence Gallery PM kurzu je veřejného úložiště GitHub ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) můžete hlásit problémy a Přispívání.


## <a name="use-case-overview"></a>Přehled případu použití

Tento kurz používá příklad simulované leteckého motoru selhání spuštění událostí předvést prediktivní údržby proces modelování. Scénář je popsaný v [prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Hlavní předpokladů v tomto nastavení se modul postupně snížený výkon za dobu života. Zjištěním snížení výkonnosti lze zjistit v měření ze senzorů motoru. PM se pokusí o vztah mezi změnami těchto hodnot snímačů a historické selhání. Model lze potom odhadnout, kdy a modul může selhat v budoucnu na základě aktuálního stavu senzor měření.

Tento scénář se vytvoří síť LSTM k predikci zbývající životnosti (RUL) z leteckých motorů pomocí historických senzor hodnot. Tento scénář používá [Keras](https://keras.io/) knihovna [Tensorflow](https://www.tensorflow.org/) obsáhlého learningu framework jako výpočetní modul. Scénář trénovat LSTM jednu sadu modulů a testy sítě na skupinu nezobrazený modul.

## <a name="prerequisites"></a>Požadavky
- [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
- Azure Machine Learning Workbench, se pracovní prostor vytvořený.
- Operacionalizace modelu: Azure Machine Learning Operacionalizace s prostředím místní nasazení nastavení a [účtu správy modelů Azure Machine Learning](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvoření nového projektu s použitím v tomto příkladu jako šablony:

1. Otevřete aplikaci Machine Learning Workbench.
2. Na **projekty** stránce **+** a pak vyberte **nový projekt**.
3. V **vytvořit nový projekt** podokně zadejte informace pro nový projekt.
4. V **Hledat v šablonách projektů** pole vyhledávání zadejte "Prediktivní údržby" a vyberte **obsáhlý Learning pro prediktivní údržbu scénář** šablony.
5. Vyberte **Vytvořit**.

## <a name="prepare-the-notebook-server-computation-target"></a>Připravte cílové výpočetní server poznámkového bloku

Ke spuštění v místním počítači, aplikace Machine Learning Workbench **souboru** nabídku, vyberte buď **otevřít příkazový řádek** nebo **otevřete rozhraní příkazového řádku Powershellu**. Toto rozhraní příkazového řádku umožňuje přístup pomocí služby Azure `az` příkazy. Nejdřív přihlaste ke svému účtu Azure pomocí příkazu:

```
az login
``` 

Tento příkaz zjistí ověřovací klíč pro použití s https:\\aka.ms\devicelogin adresy URL. Rozhraní příkazového řádku počká, až operace přihlášení zařízení vrátí a poskytuje některé informace o připojení. Další, pokud máte místní [Docker](https://www.docker.com/get-docker) instalace, připravte místní výpočetní prostředí pomocí příkazu:

```
az ml experiment prepare --target docker --run-configuration docker
```

Doporučuje se spouštět [dat virtuálního počítače VĚDY pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) pro požadavky na paměť a disk. Po dokončení konfigurace datové VĚDY, připravte se na vzdáleném prostředí Dockeru pomocí následujících dvou příkazů:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po připojení ke vzdálené kontejneru Dockeru, připravte prostředí compute DSVM Dockeru pomocí příkazu: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Výpočetní prostředí Docker připravený, otevřete server poznámkového bloku Jupyter aplikace Machine Learning Workbench **poznámkových bloků** kartu, nebo spusťte server založené na prohlížeči pomocí příkazu: 

```
az ml notebook start
```

Příklad poznámkové bloky jsou uložené v adresáři kódu. Poznámkových bloků nastavení jsou spuštěna sekvenčně, od prvního poznámkového bloku (Code\1_data_ingestion.ipynb). Když otevřete každý Poznámkový blok, budete vyzváni k výběru výpočetní jádro. Zvolte jádra _Template [Connection_Name] [Project_Name] se promítnou u dříve nakonfigurované datové VĚDY.

## <a name="data-description"></a>Popis dat

Šablona používá tři datové sady jako vstupy v souborech PM_train.txt PM_test.txt a PM_truth.txt. 

- **Trénování dat**: selhání spuštění data motoru letadla. Trénování dat (PM_train.txt) se skládá z několika, s množstvím proměnných časové řady s *cyklu* jako časovou jednotku. Zahrnuje 21 údajů snímačů přes pro každý cyklus. 

    - Každá Časová řada se generuje z jiný modul stejného typu. Každý motor začíná s různým stupněm počáteční wear a některé jedinečné výrobní variace. Tyto informace Neznámý pro uživatele. 

    - V tomto Simulovaná data. modul je považován za normálně na začátku každé časové řady. To se začne snižovat v určitém okamžiku během řadu provozní cykly. Zjištěním snížení výkonnosti postupuje a zvětší velikost. 

    - Když je dosaženo předdefinované prahové hodnoty, modul není považován za bezpečný pro další operace. Poslední cyklus každá Časová řada je bodem selhání tohoto modulu.

- **Testovací data**: letadla modul provozních dat, bez zaznamenaných událostí selhání. Testovací data (PM_test.txt) má stejné schéma dat jako trénovací data. Jediným rozdílem je, že data nenaznačuje, že pokud dojde k selhání (se poslední časové období *není* představují bodem selhání). Není znám, kolik více cyklů tento modul můžete naposledy předtím, než selže.

- **Data pravdy**: informace true zbývající cyklů pro každý motor v testovacích datech. Data základu pravdy poskytuje počet cyklů zbývající práce pro moduly v testovacích dat.

## <a name="scenario-structure"></a>Struktura scénář

Scénář pracovního postupu je rozdělen na tři kroky a spuštění každého kroku v poznámkovém bloku Jupyter. Každý Poznámkový blok vytvoří data artefakty, které jsou trvalé místně pro použití v poznámkových bloků.

### <a name="task-1-data-ingestion-and-preparation"></a>Úloha 1: Přijímání a příprava dat

Na Data Ingestování Poznámkový blok Jupyter v Code/1_data_ingestion_and_preparation.ipnyb načte do formátu Pandas DataFrame tři vstupní datové sady. Poznámkový blok pak připraví data pro modelování a provede některé předběžné datové vizualizace. Datové sady se ukládají místně na výpočetní kontext pro použití při vytváření modelu má Poznámkový blok Jupyter.

### <a name="task-2-model-building-and-evaluation"></a>Úloha 2: Vytváření modelů a vyhodnocení

Model vytváření má Poznámkový blok Jupyter v Code/2_model_building_and_evaluation.ipnyb načte datové sady pro trénování a testování z disku a vytvoří síť LSTM pro trénovací datové sady. Výkon modelů se měří na testovací datové sady. Výsledný model je serializován a uloženy v místním výpočetním kontextu pro použití při operacionalizaci úloh.

### <a name="task-3-operationalization"></a>Úloha 3: Operacionalizace

Poznámkový blok Jupyter Operacionalizace v Code/3_operationalization.ipnyb uložené modelu používá k vytvoření schéma pro volání modelu na hostovaný v Azure webové služby a funkce. Poznámkový blok testuje funkce a pak do souboru LSTM_o16n.zip zkomprimuje prostředky. Načtení souboru do kontejneru úložiště Azure pro nasazení.

Soubor LSTM_o16n.zip nasazení obsahuje následující artefakty:

- **webservices_conda.yaml**: definuje balíčky Pythonu, které jsou potřeba ke spouštění modelem LSTM na cíl nasazení.  
- **service_schema.JSON**: definuje schéma dat, která se očekává LSTM modelem.   
- **lstmscore.PY**: definuje funkce, které cíle nasazení běží na skóre nová data.    
- **modellstm.JSON**: definuje LSTM architektury. Lstmscore.py přečtou architektury a váhy inicializovat model.
- **modellstm.H5**: definuje model váhy.
- **test_service.PY**: testovací skript, který zavolá koncový bod nasazení s testem datových záznamů. 
- **PM_test_files.pkl**: test_service.py skript načte modul historických dat ze souboru PM_test_files.pkl a odešle webová služba dostatek cyklů pro LSTM vrátit pravděpodobnost selhání modulu.

Poznámkový blok funkce ověřuje pomocí definice modelu před balíčky operacionalizace prostředky pro nasazení. Pokyny pro nastavení a testování webové služby jsou zahrnuty na konci Code/3_operationalization.ipnyb poznámkového bloku.

## <a name="conclusion"></a>Závěr

Tento kurz obsahuje jednoduchý scénář, který používá hodnoty snímačů k následné predikci. Pokročilejší scénáře prediktivní údržby, jako [prediktivní údržby modelování Průvodce R Poznámkový blok](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) můžete použít mnoho zdrojů dat, jako je například údržba historické záznamy a protokoly chyb a funkce počítačů. Další zdroje dat může vyžadovat různých způsobů pro použití s obsáhlým learningem.


## <a name="references"></a>Odkazy

Příklady dalších prediktivní údržby případy použití pro různé platformy poskytují následující odkazy:

* [Šablona řešení prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Průvodce modelováním prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktivní Údržba modelování příručka, která je pomocí služby SQL Server R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Prediktivní Údržba modelování Poznámkový blok Python Průvodce](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Prediktivní údržby pomocí PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Reálné scénáře prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Další postup

Další příklad scénáře jsou k dispozici v aplikaci Machine Learning Workbench, které popisují další funkce produktu. 
