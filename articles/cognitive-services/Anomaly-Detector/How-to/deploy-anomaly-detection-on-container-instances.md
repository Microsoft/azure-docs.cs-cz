---
title: Spustit kontejner detektoru anomálií v Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner anomálie detektoru do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716352"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Nasazení kontejneru detektoru anomálií pro Azure Container Instances

Přečtěte si, jak nasadit kontejner [detektoru anomálií](../anomaly-detector-container-howto.md) Cognitive Services do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

Aby bylo možné požádat o přístup ke kontejneru, je nutné nejprve dokončit a odeslat [formulář žádosti o kontejner detektoru anomálií](https://aka.ms/adcontainer) .

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [instalace a spuštění kontejnerů](../anomaly-detector-container-configuration.md) pro vyžádání image kontejneru a spuštění kontejneru.
* Přečtěte si téma [konfigurace kontejnerů](../anomaly-detector-container-configuration.md) pro nastavení konfigurace
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
