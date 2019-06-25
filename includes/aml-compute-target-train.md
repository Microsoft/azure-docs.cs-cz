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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752949"
---
|Školení &nbsp;cíle| Podpora GPU |[Automatizované ML](../articles/machine-learning/service/concept-automated-ml.md) | [Kanály ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Vizuální rozhraní](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Místní počítač](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Možná | ano | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| ano | Ano & <br/>hyperparameter&nbsp;ladění | ano | ano |
|[Vzdáleném virtuálním počítači](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |ano | Ano & <br/>hyperparametrů | ano | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | ano | ano | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | ano | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | ano | &nbsp; |

**Všechny výpočetní cíle lze opětovně použít pro více úlohy trénování**. Například po připojení vzdáleném virtuálním počítači do svého pracovního prostoru, jej můžete znovu použít pro více úloh.