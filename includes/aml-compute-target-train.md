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
ms.openlocfilehash: 2c275e1d8f54191e12454f7e97a8dd59e7c84be2
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539382"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.  Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý cíl výpočtů.

|Školení &nbsp;ch cílů|[Automatizované ML](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ano | &nbsp; | &nbsp; |
|[Instance COMPUTE Azure Machine Learning (Preview)](../articles/machine-learning/concept-compute-instance.md) | | ano |  |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ano & <br/>&nbsp;ladění parametrů | ano | ano |
|[Vzdáleném virtuálním počítači](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[Datacihly Azure&nbsp;](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| ano | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ano | &nbsp; |
