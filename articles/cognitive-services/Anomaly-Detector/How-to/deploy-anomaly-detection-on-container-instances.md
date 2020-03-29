---
title: Spuštění kontejneru detektoru anomálií v instanci kontejnerů Azure
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner Detektor anomálií na instanci kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716352"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Nasazení kontejneru detektoru anomálií do instancí kontejnerů Azure

Zjistěte, jak nasadit kontejner [Detektor anomálií](../anomaly-detector-container-howto.md) kognitivních služeb do [instancí kontejnerů](https://docs.microsoft.com/azure/container-instances/)Azure . Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru privátního kontejneru

Musíte nejprve vyplnit a odeslat [formulář žádosti kontejneru detektoru anomálií](https://aka.ms/adcontainer) požádat o přístup ke kontejneru.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Další kroky

* Zkontrolujte [instalaci a spuštění kontejnerů](../anomaly-detector-container-configuration.md) pro vytažení bitové kopie kontejneru a spuštění kontejneru
* Kontrola [Konfigurace kontejnerů](../anomaly-detector-container-configuration.md) pro nastavení konfigurace
* [Další informace o službě API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
