---
title: Analýza postojích hloubkové Learning pomocí Azure Machine Learning | Microsoft Docs
description: Jak provádět analýzy postojích hloubkové learning pomocí Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: c780063074ec1cffbb2a667cb26ab1c86f820167
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833576"
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analýza postojích hloubkové Learning pomocí Azure Machine Learning

Analýza postojích je dobře známé úloha ve sféře, zpracování přirozeného jazyka. Zadána sada texty cílem je určit postojích tohoto textu. Cílem tohoto řešení je použijte hloubkové Learning pro predikci postojích z film recenze.

Řešení, které je v umístění https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie

Tento odkaz veřejného úložiště GitHub:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Přehled případu použití

Prudký nárůst počtu dat a jak narůstá počet mobilních zařízení jste vytvořili spoustu příležitosti pro zákazníky express jejich pocitů a postojích o nic a všechno kdykoli. Tato stanovisko nebo "postojích" je často generované prostřednictvím sociálních kanálů ve formě recenze, konverzace, sdílených složek, líbí, tweetů atd. Myšlenkou mohou být neocenitelnou pomocí firmám vyhledávání zlepšení produktů a služeb, lépe rozhodovat a lépe podporovat jejich značky.

Chcete-li získat hodnotu z postojích analýzy, musí mít firmy možnost Moje velká úložišť nestrukturovaných dat sociálních pro prakticky využitelné informace. V této ukázce jsme vyvíjet hloubkové learning modely pro provádění analýzy postojích film recenze pomocí AMLWorkbench

## <a name="prerequisites"></a>Požadavky

* [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).

* Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Průvodce instalací úvodní](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor.

* Operationalization je nejvhodnější Pokud máte Docker modul nainstalován a spuštěn místně. Pokud ne, můžete použít možnost clusteru. Spuštění služby kontejneru Azure (ACS) však může být náročná.

* Toto řešení předpokládá, že jsou Azure Machine Learning Workbench spuštěn k modulu Docker místně nainstalovaný na Windows 10. V systému macOS pokyn je z velké části stejný.

## <a name="data-description"></a>Popis dat

Datová sada používá tato ukázka je na malou datovou sadu vytvořené ruční a je umístěn ve [složky dat](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

První sloupec film recenze a druhý sloupec obsahuje jejich postojích (0 - záporné a 1 - kladnou). Datová sada se používá jenom pro demonstrační účely, ale obvykle robustní postojích skóre získáte, je třeba velké datové sady. Například [IMDB film zkontroluje postojích klasifikace problému](https://keras.io/datasets/#datasets ) z Keras se skládá z datové sady recenze 25 000 filmy z IMDB, označené postojích (kladné a záporné). Záměrem tohoto testovacího prostředí je ukazují, jak provádět analýzy postojích hloubkové learning pomocí AMLWorkbench.

## <a name="scenario-structure"></a>Struktura scénář

Struktura složek uspořádána následujícím způsobem:

1. Všechny kód týkající se analýzy postojích pomocí AMLWorkbench je v kořenové složce
2. data: obsahuje datové sady použité v řešení
3. dokumenty: obsahuje praktická cvičení

Pořadí Hands-on Labs provádět řešení je následující:

| Pořadí| Název souboru | Související soubory |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization. |

## <a name="conclusion"></a>Závěr

Na závěr toto řešení vás seznámí s pomocí hloubkové Learning k provedení analýzy postojích pomocí nástroje Azure Machine Learning Workbench. Také zprovoznit použití HDF5 modelů.
