---
title: Reálné scénáře prediktivní údržby | Dokumentace Microsoftu
description: Prediktivní Údržba pro scénářích reálného světa pomocí PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9c638ed9132612db7b82168d3a57057aba9b2d60
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870330"
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Reálné scénáře prediktivní údržby

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Neplánované prostoje zařízení můžou mít pro řadu firem vážné důsledky. Je důležité mít pole zařízení pro maximalizaci využití a výkonu a minimalizovat tak prostoje nákladná, neplánované. Včasné identifikaci problémů pomáhají přidělit prostředky omezené údržby v nákladově efektivní způsob a vylepšení kvality a zadat řetězec procesy. 

Tento scénář vám umožní prozkoumat relativně [rozsáhlé datové sady simulovaných](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) procesem s použitím projekt vědeckého zpracování dat prediktivní údržby od příjem dat, jsou vybavené technikům, vytváření modelů a operacionalizace modelu a nasazení. Kód pro celý proces je zapsán do poznámkového bloku Jupyter s využitím PySpark v aplikaci Azure Machine Learning Workbench. Nasazení finálního modelu pomocí správy modelů Azure Machine Learning k vytváření predikcí selhání vybavení v reálném čase.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Úložiště GitHub Galerie Cortana Intelligence

Cortana Intelligence Gallery PM kurzu je veřejného úložiště GitHub ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) můžete hlásit problémy a Přispívání.


## <a name="use-case-overview"></a>Přehled případu použití

Závažný problém, který je spojenou s rozšiřováním firem v oborech náročná na výkon prostředku je výrazné náklady, které jsou spojeny s zpoždění z důvodu potíží mechanických. Většina podniků zajímá předpověď při těchto problémů může dojít k proaktivnímu jim Předcházejte předtím, než k nim dojde. Cílem je snížení nákladů na zkrácením prostojů a možném zvětšení bezpečnosti. 

Tento scénář má nápady z [prediktivní údržby playbook](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) k sestavení prediktivního modelu pro simulované datové sady. Příklad data jsou odvozena z společné prvky, které jsou dodržovány v mnoha případech použití prediktivní údržby.

Pro tato Simulovaná data obchodního problému je možnost předvídat problémy, které jsou způsobeny chyb na úrovni součástí. Je obchodní otázku "*co je pravděpodobnost, že se počítač ocitne mimo provoz kvůli selhání součásti?*" Tento problém je formátován jako problém klasifikace víc tříd (více komponent na počítač). Algoritmu strojového učení se používá k vytvoření prediktivního modelu. Model se trénuje na historická data, která se shromažďují z počítačů. V tomto scénáři uživatel prochází různé kroky k implementaci modelu v prostředí Machine Learning Workbench.

## <a name="prerequisites"></a>Požadavky

* [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Postupujte podle [instalační příručky rychlý Start](quickstart-installation.md) k instalaci a vytvoření pracovního prostoru.
* Azure Machine Learning Operacionalizace vyžaduje prostředí pro místní nasazení a [účtu správy modelů Azure Machine Learning](model-management-overview.md).

Tento příklad se spouští na libovolný výpočetní kontext aplikace Machine Learning Workbench. Však doporučujeme spustit příklad s minimálně 16 GB paměti. Tento scénář se sestaví a otestují na počítač s Windows 10 spuštění vzdálené standard DS4_V2 [dat virtuálního počítače VĚDY pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Operacionalizace modelu bylo provedeno pomocí 0.1.0a22 verze rozhraní příkazového řádku Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvoření nového projektu s použitím v tomto příkladu jako šablony:
1.  Otevřete aplikaci Machine Learning Workbench.
2.  Na **projekty** stránce **+** a pak vyberte **nový projekt**.
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt.
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Prediktivní údržby" a vyberte **prediktivní údržby** šablony.
5.  Vyberte **Vytvořit**.

## <a name="prepare-the-notebook-server-computation-target"></a>Připravte cílové výpočetní server poznámkového bloku

Ke spuštění v místním počítači, aplikace Machine Learning Workbench **souboru** nabídku, vyberte buď **otevřít příkazový řádek** nebo **otevřete rozhraní příkazového řádku Powershellu**. Toto rozhraní příkazového řádku umožňuje přístup pomocí služby Azure `az` příkazy. Nejdřív přihlaste ke svému účtu Azure pomocí příkazu:

```
az login
``` 

Tento příkaz zjistí ověřovací klíč pro použití s https:\\aka.ms\devicelogin adresy URL. Rozhraní příkazového řádku počká, až operace přihlášení zařízení vrátí a poskytuje některé informace o připojení. Další, pokud máte místní [Docker](https://www.docker.com/get-docker) instalace, připravte místní výpočetní prostředí pomocí příkazu:

```
az ml experiment prepare --target docker --run-configuration docker
```

Doporučuje se spouštět [DSVM pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) pro požadavky na paměť a disk. Po dokončení konfigurace datové VĚDY, připravte se na vzdáleném prostředí Dockeru pomocí následujících dvou příkazů:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po připojení ke vzdálené kontejneru Dockeru, připravte prostředí compute DSVM Dockeru pomocí příkazu: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Výpočetní prostředí Docker připravený, otevřete server poznámkového bloku Jupyter aplikace Azure Machine Learning Workbench **poznámkových bloků** kartu, nebo spusťte server založené na prohlížeči pomocí příkazu: 

```
az ml notebook start
```

Příklad poznámkové bloky jsou uložené v adresáři kódu. Poznámkových bloků nastavení jsou spuštěna sekvenčně, od prvního poznámkového bloku (Code\1_data_ingestion.ipynb). Když otevřete každý Poznámkový blok, budete vyzváni k výběru výpočetní jádro. Zvolte jádra _Template [Connection_Name] [Project_Name] se promítnou u dříve nakonfigurované datové VĚDY.

## <a name="data-description"></a>Popis dat

[Simulovaná data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide) se skládá z následujících [pět oddělených čárkou (CSV) soubory hodnoty](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data):

* [Počítače](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/machines.csv): funkce, které rozlišit každý počítač, jako je stáří a modelu.
* [Chyby](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/errors.csv): V protokolu chyb obsahuje chyby pevné, které jsou vyvolány, když je počítač je stále v provozu. Tyto chyby nejsou považované za chyby, i když je možné prediktivní selhání budoucí události. Hodnoty datum a čas pro chyby jsou zaokrouhleny na nejbližší hodinu od telemetrická data se shromažďují hodinové sazby.
* [Údržba](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/maint.csv): Protokol údržby obsahuje oba plánované a neplánované údržbě záznamy. Plánovaná údržba odpovídá pravidelných kontrol komponenty. Neplánované údržby mohou vyplývat z mechanickým selhání nebo jiné snížení výkonu. Hodnoty datum a čas údržby jsou zaokrouhleny na nejbližší hodinu od telemetrická data se shromažďují hodinové sazby.
* [Telemetrie](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/telemetry.csv): telemetrická data se skládá z řady měření času z řadu senzorů v každém počítači. Data se protokolují podle průměrné hodnoty čidel za každou hodinu interval.
* [Selhání](https://github.com/Microsoft/SQL-Server-R-Services-Samples/blob/master/PredictiveMaintanenceModelingGuide/Data/failures.csv): selhání odpovídají nahrazení součást v protokolu údržby. Každý záznam obsahuje ID počítače, typ součásti a nahrazení datum a čas. Tyto záznamy se používají k vytvoření strojového učení popisky, které model se pokouší předvídat.

Stažení sady nezpracovaných dat z úložiště GitHub a vytvoření datové sady PySpark pro tuto analýzu najdete v tématu [Ingestování](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) scénář Poznámkový blok Jupyter v složky s kódem.

## <a name="scenario-structure"></a>Struktura scénář
Obsah pro tento scénář je k dispozici na [úložiště GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) soubor popisuje pracovní postup ze přípravě dat, vytváření modelu a poté Nasaďte řešení pro produkční prostředí. Každý krok pracovního postupu, je zapouzdřena v Poznámkový blok Jupyter v [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) složky v rámci tohoto úložiště.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): pět souborů .csv vstupní stáhne tento poznámkový blok a provádí některé předběžné čištění a vizualizace. Poznámkový blok převede každou sadu dat na formát PySpark a uloží je v kontejneru objektů blob v Azure pro použití v technických poznámkového bloku funkce.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): funkce modelu se vytvářejí na základě sady nezpracovaných dat z úložiště objektů Blob v Azure pomocí řady přístupu (běžný čas) pro data telemetrie, chyby a údržby. Nahrazení týkající se selhání součásti se používají k vytvoření modelu popisky, které popisují, jaké součásti se nezdařilo. Data s popiskem funkce se uloží do objektu blob Azure Poznámkový blok jupyter vytváření modelu.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): The Poznámkový blok vytváření modelu používá sadu dat označených funkce a rozdělí data do datové sady trénování a vývoj podél razítka data a času. Poznámkový blok je nastavený jako experiment s pyspark.ml.classification modely. Je vektorizovaná trénovací data. Uživatel můžete experimentovat s buď **DecisionTreeClassifier** nebo **RandomForestClassifier** k manipulaci s hyperparameters najít nejvýkonnějšího modelu. Výkon je určen vyhodnocování statistik měření v datové sadě vývoj. Ve statistikách přihlášeni zpět na obrazovku aplikace Machine Learning Workbench modulu runtime pro sledování. Při každém spuštění poznámkového bloku uloží výsledný model na místní disk, na kterém běží jádra aplikace Jupyter notebook. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): poslední model, který je uložen na místní disk (jádra aplikace Jupyter notebook) k vytváření komponent pro model nasazení do služby Azure web používá tento poznámkový blok. Plně funkční prostředky jsou zkomprimována do o16n.zip soubor, který je uložen v jiném kontejneru objektů blob v Azure. Soubor ZIP obsahuje:

* **service_schema.JSON**: soubor definice schématu pro nasazení. 
* **pdmscore.PY**: **init()** a **run()** funkce, které jsou vyžadované Azure webovou službu.
* **pdmrfull.model**: adresář definice modelu.
    
Poznámkový blok testuje funkce s definicí model než připravíte balíček operacionalizace prostředky pro nasazení. Pokyny pro nasazení jsou zahrnuty na konci poznámkového bloku.

## <a name="conclusion"></a>Závěr

Tento scénář s přehledem sestavení řešení prediktivní údržby začátku do konce pomocí PySpark prostředí Poznámkový blok Jupyter v aplikaci Machine Learning Workbench. Tento příklad scénáře také podrobně popisuje model nasazení pomocí prostředí správy modelů Machine Learning k vytváření predikcí selhání vybavení v reálném čase.

## <a name="references"></a>Odkazy

Příklady dalších prediktivní údržby případy použití pro různé platformy poskytují následující odkazy:

* [Šablona řešení prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Průvodce modelováním prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktivní Údržba modelování příručka, která je pomocí služby SQL Server R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Prediktivní Údržba modelování Poznámkový blok Python Průvodce](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Prediktivní údržby pomocí PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Hloubkové učení pro prediktivní údržbu](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Další postup

Další příklad scénáře jsou k dispozici v aplikaci Machine Learning Workbench, které popisují další funkce produktu. 
