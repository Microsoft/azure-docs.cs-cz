---
title: Řešení předpovědi výpovědí zákazníků pomocí služby Azure Machine Learning | Dokumentace Microsoftu
description: Jak provádět změny analytics pomocí Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7c7b50098cfd1bcac534156dd905b37affab80bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643050"
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Předpověď výpovědi zákazníka pomocí Azure Machine Learning

Udržování stávající zákazníci je v průměru pětkrát levnější než náklady na přijetí nové značky. V důsledku toho vedoucí pracovníci marketingu často naleznou sami sebe snaží odhadnout pravděpodobnost výpovědi zákazníků a přijít na výpovědí minimalizovat.

Cílem tohoto řešení je předvést prediktivní změn analytics pomocí Azure Machine Learning Workbench. Toto řešení poskytuje snadno použitelné šablony pro vývoj změn prediktivní datové kanály pro maloobchodní prodejce. Šablonu je možné pomocí různých datových sad a různé definice četností změn. Cílem praktických ukázek je:

1. Seznamte se s Azure Machine Learning Workbench pro přípravu dat nástroje vyčistí a nově ingestování dat vztahů se zákazníky pro analýzu provozu.

2. Provede transformaci funkce pro zpracování hlučného heterogenní data.

3. Integrace knihovny třetích stran (například `scikit-learn` a `azureml`) k vývoji Bayesova a založený na stromové architektuře třídění pro predikci změn.

4. Nasazení.

## <a name="link-of-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie
Tady je odkaz do veřejného úložiště GitHub, kde se hostuje kód:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Přehled případu použití
Podniky potřebují efektivní strategii pro správu fluktuace zákazníků. Fluktuace zákazníků zahrnuje ukončení používání služby, přechod na službu konkurence, přechod na nižší úroveň prostředí ve službě nebo omezení služby.

V tomto případě se podíváme se na data z Francouzské telekomunikační společnosti Orange k určení zákazníků, kteří jsou pravděpodobně v blízké době k vylepšování služby a vytvořila vlastní veřejné kampaně, které pomáhají udržet si zákazníky.

Čelí telekomunikačních společností trhu. Mnoho prostředníci ztrátě výnosů kvůli fluktuaci zákazníků na změny. Proto můžete možnost přesně určovat fluktuaci zákazníků se to obrovská konkurenční výhoda.

Některé z faktorů přispívajících k fluktuaci zákazníků telekomunikační systémy patří:

* Zjištěné častá přerušení služeb
* Nevyhovující zákaznické prostředí služby v online/maloobchodě
* Nabídky od jiných konkurenčních prostředníci (lepší řady plán, datový tarif, atd.).

V tomto řešení využijeme konkrétní příklad sestavení změn modelu prediktivní zákazníka pro telekomunikačních společností.

## <a name="prerequisites"></a>Požadavky

* [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici)

* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Stručná Instalační příručka](../service/quickstart-installation.md) k instalaci a vytvoření pracovního prostoru

* Operacionalizace je nejvhodnější v případě, že máte nainstalovaný a místně spuštěný modul Docker. Pokud ne, použijte možnost clusteru, ale mějte na paměti spuštění Azure Container Service (ACS) může být nákladné.

* Toto řešení se předpokládá, že spustíte aplikaci Azure Machine Learning Workbench ve Windows 10 s místně nainstalovaný modul Docker. Pokud používáte macOS instrukce je z velké části stejný.

## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Řešení předpovědi výpovědí zákazníků" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

Datové sady použité v daném řešení se od konkurence SIDKDD 2009. Je volána `CATelcoCustomerChurnTrainingSample.csv` a nachází se v [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) složky. Datová sada se skládá z heterogenních dat hlučného (číselné nebo kategorií proměnné) z Francouzské telekomunikační společnosti oranžovou a jsou anonymní.

Proměnné zachytávání demografických informací o zákaznicích, statistiku volání (například průměrná doba volání, frekvence neúspěšných volání atd.), smlouvy informace, statistiky předpisy. Četnost změn proměnná je binární (0 - není změny, 1 – změn dat).

## <a name="scenario-structure"></a>Struktura scénář

Struktura složek uspořádána následujícím způsobem:

__data__: obsahuje datové sady použité v daném řešení  

__dokumentace__: obsahuje praktická cvičení

Pořadí praktického cvičení provádět řešení je následujícím způsobem:
1. Přípravy dat: Je hlavní soubor související s přípravou dat ve složce data `CATelcoCustomerChurnTrainingSample.csv`
2. Modelování a hodnocení: hlavní soubor související s modelování a hodnocení v kořenové složce `CATelcoCustomerChurnModeling.py`
3. Modelování a vyhodnocení bez .dprep: hlavní soubor pro tuto úlohu v kořenové složce `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operacionalizace: Hlavní soubory deloyment jsou modelu (`model.pkl`) a `churn_schema_gen.py`

| Objednání| Název souboru | Soubory Rizikoví |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv. |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py. |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | "CATelcoCustomerChurnModelingWithoutDprep.py. |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl.<br>"churn_schema_gen.py. |

Postupujte podle Tato praktická cvičení v sekvenčním způsobem je popsáno výše.

## <a name="conclusion"></a>Závěr
Tato praktická scénář ukázal, jak k provádění předpověď výpovědi pomocí Azure Machine Learning Workbench. Provedli jsme zpracování aktivní nebo heterogenních dat, za nímž následuje vytváření funkcí pomocí nástroje pro přípravu dat pro čištění dat. Jsme pak používají k vytváření a vyhodnocení modelů klasifikace opensourcových nástrojů strojového učení a potom použít kontejner dockeru pro místní nasazení modelu díky tomu je připravený pro produkční prostředí.
