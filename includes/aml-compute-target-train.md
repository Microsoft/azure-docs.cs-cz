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
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806031"
---
**COMPUTE cíle lze znovu použít z jedné trénovací úlohu na další**. Například po připojení vzdáleném virtuálním počítači do svého pracovního prostoru, jej můžete znovu použít pro více úloh.

|Školení &nbsp;cíle| Podpora GPU |[Automatizované ML](../articles/machine-learning/service/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Vizuální rozhraní](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Možná | ano | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ano | Ano & <br/>hyperparameter&nbsp;ladění | ano | ano |
|[Vzdáleném virtuálním počítači](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ano | Ano & <br/>hyperparametrů | ano | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ano | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ano | &nbsp; |
