---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning
description: Naučte se, jak a kde nasadit modely Azure Machine Learning, včetně Azure Container Instances, služby Azure Kubernetes, Azure IoT Edge a programovatelné pole brány.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 5e73744f3d467d08944d0e8800dd6d8824857abd
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846984"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Nasazování modelů pomocí služby Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo Azure IoT Edge zařízení.

Pracovní postup je podobný bez ohledu na to, kam model nasadíte:

1. Zaregistrujte model.
1. Příprava odvození konfigurace
1. Příprava vstupního skriptu (Pokud nepoužíváte [nasazení bez kódu](how-to-deploy-no-code-deployment.md))
1. Nasaďte model do cílového výpočetního objektu.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Princip stavu služby

Během nasazování modelu se může zobrazit Změna stavu služby během plného nasazení.

Následující tabulka popisuje různé stavy služby:

| Stav WebService | Popis | Konečný stav?
| ----- | ----- | ----- |
| Přechod | Služba je v procesu nasazení. | Ne |
| Není v pořádku | Služba je nasazená, ale v tuto chvíli není dostupná.  | Ne |
| Unschedulable | Službu nyní nelze nasadit z důvodu nedostatku prostředků. | Ne |
| Neúspěšný | Nasazení služby se nezdařilo z důvodu chyby nebo selhání. | Ano |
| V pořádku | Služba je v pořádku a koncový bod je k dispozici. | Ano |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány pomocí Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v tématu [How to run Batch předpovědi](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v tématu [nasazení Azure Machine Learning jako modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)

