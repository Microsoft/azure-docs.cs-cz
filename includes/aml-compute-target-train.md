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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489573"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.

|Školení &nbsp;ch cílů| Podpora GPU |[Automatizované ML](../articles/machine-learning/service/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Možná | ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní instance](../articles/machine-learning/service/concept-compute-instance.md)| ano | | ano |  |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ano | Ano & <br/>&nbsp;ladění parametrů | ano | ano |
|[Vzdálený virtuální počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ano | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[Datacihly Azure&nbsp;](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ano | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ano | &nbsp; |
