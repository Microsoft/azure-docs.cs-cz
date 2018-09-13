---
title: Analýza mínění s využitím obsáhlého learningu pomocí služby Azure Machine Learning | Dokumentace Microsoftu
description: Jak provádět analýzu subjektivního hodnocení obsáhlý learning pomocí Azure ML Workbench.
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
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: 97e3a621e291935db2e0c70eb2b596e77c7bffb7
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644479"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analýza mínění s využitím obsáhlého learningu pomocí služby Azure Machine Learning

Analýza subjektivního hodnocení je dobře známé úlohy v souvislosti se zpracováním přirozeného jazyka. Vzhledem k sadě textů, cílem je zjistit mínění textu. Cílem tohoto řešení je pomocí hloubkového učení pro predikci mínění v recenze filmů.

Řešení se nachází na https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie

Přejděte na tento odkaz do veřejného úložiště GitHub:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Přehled případu použití

Obrovské množství dat a růst počtu mobilních zařízení vytvořili spoustu příležitostí pro zákazníky, kteří express jejich nálad a postoje o všech informací za všech kdykoli. Tento stanovisko nebo "mínění" generovaná přes sociální kanály ve formě revize, konverzace, sdílí, lajků tweety, atd. Mínění mohou být neocenitelnou pomocí pro podniky chtějí zlepšení produktů a služeb, informovaněji rozhodovat a lepší podporu jejich značky.

K získání hodnoty z analýzu mínění, firmám musí mít možnost dolovat rozsáhlé úložiště nestrukturovaných dat v sociálních sítích pro užitečné přehledy. V této ukázce jsme vyvíjet modely obsáhlého learningu pro provádění analýzu subjektivního hodnocení ve pomocí AMLWorkbench recenze filmů

## <a name="prerequisites"></a>Požadavky

* [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).

* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Stručná Instalační příručka](../service/quickstart-installation.md) k instalaci a vytvoření pracovního prostoru.

* Operacionalizace je nejvhodnější v případě, že máte nainstalovaný a místně spuštěný modul Docker. Pokud ne, můžete použít možnost clusteru. Spuštění Azure Container Service (ACS) však může být nákladné.

* Toto řešení se předpokládá, že spustíte aplikaci Azure Machine Learning Workbench ve Windows 10 s místně nainstalovaný modul Docker. V systému macOS instrukce je z velké části stejný.

## <a name="data-description"></a>Popis dat

Datová sada používá pro tuto ukázku je malá datová sada ručně vytvořený a je umístěn v [složka dat](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

První sloupec obsahuje recenze filmů a druhý sloupec obsahuje jejich mínění (0 - negativní a 1 - pozitivní). Tato datová sada použije jenom pro demonstrační účely, ale obvykle Chcete-li získat skóre mínění robustní, potřebujete velkou datovou sadu. Například [IMDB film kontroly problém klasifikace mínění](https://keras.io/datasets/#datasets ) z rozhraní Keras se skládá z datové sady recenze filmů 25 000 z IMDB označené mínění (kladná nebo záporná). Záměrem tohoto testovacího prostředí je ukazují, jak provádět analýzu subjektivního hodnocení s AMLWorkbench využívající hloubkové učení.

## <a name="scenario-structure"></a>Struktura scénář

Struktura složek uspořádána následujícím způsobem:

1. Veškerý kód související s analýza mínění s využitím AMLWorkbench je v kořenové složce
2. data: obsahuje datové sady použité v daném řešení
3. dokumentace: obsahuje praktická cvičení

Pořadí praktického cvičení provádět řešení je následujícím způsobem:

| Objednání| Název souboru | Související soubory |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization. |

## <a name="conclusion"></a>Závěr

Na závěr tohoto řešení vás seznámí s využitím obsáhlého learningu provádět analýzu subjektivního hodnocení s Azure Machine Learning Workbench. Také zprovoznění HDF5 modely.
