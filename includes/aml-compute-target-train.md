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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156526"
---
**Výpočetní cíle lze znovu použít z jedné úlohy školení do další**. Například po připojení vzdáleného virtuálního počítače k pracovnímu prostoru jej můžete znovu použít pro více úloh.  Pro kanály strojového učení použijte příslušný [krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý výpočetní cíl.

|Tréninkové &nbsp;cíle|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ano | &nbsp; | &nbsp; |
|[Výpočetní cluster Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Jo, & <br/>hyperparametrické&nbsp;ladění | ano | ano |
|[Vzdálený virtuální virtuální ms](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Jo, & <br/>hyperparametrické ladění | ano | &nbsp; |
|[Datové&nbsp;cihly Azure](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| ano (pouze místní režim sady SDK) | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ano | &nbsp; |
