---
title: Spustit kontejner detektoru anomálií v Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner anomálie detektoru do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 975efa9fdaff9175317794d8b6df7afbfc73cba8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83585674"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Nasazení kontejneru detektoru anomálií pro Azure Container Instances

Přečtěte si, jak nasadit kontejner [detektoru anomálií](../anomaly-detector-container-howto.md) Cognitive Services do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [instalace a spuštění kontejnerů](../anomaly-detector-container-configuration.md) pro vyžádání image kontejneru a spuštění kontejneru.
* Přečtěte si téma [konfigurace kontejnerů](../anomaly-detector-container-configuration.md) pro nastavení konfigurace
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
