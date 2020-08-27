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
ms.openlocfilehash: 9a04bca11c6fca480ea1c9fd3d4a51956c1830de
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933102"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.  Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý cíl výpočtů.

|&nbsp;Cíle školení|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-set-up-training-targets.md#local)| ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Ano & <br/>ladění parametrů &nbsp; | ano | ano |
|[Výpočetní instance Azure Machine Learningu](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | Ano & <br/>ladění parametrů | ano |  |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[&nbsp;Datacihly Azure](../articles/machine-learning/how-to-set-up-training-targets.md#databricks)| Ano (pouze místní režim sady SDK) | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-set-up-training-targets.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | ano | &nbsp; |
