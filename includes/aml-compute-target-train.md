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
ms.date: 08/19/2020
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146725"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.  Pro kanály strojového učení použijte odpovídající [Krok kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) pro každý cíl výpočtů.

|&nbsp;Cíle školení|[Automatizované strojové učení](../articles/machine-learning/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| Ano & <br/>ladění parametrů &nbsp; | ano | ano |
|[Výpočetní instance Azure Machine Learningu](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | Ano & <br/>ladění parametrů | ano |  |
|[Vzdálený virtuální počítač](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[&nbsp;Datacihly Azure](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| Ano (pouze místní režim sady SDK) | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | ano | &nbsp; |
