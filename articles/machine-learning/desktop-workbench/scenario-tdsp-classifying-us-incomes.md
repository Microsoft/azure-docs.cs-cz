---
title: Klasifikace příjem - Team Data vědecké účely proces – Azure Machine Learning | Microsoft Docs
description: Jak používat šablony Team datové vědy procesu k vytvoření projektu v Azure Machine Learning, který klasifikuje příjmů USA.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 48c88f541f650fac3bdec431f3164138fb0f3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Klasifikace příjem s projektem tým datové vědy procesu (TDSP)

## <a name="introduction"></a>Úvod

Standardizace struktury a vědecké zpracování dat naleznete v dokumentaci projekty, který je ukotven zavedených [životního cyklu vědecké účely data](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), je klíčová pro usnadnění efektivní spolupráce v datové vědy týmy. Vytváření projektů Azure Machine Learning s [tým datové vědy procesu (TDSP)](https://github.com/Azure/Microsoft-TDSP) šablona poskytuje rozhraní pro takové standardizace.

Jsme dřív vydané [úložiště GitHub pro strukturu TDSP projektů a šablon](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ale nebylo možné, dokud teď k vytváření instancí TDSP strukturu a šablon v rámci nástroje vědecké účely data. Nyní jsme povolili vytváření projektů Azure Machine Learning, které jsou vytvářeny instance s [TDSP strukturu a dokumentaci šablon pro Azure Machine Learning](https://github.com/amlsamples/tdsp). Poskytuje pokyny, jak používat TDSP struktura a šablon v Azure Machine Learning [zde](https://aka.ms/how-to-use-tdsp-in-aml). Zde jsou příklady jak projektu skutečné machine learning lze vytvořit pomocí TDSP struktura, naplní kódu pro konkrétní projekt, artefaktů a dokumenty a spustit v Azure Machine Learning.

## <a name="link-to-github-repository"></a>Propojit s úložišti GitHub
Poskytujeme souhrn dokumentace [sem](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) o vzorku. Rozsáhlejší dokumentace naleznete na webu GitHub.

### <a name="purpose"></a>Účel
Primárním účelem Tato ukázka je ukazují, jak vytvořit instanci a provedení machine learning projekt pomocí [tým datové vědy procesu (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktura a šablon v nástroji Azure Machine Learning. Pro tento účel využijeme dobře známé [roce 1994 za úplné zjišťování nám data z úložiště UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/adult). Tato úloha modelování je k předvídání USA roční příjem třídy z informací nám sčítání (například stáří, soupeření, education úroveň, země původu, atd.)

### <a name="scope"></a>Rozsah
 * Zkoumání dat, školení a nasazení modelu strojového učení, které adres předpovědi problém popsaný v části Přehled případ použití. 
 * Spuštění projektu v Azure Machine Learning pomocí šablony tým datové vědy procesu (TDSP) z Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav vytvoříme použít TDSP životního cyklu.
 * Operationalization řešení přímo z Azure Machine Learning v kontejneru služby Azure.

 Projekt označuje některé funkce služby Azure Machine Learning, takové TDSP struktura vytváření instancí a použití, spouštění kódu v poznámkové bloky Jupyter a také soubory Python a snadno operationalization v kontejneru služby Azure pomocí Docker a Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Životní cyklus tým datové vědy procesu (TDSP)
V tématu [Team životního cyklu procesu (TDSP) Data vědecké účely](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Požadavky
### <a name="required-subscription-hardware-software"></a>Požadováno: předplatné, hardwaru, softwaru
1. Azure [předplatné](https://azure.microsoft.com). Můžete získat [bez předplatného](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) k provedení této ukázce také.
2. [Azure datové vědy virtuálního počítače (DSVM) systému Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (velikost virtuálního počítače: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), s 4 virtuální procesory a 14 Gb paměti RAM). I když otestovali na DSVM Azure, bude pravděpodobně pracovat z jakéhokoli počítače Windows 10.
3. Přečtěte si dokumentaci k Azure Machine Learning a jeho souvisejících služeb (dole najdete odkazy).
4. Ujistěte se, že jste nainstalovali správně Azure Machine Learning pomocí [Průvodce instalací úvodní](../service/quickstart-installation.md).

Datová sada pro tato ukázka je z úložiště ML UCI [[odkaz]](https://archive.ics.uci.edu/ml/datasets/adult). To je převzat z databáze úplné zjišťování nám 1994 a obsahuje informace o úplné zjišťování a příjem pro jednotlivce přibližně 50 000. Toto je strukturované datové sady s číselné a kategorií funkce a kategorií cíl, který se skládá ze dvou kategorií příjem ('> 50 tisíc ' nebo ' < = 50 tisíc '). 

### <a name="optional-version-control-repository"></a>Volitelné: Úložiště správy verzí
Pokud chcete uložit a verze projektu a její obsah, je potřeba mít ovládací prvek úložiště verze kde to lze provést. Při vytváření nového projektu pomocí šablony TDSP v Azure Machine Learning můžete zadat umístění úložiště Git. V tématu [použití Git v Azure Machine Learning](using-git-ml-project.md) další podrobnosti.

### <a name="informational-about-azure-machine-learning"></a>Informační: O Azure Machine Learning
* [Časté otázky – Začínáme](frequently-asked-questions.md)
* [Přehled](../service/overview-what-is-azure-ml.md)
* [Instalace](../service/quickstart-installation.md)
* [Provádění](experimentation-service-configuration.md)
* [Pomocí TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Číst a zapisovat soubory](how-to-read-write-files.md)
* [Použití Gitu se službou Azure Machine Learning](using-git-ml-project.md)
* [Nasazení modelu ML jako webovou službu](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Klasifikovat USA příjmy – projekt TDSP" a vyberte šablonu
5.  Klikněte na **Vytvořit**

Pokud zadáte na prázdný umístění úložiště Git během vytváření projektu (do příslušného pole), bude možné tohoto úložiště vyplňovat projektu strukturu a obsah po vytvoření projektu.

## <a name="use-case-overview"></a>Přehled případu použití
Je ale problém pochopit, jak socioekonomickými data zaznamenaná nám sčítání může pomoci odhadnout roční příjem jednotlivce v USA. Podle toho, funkce, úplné zjišťování, úlohu machine learning je k předvídání, pokud příjem jednotlivce je vyšší než 50 000 $, nebo ne (binární klasifikace úlohy).

## <a name="data-description"></a>Popis dat
Podrobné informace o datech najdete v tématu [popis](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) v úložišti UCI. 

Tato data byla extrahovaných z databáze úplné zjišťování kancelář nachází zde: https://www.census.gov/en.html. 


* Je celkem 48,842 instancí (před verzí jakéhokoli filtrování), kombinovat souvislé a diskrétní (cvičení = 32, 561, testovací = 16, 281)
* Pravděpodobnosti pro popisek ' > 50 tisíc ': 23.93 % / 24.78 % (bez neznámé prvky)
* Pravděpodobnost na štítek, ' < = 50 tisíc ': 76.07 % / 75.22 % (bez neznámé prvky)  

* **Cíl**: Třída příjem ' > 50 tisíc ', ' < = 50 tisíc '. Tyto nahrazují 1 a 0 ve fázi přípravy data.
* **FUNKCE**: stáří, třída pracovní, education úroveň, education úroveň, soupeření, pohlaví, hodin práce za týden, atd.


## <a name="project-structure-execution-and-reporting"></a>Struktura projektu, spouštění a vytváření sestav

### <a name="structure"></a>Struktura
Pro tento projekt používáme TDSP složky struktura a dokumentace šablony (dole), které způsobem [životního cyklu TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Vytvoření projektu podle pokynů [zde](https://aka.ms/how-to-use-tdsp-in-aml). Po, naplní se kód projektu a artefaktů, struktura vypadá takto (viz struktura projektu do pole červeně obrázek).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Spouštěcí
V tomto příkladu jsme spouštění kódu v **místní výpočetním prostředí**. Azure Machine Learning dokumenty, kde najdete další podrobnosti naleznete v [možnosti provedení](experimentation-service-configuration.md).

Provádění skriptu jazyka Python v místní modul Python runtime je snadné:

    az ml experiment submit -c local my_script.py

Soubory poznámkového bloku IPython dají neotevřou ze struktury projekt na levé straně rozhraní Azure Machine Learning a spouštět na serveru Jypyter poznámkového bloku.


Pracovní postup vědecké účely podrobné data byla následujícím způsobem:

* [**Získávání dat a principy**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Data byla stažena ve formátu .csv z adresy URL v úložišti ML UCI [[odkaz]](https://archive.ics.uci.edu/ml/datasets/adult). Funkce, cíl a jejich transformace jsou podrobně popsané v souboru ProjectReport.md.

Kód pro získávání dat a principy se nachází v: / kód/01_data_acquisition_and_understanding.

Zkoumání dat se provádí pomocí Python 3 [IDEAR (interaktivní zkoumání dat a vytváření sestav) nástroj](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) publikované jako součást [TDSP sada nástrojů, vědecké účely data](https://github.com/Azure/Azure-TDSP-Utilities). Tento program pomáhá generovat zkoumání standardizované dat pro data obsahující číselné a kategorií funkce a cíle. Podrobnosti o použití nástrojů Python 3 IDEAR najdete níže. 

Umístění sestavy zkoumání konečné dat je [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Zobrazení sestavy IDEAR je zobrazena níže:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modelování**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Jsme vytvořili dva modely s 3-fold křížové ověření: elastické Net a náhodných doménové struktury. Použili jsme [59 bodu vzorkování](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) pro vyhledávání náhodné mřížky jako strategie pro optimalizaci parametr křížové ověření a modelu. Přesnost modelů byly měří pomocí AUC (oblast v části křivky) na testovací datové sady. 

Kód pro modelování se nachází v: / kód/02_modeling.

AUC elastické Net a modely náhodných doménové struktury byly > 0.85. V souborech pickled.pkl a výstup, který ROC ukazuje zeměpisný pro oba modely uložíme obou modelů. AUC z náhodné model doménové struktury bylo 0.92 a který elastické Net modelu 0,90. Kromě toho pro model interpretace důležitosti funkce pro model doménové struktury náhodných jsou výstup do souboru CSV a vykreslena pdf (pouze funkce prediktivní prvních 20 počítačů).

Křivka ROC **model doménové struktury náhodných** na testovací data jsou uvedeny níže. To se model, který byl nasazen:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Model doménové struktury náhodných funkce význam (prvních 20) jsou uvedeny níže. Zobrazuje nárůst velké množství funkcí, eduction, rodinném stavu, mít nejvyšší důležitosti funkce.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**nasazení**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Model doménové struktury náhodných jsme nasadili jako webovou službu na cluster [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Prostředí operationalization zřídí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Můžete najít další informace o procesu operationalization [zde](model-management-service-deploy.md). 

Kód pro nasazení se nachází v: / kód/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Sestava konečné projektu](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Podrobnosti o každém z výše uvedených částech jsou uvedeny ve zprávě zkompilovaný projekt pro konečné [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Projekt sestava také obsahuje další podrobnosti o případ použití, metriky výkonu modelu, nasazení a infrastruktury, na kterém byl vyvinutý a nasazení projektu.

Sestava projektu, společně s obsah složky celý projekt a verze, úložiště správy se může doručit do klienta.


## <a name="conclusion"></a>Závěr

V této ukázce jsme vám ukázal, teď pro použití TDSP strukturu a šablon v Azure Machine Learning. Prostřednictvím šablon dokumentů a artefaktů můžete:
1. Správně definovat účel a rozsah projektu
2. Vytvořte projektový tým s distribuované role a zodpovědnosti
3. Struktury a provedení projekty podle fáze životního cyklu TDSP
4. Vývoj standardizovaných sestav pomocí nástroje vědecké účely TDSP data (například IDEAR data zkoumání a vizualizace sestavu).
5. Příprava sestavu projektu vědecké účely poslední data, která se dá doručit do klienta

Věříme, že používáte tuto funkci Azure Machine Learning k usnadnění s standardizace a spolupráce v rámci týmům vědecké účely data.

## <a name="next-steps"></a>Další kroky

Odkazy dole najdete:

[Jak používat tým datové vědy procesu (TDSP) v Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Proces vědecké účely dat Team (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Šablona projektu TDSP pro Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[USA příjem datové sady z úložiště UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)