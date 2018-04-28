---
title: Prediktivní údržby pro scénářích reálného světa | Microsoft Docs
description: Prediktivní údržby pro scénářích reálného světa pomocí PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 791c34785fa817fd68d0bec8111bf23e606c9b64
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Prediktivní údržby pro scénářích reálného světa

Dopad výpadek neplánovanou zařízení může být škodlivé pro všechny firmy. Je důležité zachovat pole zařízení s chcete maximalizovat využití a výkonu a chcete-li minimalizovat nákladná, neplánované výpadky. Časná identifikaci problémů může pomoci přidělení prostředků omezené údržby v nákladově efektivní způsob, jak a vylepšení kvality a zadat řetězec procesy. 

Jsou zde popsány v tomto scénáři relativně [rozsáhlých datových sad simulované](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) vás provede projektu vědecké účely prediktivní údržby dat z přijímání dat funkci analýzy, vytváření modelů a operationalization modelu a nasazení. Kód pro celý proces je zapsán do poznámkového bloku Jupyter s použitím PySpark v nástroji Azure Machine Learning Workbench. Nasazení konečné modelu provádět předpovědi selhání zařízení v reálném čase pomocí Azure Machine Learning modelu správy.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Úložiště Cortana Intelligence Galerie GitHub

Cortana Intelligence Gallery kurzu PM je veřejný úložiště GitHub ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)) kde hlášení problémů a příspěvky.


## <a name="use-case-overview"></a>Přehled případu použití

Hlavní problém, který je, jimž firmy v odvětví asset náročné je výrazné náklady, které jsou přidružené zpoždění kvůli problémům s mechanických. Většina podniků zajímá predikci při tyto problémy mohou se vyskytnout aby proaktivně je předtím, než k nim dojde. Cílem je snížení nákladů snížením výpadky a možném zvětšení zabezpečení. 

Tento scénář trvá nápady z [playbook prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) abychom ukázali, jak vytvořit prediktivní model pro simulované datové sady. Příklad dat je odvozená od společné prvky, které je možné vysledovat v řadě případů použití prediktivní údržby.

Obchodního problému pro toto simulované dat je k předvídání problémy, které jsou způsobeny selhání komponent. Otázka pro firmy je "*co je pravděpodobnost, že je počítač ocitne mimo provoz z důvodu selhání součásti?*" Tento problém je naformátován jako více třída klasifikace problému (více komponent na počítač). Algoritmu strojového učení se používá k vytvoření prediktivního modelu. Cvičení modelu na historických datech, které jsou shromážděny z počítačů. V tomto scénáři uživatel prochází různé kroky pro implementaci modelu v prostředí nástroje Machine Learning Workbench.

## <a name="prerequisites"></a>Požadavky

* [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Postupujte podle [rychlé spuštění Průvodce instalací](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor.
* Azure Machine Learning Operationalization vyžaduje prostředí pro místní nasazení a [účet Azure Machine Learning Model správy](model-management-overview.md).

V tomto příkladu se spouští na jakýkoli kontext výpočetní Machine Learning Workbench. Má však doporučujeme spustit v příkladu s minimálně 16 GB paměti. V tomto scénáři byl vytvořen a otestovali na počítač s Windows 10 běží vzdálené standard DS4_V2 [datové vědy virtuálního počítače (DSVM) pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

Model operationalization bylo provedeno pomocí verze 0.1.0a22 z rozhraní příkazového řádku Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete aplikaci Machine Learning Workbench.
2.  Na **projekty** vyberte **+** a potom vyberte **nový projekt**.
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt.
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Prediktivní Údržba", vyberte **prediktivní údržby** šablony.
5.  Vyberte **Vytvořit**.

## <a name="prepare-the-notebook-server-computation-target"></a>Příprava cílového výpočetní server poznámkového bloku

Ke spuštění na místním počítači, z nástroje Machine Learning Workbench **soubor** nabídce vyberte buď **spusťte příkazový řádek** nebo **otevřete prostředí PowerShell CLI**. Rozhraní CLI umožňuje přístup k vašim službám Azure pomocí `az` příkazy. Nejdřív přihlaste k účtu Azure pomocí příkazu:

```
az login
``` 

Tento příkaz poskytuje ověřovací klíč pro použití s https:\\aka.ms\devicelogin adresy URL. Rozhraní příkazového řádku počká, až operace přihlášení prostřednictvím zařízení vrátí, poskytuje některé informace o připojení. Další, pokud máte místní [Docker](https://www.docker.com/get-docker) instalace připravit místní výpočetní prostředí pomocí příkazu:

```
az ml experiment prepare --target docker --run-configuration docker
```

Je vhodnější ke spuštění na [DSVM pro Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) pro požadavky na paměť a disku. Po dokončení konfigurace DSVM Příprava vzdáleného prostředí Docker s následující dva příkazy:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Po připojení k vzdálené kontejner Docker, Příprava DSVM Docker výpočetní prostředí pomocí příkazu: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

S Docker výpočetním prostředí připravené, otevřete server poznámkového bloku Jupyter z nástroje Azure Machine Learning Workbench **poznámkových bloků** kartě nebo spuštění webového serveru pomocí příkazu: 

```
az ml notebook start
```

Příklad poznámkových bloků jsou uložené v adresáři kódu. Poznámkových bloků se nastavit tak, aby spouští sekvenčně, spouštění v poznámkovém bloku první (Code\1_data_ingestion.ipynb). Při otevření jednotlivých poznámkových bloků, se zobrazí výzva k výběru výpočetní jádra. Zvolte jádra _Template [Connection_Name] [název_projektu] ke spouštění na dříve nakonfigurované DSVM.

## <a name="data-description"></a>Popis dat

[Simulated data](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) se skládá z pěti hodnot oddělených čárkami (.csv) soubory. Pomocí následujících odkazů získáte podrobný popis o datových sad.

* [Počítače](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): funkce, které odlišit od každého počítače, jako je stáří a modelu.
* [Chyba](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): V protokolu chyb obsahuje pevné chyby, které jsou vyvolány, když je počítač je stále v provozu. Tyto chyby nejsou považovány za selhání, přestože se můžou být prediktivní budoucí selhání události. Hodnoty data a času pro chyby jsou zaokrouhleny na nejbližší hodiny vzhledem k tomu, že telemetrická data jsou shromažďována hodinové tempem.
* [Údržby](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): Protokol údržby obsahuje oba záznamy plánovaných a neplánovaných údržby. Plánované údržby odpovídá pravidelné kontroly součástí. Neplánovanou údržbu mohou vyplývat z mechanických selhání nebo jiných snížení výkonu. Hodnoty data a času údržby jsou zaokrouhleny na nejbližší hodiny vzhledem k tomu, že telemetrická data jsou shromažďována hodinové tempem.
* [Telemetrie](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): telemetrická data se skládá z řady měření času z více senzory v každém počítači. Data se zaznamená jako průměr hodnoty čidel za každou hodinu interval.
* [Selhání](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): selhání odpovídají náhrady součásti v rámci protokolu údržby. Každý záznam obsahuje ID počítače, typ součásti a nahrazení datum a čas. Tyto záznamy se používají k vytvoření strojového učení štítků, které se pokouší o předpovědi modelu.

Stažení sady nezpracovaných dat z úložiště Githubu a vytvořit PySpark datových sad u této analýzy, najdete v článku [přijímání dat](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Poznámkový blok Jupyter scénář ve složce kódu.

## <a name="scenario-structure"></a>Struktura scénář
Obsah pro tento scénář je k dispozici na [úložiště GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) soubor popisuje od přípravy dat, vytváření modelu a poté nasazení řešení pro produkční prostředí. Každý krok pracovního postupu je zapouzdřené v poznámkového bloku Jupyter v [kód](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) složku v rámci úložiště.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Tento poznámkový blok stáhne pět vstupní .csv a nemá některé čištění předběžné dat a vizualizaci. Poznámkového bloku převede každé datové sady na formát PySpark a ukládá je v kontejneru objektů blob v Azure pro použití v poznámkovém bloku Engineering funkce.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): funkce modelu se vytvářejí na základě nezpracované datových sad z úložiště objektů Blob v Azure pomocí řady přístup (běžný čas) pro data telemetrie, chyb a údržby. Náhrady související selhání součásti jsou používány k vytváření modelu štítků, které popisují, které součásti došlo k chybě. Data s popiskem funkce se uloží do objektu blob Azure poznámkového bloku vytváření modelu.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): modelu Poznámkový blok vytváření používá sada dat s popiskem funkce a rozdělí data do vlaku a vývojáře sady dat společně razítka data a času. Poznámkový blok je nastavený jako experimentu s pyspark.ml.classification modely. Je vectorized Cvičná data. Uživatele můžete experimentovat s buď **DecisionTreeClassifier** nebo **RandomForestClassifier** k manipulaci s hyperparameters najít nejvýkonnější modelu. Výkon je určen vyhodnocení statistiky měření na dev datové sady. Ve statistikách přihlášeni zpět na obrazovku Machine Learning Workbench modulu runtime pro sledování. Při každém spuštění se uloží poznámkového bloku výsledný model na místní disk, na kterém běží jádra poznámkového bloku Jupyter. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): Tento poznámkový blok používá poslední model, který je uložen na místní disk (jádro poznámkového bloku Jupyter) k vytvoření komponenty pro nasazení modelu do Azure webové služby. Úplné provozní prostředky jsou zkomprimovanou do o16n.zip souboru, který je uložený v jiném kontejneru objektů blob v Azure. Soubor ZIP obsahuje:

* **service_schema.JSON**: soubor definice schématu pro nasazení. 
* **pdmscore.PY**: **init()** a **run()** funkce, které jsou vyžadované Azure webové služby.
* **pdmrfull.model**: adresář definice modelu.
    
Poznámkového bloku testy funkce s definicí modelu před zabalení operationalization prostředky pro nasazení. Pokyny pro nasazení jsou zahrnuty na konci poznámkového bloku.

## <a name="conclusion"></a>Závěr

Tento scénář nabízí přehled vytváření řešení prediktivní údržby začátku do konce pomocí PySpark prostředí Poznámkový blok Jupyter v nástroji Machine Learning Workbench. Tento příklad také podrobné informace o modelu nasazení pomocí prostředí správy Model Machine Learning tak, aby provádět předpovědi selhání zařízení v reálném čase.

## <a name="references"></a>Odkazy

Následující odkazy poskytují příklady dalších prediktivní údržby případy použití pro různé platformy:

* [Šablona řešení prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Průvodce modelováním prediktivní údržby](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Prediktivní údržby modelování příručce, která je pomocí služby SQL Server R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Prediktivní údržby modelování Poznámkový blok průvodce Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Prediktivní údržby pomocí PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Přímý učení pro prediktivní údržby](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Další postup

Další příklad scénáře jsou k dispozici v nástroji Machine Learning Workbench, které ukazují další funkce produktu. 
