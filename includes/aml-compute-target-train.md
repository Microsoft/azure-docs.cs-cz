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
ms.openlocfilehash: dc6c29793b71525ad64254aa9f57e12e56601adc
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82726210"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.  Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý cíl výpočtů.

|Cíle &nbsp;školení|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ano & <br/>ladění parametrů&nbsp; | ano | ano |
|[Azure Machine Learning výpočetní instance](../articles/machine-learning/concept-compute-instance.md) | Ano & <br/>ladění parametrů | ano | ano |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[Datacihly Azure&nbsp;](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Ano (pouze místní režim sady SDK) | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ano | &nbsp; |
