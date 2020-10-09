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
ms.date: 09/17/2020
ms.openlocfilehash: cdf2c3d1840f64b267f1aac1f8877d5f0bf76082
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841936"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další.** Například po připojení vzdáleného virtuálního počítače k pracovnímu prostoru jej můžete znovu použít pro více úloh. Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) pro každý cíl výpočtů.

Pro většinu úloh můžete použít libovolný z následujících zdrojů pro školení výpočetní cíl. Ne všechny prostředky je možné použít pro automatizované strojové učení, kanály strojového učení nebo návrháře.

|&nbsp;Cíle školení|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály Machine Learningu](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář služby Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ano | Ano | Ano |
|[Výpočetní instance Azure Machine Learningu](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ano (přes SDK)  | Ano |  |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ano  | Ano | &nbsp; |
|[&nbsp;Datacihly Azure](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ano (pouze místní režim sady SDK) | Ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Ano | &nbsp; |
