---
title: Zákazník změn předpovědi pomocí Azure Machine Learning | Microsoft Docs
description: Postup provedení změn analytics pomocí Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 501735e1cf32359a34c01ad1c7990609a2bf4058
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Předpověď změn zákazníka pomocí Azure Machine Learning

Zachování stávající zákazníky služby je v průměru pětkrát levnějších než náklady o přijetí nové. V důsledku toho marketing vedení často najít sami pokusu odhadnout pravděpodobnost změn zákazníka a hledání akce nezbytné pro minimalizaci klidové vytížení.

Cílem tohoto řešení je k předvedení prediktivní změn analytics pomocí Azure Machine Learning Workbench. Toto řešení poskytuje snadno použitelný šablonu k vývoji změn prediktivní datových kanálů pro prodejců. Šablony lze použít s různými datovými sadami a jiné definice změn. Cílem praktických příklad je:

1. Pochopení Azure Machine Learning Workbench na přípravu dat nástrojů pro čištění a ingestování dat vztah zákazníků k analýze změn.

2. Proveďte transformaci funkci pro zpracování aktivní heterogenní data.

3. Integrace knihovny třetích stran (například `scikit-learn` a `azureml`) pro vývoj Bayesova a na základě stromu třídění pro predikci změn.

4. Nasadíte.

## <a name="link-of-the-gallery-github-repository"></a>Odkaz úložiště GitHub Galerie
Toto je odkaz na veřejné úložiště GitHub je hostitelem všechny kód:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Přehled případu použití
Společnosti potřebují efektivní strategie pro správu změn zákazníka. Zákazník změn zahrnuje zákazníky zastavení použití služby, přepínání službě konkurence, přepnutí na nižší úroveň prostředí ve službě nebo snížit zapojení se službou.

V takovém případě použijte Podíváme se na data od společnosti francouzštině telekomunikace oranžová k určení zákazníků, kteří jsou pravděpodobně změn v nejbližší době k vylepšování služby a vytvořit vlastní dosah kampaní, které pomáhají zachovat zákazníků.

Telekomunikace společností čelí konkurenční trh. Mnoho prostředníci ztraceny výnosy postpaid zákazníků z důvodu změn. Schopnost přesně určit zákazníka změn proto může být velký konkurencí.

Některé z faktorů přispívání do telekomunikace zákazníka změn patří:

* Přerušení dosahovaný časté služeb
* Možnosti služby nízký zákazníka v online nebo prodejní úložiště
* Nabízí z jiných konkurenčních prostředníci (lépe rodinách plán, datový tarif atd.).

V tomto řešení používáme konkrétní příklady vytváření změn modelu prediktivní zákazník pro telekomunikace společnosti.

## <a name="prerequisites"></a>Požadavky

* [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici)

* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Průvodce instalací úvodní](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor

* Operationalization je nejvhodnější Pokud máte Docker modul nainstalován a spuštěn místně. Pokud ne, můžete použít možnost clusteru, ale mějte na paměti spuštění služby kontejneru Azure (ACS) může být náročná.

* Toto řešení předpokládá, že jsou Azure Machine Learning Workbench spuštěn k modulu Docker místně nainstalovaný na Windows 10. Pokud používáte systému macOS pokyn je z velké části stejný.

## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Zákazníka změn předpovědi" a vyberte šablonu
5.  Klikněte na **Vytvořit**

## <a name="data-description"></a>Popis dat

Datové sady použité v řešení je před konkurencí SIDKDD 2009. Je volána `CATelcoCustomerChurnTrainingSample.csv` a nachází se v [ `data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data) složky. Datová sada se skládá z heterogenní aktivní data (číselná nebo kategorií proměnné) od společnosti francouzština telekomunikace oranžová a je anonymní.

Proměnné zaznamenat demografické údaje zákazníka, statistiku volání (například volání Průměrná doba trvání, četnosti selhání volání atd.), smlouvy informace statistiky předpisy. Proměnná změn je binární (0 - není změn, 1 – změn).

## <a name="scenario-structure"></a>Struktura scénář

Struktura složek uspořádána následujícím způsobem:

__data__: obsahuje datové sady použité v řešení  

__dokumentace__: obsahuje praktická cvičení

Pořadí Hands-on Labs provádět řešení je následující:
1. Přípravu dat: Je hlavní soubor týkající se přípravy Data ve složce data `CATelcoCustomerChurnTrainingSample.csv`
2. Modelování a vyhodnocení: hlavní soubor související s modelování a vyhodnocení v kořenové složce `CATelcoCustomerChurnModeling.py`
3. Modelování a vyhodnocení bez .dprep: hlavní soubor pro tuto úlohu v kořenové složce `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operationalization: Hlavní soubory deloyment jsou modelu (`model.pkl`) a `churn_schema_gen.py`

| Pořadí| Název souboru | Realted soubory |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv. |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py. |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py. |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl.<br>'churn_schema_gen.py. |

Postupujte podle Labs sekvenční způsobem popsané výše.

## <a name="conclusion"></a>Závěr
To předá scénář ukázal, jak lze provést pomocí Azure Machine Learning Workbench předpovědi změn. Nám nejdřív provést čištění zpracování aktivní a heterogenní dat, za nímž následuje inženýrství funkce pomocí nástroje pro přípravu dat dat. Jsme pak používají s otevřeným zdrojem strojového učení nástroje k vytváření a vyhodnocení modelu klasifikace a potom použít kontejner docker místní nasazení modelu, takže je připravená pro vydání.
