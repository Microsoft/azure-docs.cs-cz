---
title: Hloubkové learning pro prediktivní údržby reálných scénářů - Azure | Microsoft Docs
description: Zjistěte, jak replikovat kurz na hloubkové learning pro prediktivní údržby pomocí Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.devlang: ''
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 26b061c6bd6ff3ec9d1edbb7d99053d17db9b773
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832597"
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Hloubkové learning pro scénářích reálného světa prediktivní údržby

Hloubkové learning je jednou z nejčastěji používané trendy v machine learning a má aplikací na mnoha oblastech, včetně:
- Standardizovaných aut a robotics.
- Rozpoznávání řeči a bitové kopie.
- finanční prognózy.

Také označuje jako hluboké neuronové sítě (DNN), tyto metody jsou INSPIROVANÉ jednotlivých neurons, které jsou v rámci mozku (biologické neuronové sítě).

Neplánované prostoje zařízení můžou mít pro řadu firem vážné důsledky. Je důležité zachovat pole zařízení s maximalizovat využití a výkonu a minimalizovat prostoje nákladná, neplánované. Časná identifikaci problémů může pomoci přidělení prostředků omezené údržby v nákladově efektivní způsob, jak a vylepšení kvality a zadat řetězec procesy. 

Strategie prediktivní údržby (odp) používá k určení stavu zařízení ho preventivně provádění údržby, aby se zabránilo výkon nežádoucí počítače machine learning metody. V PM data se shromažďují průběžně monitorovat stav počítače a potom analyzovat najít vzory k předvídání selhání. [Dlouhé paměti krátké termín (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) sítě jsou atraktivní pro toto nastavení vzhledem k tomu, že jsou navržené dozvědět se od pořadí dat.

### <a name="cortana-intelligence-gallery-github-repository"></a>Úložiště Cortana Intelligence Galerie GitHub

Cortana Intelligence Gallery kurzu PM je veřejný úložiště GitHub ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) kde hlášení problémů a příspěvky.


## <a name="use-case-overview"></a>Přehled případu použití

Tento kurz používá jako příklad simulované letadla modul spustit selhání události k předvedení prediktivní údržby modelování procesu. Tento scénář je popsán v [prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

Hlavní předpokladů v toto nastavení je modul progresivně degradovaný přes celé jeho životnosti. Snížení lze zjistit v modulu senzor měření. PM pokusí modelu vztah mezi změny v tyto hodnoty čidel a historických selhání. Model lze poté odhadnout, kdy a modul může selhat v budoucnu na základě aktuálního stavu senzor měření.

Tento scénář vytvoří síť LSTM k předvídání zbývající dobu životnosti (RUL) z letecké motory pomocí historických senzor hodnoty. Tento scénář používá [Keras](https://keras.io/) knihovna se [Tensorflow](https://www.tensorflow.org/) hloubkové learning framework jako výpočetní modul. Tento scénář soupravy LSTM s jednu sadu moduly a testy sítě na sadu neviditelný modul.

## <a name="prerequisites"></a>Požadavky
- [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
- Azure Machine Learning Workbench, s vytvořen pracovní prostor.
- Pro model operationalization: Azure Machine Learning Operationalization s prostředím místní nasazení nastavení a [účet Azure Machine Learning Model správy](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:

1. Otevřete aplikaci Machine Learning Workbench.
2. Na **projekty** vyberte **+** a potom vyberte **nový projekt**.
3. V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt.
4. V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Prediktivní Údržba", vyberte **hloubkové Learning pro scénáři prediktivní údržby** šablony.
5. Vyberte **Vytvořit**.

## <a name="prepare-the-notebook-server-computation-target"></a>Příprava cílového výpočetní server poznámkového bloku

Ke spuštění na místním počítači, z nástroje Machine Learning Workbench **soubor** nabídce vyberte buď **spusťte příkazový řádek** nebo **otevřete prostředí PowerShell CLI**. Rozhraní CLI umožňuje přístup k vašim službám Azure pomocí `az` příkazy. Nejdřív přihlaste k účtu Azure pomocí příkazu:

```
az login
``` 

Tento příkaz poskytuje ověřovací klíč pro použití s https:\\aka.ms\devicelogin adresy URL. Rozhraní příkazového řádku počká, až operace přihlášení prostřednictvím zařízení vrátí, poskytuje některé informace o připojení. Další, pokud máte místní [Docker](https://www.docker.com/get-docker) instalace připravit místní výpočetní prostředí pomocí příkazu:

```
az ml experiment prepare --target docker --run-configuration docker
```

Je vhodnější ke spuštění na [datové vědy virtuálního počítače (DSVM) pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) pro požadavky na paměť a disku. Po dokončení konfigurace DSVM Příprava vzdáleného prostředí Docker s následující dva příkazy:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po připojení k vzdálené kontejner Docker, Příprava DSVM Docker výpočetní prostředí pomocí příkazu: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

S Docker výpočetním prostředí připravené, otevřete server poznámkového bloku Jupyter z nástroje Machine Learning Workbench **poznámkových bloků** kartě nebo spuštění webového serveru pomocí příkazu: 

```
az ml notebook start
```

Příklad poznámkových bloků jsou uložené v adresáři kódu. Poznámkových bloků se nastavit tak, aby spouští sekvenčně, spouštění v poznámkovém bloku první (Code\1_data_ingestion.ipynb). Při otevření jednotlivých poznámkových bloků, se zobrazí výzva k výběru výpočetní jádra. Zvolte jádra _Template [Connection_Name] [název_projektu] ke spouštění na dříve nakonfigurované DSVM.

## <a name="data-description"></a>Popis dat

Šablona používá tři datových sad jako vstupy v souborech PM_train.txt, PM_test.txt a PM_truth.txt. 

- **Cvičení data**: letadla modul data spustit selhání. Data train (PM_train.txt) se skládá z několika, multivariační časové řady s *cyklus* jako časovou jednotku. Obsahuje 21 odečty snímačů pro každý cyklus. 

    - Každý časové řady se generují z jiný modul stejného typu. Každý motor začíná různých stupňů počáteční opotřebení a některé jedinečné výrobní variace. Tyto informace Neznámý uživateli. 

    - V těchto datech simulované modul se předpokládá, že na začátku každé časové řady pracovat normálně. Začne snižovat v určitém okamžiku během řadu provozní cykly. Snížení hodnoty a zvětšování v rozsahem. 

    - Když je dosaženo předdefinované prahovou hodnotu, modul považovány za nebezpečné pro další operace. Posledního cyklu každého časové řady je bod selhání tohoto modulu.

- **Testovacích dat**: letadla modul provozních dat bez zaznamenaných událostí selhání. Testovací data (PM_test.txt) mají stejné schéma dat, jako jsou Cvičná data. Jediným rozdílem je, že data neindikuje, když dojde k selhání (poslední časové období nemá *není* představují bodem selhání). Není znám, kolik více cyklů tento modul může poslední předtím, než se nezdaří.

- **Data pravdivosti**: true zbývající informace cyklů pro každý modul v testovacích datech. Data pravdivosti základů poskytuje počet cyklů zbývající práce pro moduly v testovacích datech.

## <a name="scenario-structure"></a>Struktura scénář

Pracovní postup scénář je rozdělené do tří kroky a každý krok se spouštějí v poznámkového bloku Jupyter. Každý poznámkového bloku vytvoří artefaktů dat, které jsou nastavené jako trvalé místně pro použití v poznámkových bloků.

### <a name="task-1-data-ingestion-and-preparation"></a>Úloha 1: Přijímání dat a příprava

Blok Jupyter přijímání dat v Code/1_data_ingestion_and_preparation.ipnyb načte tři vstupních datových sad do formátu Pandas DataFrame. Poznámkového bloku pak připraví data pro modelování a nemá některé vizualizace předběžné data. Datové sady jsou uloženy místně na výpočetní kontext pro použití v poznámkového bloku Jupyter vytváření modelu.

### <a name="task-2-model-building-and-evaluation"></a>Úloha 2: Vytváření modelů a vyhodnocení

Blok Jupyter sestavení modelu v Code/2_model_building_and_evaluation.ipnyb čte train a testovací sady dat z disku a vytvoří síť LSTM pro datovou sadu školení. Výkon modelu se měří v testovací datové sady. Výsledný model je serializované a uložená v kontextu místního výpočetní pro použití v úloze operationalization.

### <a name="task-3-operationalization"></a>Úloha 3: Operationalization

Poznámkový blok Jupyter Operationalization v Code/3_operationalization.ipnyb používá uložené model vytvářet funkce a schéma pro volání modelu v Azure hostovaná webové služby. Poznámkového bloku testuje funkce a pak do souboru LSTM_o16n.zip komprimaci prostředky. Soubor je načten do vašeho kontejneru úložiště Azure pro nasazení.

Soubor LSTM_o16n.zip nasazení obsahuje následující artefakty:

- **webservices_conda.yaml**: definuje balíčky Python, které jsou nutné ke spuštění modelu LSTM na cíl nasazení.  
- **service_schema.JSON**: definuje schéma dat, kterou je očekáván LSTM modelem.   
- **lstmscore.PY**: definuje funkce, které běží cíl nasazení skóre nová data.    
- **modellstm.JSON**: definuje LSTM architekturu. Funkce lstmscore.py přečíst architektuře a váhu k chybě při inicializaci modelu.
- **modellstm.H5**: definuje váhu modelu.
- **test_service.PY**: test skript, který volá nasazení koncový bod s testovací datových záznamů. 
- **PM_test_files.pkl**: skript test_service.py přečte modul historických dat ze souboru PM_test_files.pkl a odešle webovou službu dostatečný počet cyklů pro LSTM vrátit pravděpodobnost selhání modulu.

Poznámkového bloku testy funkce pomocí definice modelu předtím, než ho balíčky operationalization prostředky pro nasazení. Pokyny k instalaci a testování webové služby jsou zahrnuty na konci Code/3_operationalization.ipnyb poznámkového bloku.

## <a name="conclusion"></a>Závěr

V tomto kurzu poskytuje jednoduchého scénáře, který používá hodnoty čidel předpovědi. Pokročilejší scénáře prediktivní údržby, jako [prediktivní údržby modelování R Poznámkový blok průvodce](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) můžete použít mnoha zdrojů dat, jako jsou záznamy Historie údržby, protokoly chyb a funkce počítačů. Dalších zdrojů dat může vyžadovat různých způsobů pro použití s hloubkovým učení.


## <a name="references"></a>Odkazy

Následující odkazy poskytují příklady dalších prediktivní údržby případy použití pro různé platformy:

* [Šablona řešení prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Průvodce modelováním prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktivní údržby modelování příručce, která je pomocí služby SQL Server R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Prediktivní údržby modelování Poznámkový blok průvodce Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Prediktivní údržby pomocí PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Scénářích reálného světa prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance)

## <a name="next-steps"></a>Další postup

Další příklad scénáře jsou k dispozici v nástroji Machine Learning Workbench, které ukazují další funkce produktu. 
