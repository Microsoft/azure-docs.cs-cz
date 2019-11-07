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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580603"
---
**Cíle výpočetní služby se dají znovu použít z jedné školicí úlohy do další**. Když například připojíte vzdálený virtuální počítač k pracovnímu prostoru, můžete ho znovu použít pro více úloh.

|Školení &nbsp;ch cílů| Podpora GPU |[Automatizované ML](../articles/machine-learning/service/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Návrhář Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Možná | ano | &nbsp; | &nbsp; |
|[Azure Machine Learning výpočetní cluster](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ano | Ano & <br/>&nbsp;ladění parametrů | ano | ano |
|[Vzdálený virtuální počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ano | Ano & <br/>ladění parametrů | ano | &nbsp; |
|[Datacihly Azure&nbsp;](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ano | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ano | &nbsp; |
