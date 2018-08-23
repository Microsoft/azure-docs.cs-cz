---
title: Klasifikace příjmů - vědecké zpracování týmových dat – Azure Machine Learning | Dokumentace Microsoftu
description: Jak použít šablonu vědecké zpracování týmových dat k vytvoření projektu ve službě Azure Machine Learning, která klasifikuje příjmů v USA.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: edc3fc5e2a625a14bcb48b03f32cd99069a0ad53
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054877"
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Klasifikace příjmů s projektem vědecké zpracování týmových dat (TDSP)

## <a name="introduction"></a>Úvod

Normalizaci struktury a dokumentace pro datovou vědu projekty, který je ukotven zavedené [životního cyklu datové vědy](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), je klíčem k umožňuje efektivní spolupráci v týmech datové vědy. Vytváření projektů Azure Machine Learning s [vědecké zpracování týmových dat (TDSP)](https://github.com/Azure/Microsoft-TDSP) šablony poskytuje rozhraní pro takové standardizaci.

Jsme už vydali [úložiště GitHub pro šablony a struktura projektu TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ale nebylo možné, dokud se teď pro vytvoření instance struktury TDSP a šablon v rámci nástroje pro datové vědy. Zavedli jsme teď vytváření projektů Azure Machine Learning, které jsou vytvořeny pomocí [TDSP strukturu a dokumentace ke službě šablon pro Azure Machine Learning](https://github.com/amlsamples/tdsp). Pokyny týkající se použití TDSP strukturu a šablon ve službě Azure Machine Learning [tady](https://aka.ms/how-to-use-tdsp-in-aml). Tady vám nabízíme příkladem jak projektu aplikace skutečný machine learning se dají vytvářet pomocí TDSP struktury, vyplní specifické pro projekt kódu, artefakty a dokumenty a spustit v Azure Machine Learning.

## <a name="link-to-github-repository"></a>Propojit úložiště GitHub
Poskytujeme souhrnu dokumentaci [tady](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) týkající se ukázky. Více si rozsáhlou dokumentaci k najdete na webu GitHub.

### <a name="purpose"></a>Účel
Je primárním účelem této ukázky ukazují, jak vytvořit instanci a provádění strojového učení pomocí projektu [vědecké zpracování týmových dat (TDSP)](https://github.com/Azure/Microsoft-TDSP) strukturu a šablon v Azure Machine Learning. V tomto případě používáme dobře známé [1994 nám sčítání data z úložiště UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/adult). Modelování úkolu je možnost předvídat USA roční příjem tříd z nás sčítání informace (například věk, závodu, vzdělání, země původu, atd.)

### <a name="scope"></a>Rozsah
 * Zkoumání dat, školení a nasazení modelu strojového učení, které řešení problému předpovědi je popsáno v přehledu případu použití. 
 * Spuštění projektu ve službě Azure Machine Learning pomocí šablony vědecké zpracování týmových dat (TDSP) ze služby Azure Machine Learning pro tento projekt. Pro spuštění projektu a vytváření sestav budeme používat TDSP životního cyklu.
 * Operacionalizace řešení přímo ze služby Azure Machine Learning v Azure Container Service.

 Projekt zvýrazní několik funkcí služby Azure Machine Learning, takové vytvoření instance struktury TDSP a použití, provádění kódu v poznámkových bloků Jupyter, stejně jako soubory Pythonu a snadno operacionalizace v Dockeru a Kubernetes pomocí služby Azure Container Service.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Životní cyklus týmu vědecké zpracování dat (TDSP)
Zobrazit [týmu životního cyklu procesu (TDSP) pro datové vědy](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Požadavky
### <a name="required-subscription-hardware-software"></a>Požadováno: předplatné, hardware, software
1. Azure [předplatné](https://azure.microsoft.com). Můžete získat [bezplatné předplatné](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) spustit tento příklad také.
2. [Azure dat virtuálního počítače VĚDY Windows serveru 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (velikost virtuálního počítače: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)s 4 virtuální procesory a 14 Gb paměti RAM). I když otestovali na DSVM Azure, bude pravděpodobně pracovat na jakýkoli počítač s Windows 10.
3. Projděte si dokumentaci k Azure Machine Learning a související služby (dole najdete odkazy).
4. Ujistěte se, že jste správně nainstalovali Azure Machine Learning pomocí [Stručná Instalační příručka](../service/quickstart-installation.md).

Tato datová sada pro tuto ukázku je z úložiště ML UCI [[link]](https://archive.ics.uci.edu/ml/datasets/adult). Je převzat z databáze 1994 nám sčítání a obsahuje informace o sčítání a příjmu pro jednotlivce přibližně 50 000. Toto je strukturované datové sady s číselnými a kategorií funkcí a kategorií cíl skládající se ze dvou kategorií příjem ("> 50 tis." nebo "< = 50 tis."). 

### <a name="optional-version-control-repository"></a>Volitelné: Úložiště správy verzí
Pokud chcete uložit a verzi vašeho projektu a její obsah, musíte mít úložiště správy verzí, kde to lze provést. Při vytváření nového projektu pomocí šablony TDSP ve službě Azure Machine Learning můžete zadat umístění úložiště Git. Zobrazit [jak používat Git ve službě Azure Machine Learning](using-git-ml-project.md) další podrobnosti.

### <a name="informational-about-azure-machine-learning"></a>Informační: O službě Azure Machine Learning
* [Nejčastější dotazy – jak začít](frequently-asked-questions.md)
* [Přehled](../service/overview-what-is-azure-ml.md)
* [Instalace](../service/quickstart-installation.md)
* [Spuštění](experimentation-service-configuration.md)
* [Použití TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Čtení a zápis souborů](how-to-read-write-files.md)
* [Použití Gitu se službou Azure Machine Learning](using-git-ml-project.md)
* [Nasazení modelu ML jako webové služby](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Příjmy klasifikovat USA – projekt TDSP" a vyberte šablonu
5.  Klikněte na **Vytvořit**

Pokud zadáte umístění služby prázdná úložiště Git během vytváření projektu (do příslušného pole), pak daného úložiště naplní se se strukturou projektu a obsah po vytvoření projektu.

## <a name="use-case-overview"></a>Přehled případu použití
Problém je pochopit, jak socioekonomická data zaznamenaná v sčítání nám může pomoct předvídat roční příjem jednotlivců v USA. Založené na těchto funkcích sčítání, úloha machine learning je předpovědět, pokud příjem individuální uživatel je vyšší než 50 000 dolarů, nebo ne (binární klasifikace úlohu).

## <a name="data-description"></a>Popis dat
Podrobné informace o datech, najdete v článku [popis](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) v úložišti UCI. 

Tato data získané z databáze sčítání kanceláře v: https://www.census.gov/en.html. 


* Existují celkem 48,842 instance (před filtrování), kombinovat průběžné a diskrétní (trénování = 32, 561, otestovat = 16, 281)
* Pravděpodobnost popisku "> 50 tis. ': 23.93 % nebo 24.78 % (bez neznámé prvky)
* Pravděpodobnost popisku "< = 50 tis. ': 76.07 % nebo 75.22 % (bez neznámé prvky)  

* **Cíl**: příjem třídy "> 50 tis. ', ' < = 50 tis.". Tyto nahrazují 1 a 0 ve fázi přípravy dat.
* **FUNKCE**: věk, třída práce, vzdělání, vzdělání, závodu, pohlaví, počet hodin práce za týden, atd.


## <a name="project-structure-execution-and-reporting"></a>Struktura projektu, provádění a vytváření sestav

### <a name="structure"></a>Struktura
Pro tento projekt, jsme použití TDSP složky strukturu a dokumentace ke službě šablon (níže), který využívá [životního cyklu TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Vytvoření projektu podle pokynů uvedených [tady](https://aka.ms/how-to-use-tdsp-in-aml). Po, naplní se kód projektu a artefaktů, struktura vypadá takto (viz strukturu projektu v poli červeně na následujícím obrázku).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Spouštěcí
V tomto příkladu jsme spouštění kódu v **místním výpočetním prostředí**. Další informace naleznete na dokumenty Azure Machine Learning pro další podrobnosti v [možnosti spuštění](experimentation-service-configuration.md).

Spuštění skriptu v jazyce Python v místním modulu runtime Pythonu se snadno:

    az ml experiment submit -c local my_script.py

IPython notebook soubory může být poklepání ze struktury projektu na levé straně uživatelské rozhraní služby Azure Machine Learning a spustit na serveru Jypyter poznámkového bloku.


Pracovní postup krok za krokem datových věd došlo k následujícím způsobem:

* [**Získávání a pochopení dat**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Data se stáhl v podobě CSV z adres URL v úložišti ML UCI [[link]](https://archive.ics.uci.edu/ml/datasets/adult). Funkce, cíle a jejich transformace jsou popsány podrobně v souboru ProjectReport.md.

Kód pro získávání a pochopení dat se nachází v: / kód/01_data_acquisition_and_understanding.

Zkoumání dat se provádí pomocí Python 3 [IDEAR (interaktivní zkoumání dat a vytváření sestav) nástroj](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) publikované jako součást [TDSP sadu nástrojů pro datové vědy](https://github.com/Azure/Azure-TDSP-Utilities). Tento nástroj umožňuje generování sestav standardizované dat zkoumání dat obsahující funkce číselné a zařazená do kategorií a cíl. Podrobnosti o použití nástroje Python 3 IDEAR jsou uvedeny níže. 

Umístění sestavy zkoumání dat je [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Zobrazení sestavy IDEAR je zobrazena níže:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modelování**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Jsme vytvořili dva modely s 3-fold křížového ověřování: elastické Net a Random doménové struktury. Použili jsme [59 bodu vzorkování](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) pro náhodné mřížky vyhledávání jako strategii pro optimalizaci parametr křížového ověřování a modelu. Přesnost modelů se měří pomocí AUC (oblasti pod křivkou) na testovací datové sady. 

Kód pro modelování se nachází v: / kód/02_modeling.

AUC elastické Net a modely Random doménové struktury byly > než 0,85. Oba modely uložíme pickled.pkl souborů a výstup, který roc s více TŘÍDAMI, který pro oba modely. AUC náhodné doménové model byl 0.92 a elastické Net modelu bylo to 0,90. Kromě toho pro vyhodnocení modelu, důležitost funkce Random doménovou strukturu modelu jsou výstup do souboru .csv a vykreslena ve formátu pdf (pouze prvních 20 prediktivní funkce).

Křivka roc s více TŘÍDAMI **model doménové struktury Random** na testovací data se zobrazují níže. To se model, který byl nasazen:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Model doménové struktury Random funkce význam (prvních 20) je uveden níže. Zobrazí zisk velké množství funkcí, eduction, rodinný stav, mají nejvyšší důležitost funkce.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Nasazení**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Jsme nasadili Random doménovou strukturu modelu jako webové služby v clusteru v [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Operacionalizace vývojového a testovacího prostředí Docker a Kubernetes v clusteru pro správu nasazení webové služby. Další informace najdete v procesu operacionalizace [tady](model-management-service-deploy.md). 

Kód pro nasazení se nachází v: / kód/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Sestavy konečné projektu](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Podrobnosti o každém z výše uvedených částech jsou uvedeny ve zprávě o zkompilovaný poslední projekt [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Sestava projektu také obsahuje další podrobnosti o případu použití, metriky výkonu modelu, nasazení a infrastruktury, na kterém byl vyvinutý a nasazení projektu.

Projekt sestavy, společně s obsah celé složky projektu a verzi úložiště správy mohou bude doručen do klienta.


## <a name="conclusion"></a>Závěr

V této ukázce jsme vám ukázali teď pro použití TDSP strukturu a šablon v Azure Machine Learning. Prostřednictvím šablon a artefaktů dokumentů, můžete:
1. Správné definování účel a rozsah projektu
2. Vytvoření týmu projektu pomocí distribuované role a zodpovědnosti
3. Struktury a spouštění projektů podle fáze životního cyklu TDSP
4. Vyvíjejte standardizované sestav pomocí TDSP data science nástroje (jako je například IDEAR data zkoumání a vizualizaci sestavy).
5. Příprava dat vědy projektu sestavu, která se dá doručit do klienta

Doufáme, že používáte tuto funkci Azure Machine Learning pro usnadnění díky normalizaci a spolupráci v rámci vaší datové vědy týmy.

## <a name="next-steps"></a>Další kroky

Zobrazit odkazy níže, abyste mohli začít:

[Jak používat vědecké zpracování týmových dat (TDSP) ve službě Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Vědecké zpracování týmových dat (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Šablona projektu TDSP pro Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Příjem USA sadu dat z úložiště UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)