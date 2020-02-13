---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156526"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.  Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý cíl výpočtů.

|Školení &nbsp;ch cílů|[Automatizované ML](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ano & <br/>&nbsp;ladění parametrů | ano | ano |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[Datacihly Azure&nbsp;](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Ano (pouze místní režim sady SDK) | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ano | &nbsp; |
