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
ms.openlocfilehash: 081eea7842652884363603bc5ba742ba48539f85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503606"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další.** Například po připojení vzdáleného virtuálního počítače k pracovnímu prostoru jej můžete znovu použít pro více úloh. Pro kanály strojového učení použijte odpovídající [Krok kanálu](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) pro každý cíl výpočtů.

Pro většinu úloh můžete použít libovolný z následujících zdrojů pro školení výpočetní cíl. Ne všechny prostředky je možné použít pro automatizované strojové učení, kanály strojového učení nebo návrháře.

|&nbsp;Cíle školení|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály Machine Learningu](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář služby Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Yes | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Yes | Yes | Yes |
|[Výpočetní instance Azure Machine Learningu](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ano (přes SDK)  | Yes |  |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Yes  | Yes | &nbsp; |
|[&nbsp;Datacihly Azure](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ano (pouze místní režim sady SDK) | Yes | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Yes | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Yes | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Yes | &nbsp; |
