---
title: Spuštění instance kontejneru Azure – služba řeči
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner služby Rozpoznávání řeči do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717403"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Nasazení kontejneru služby Rozpoznávání řeči do instancí kontejneru Azure

Zjistěte, jak nasadit kontejner [služby Azure Speech](speech-container-howto.md) do [instance kontejneru](https://docs.microsoft.com/azure/container-instances/)Azure . Tento postup ukazuje vytvoření prostředku služby Azure Speech. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Musíte nejprve vyplnit a odeslat [formulář žádosti o žádost o kontejnery řeči služby Cognitive Services,](https://aka.ms/speechcontainerspreview/) abyste požádali o přístup ke kontejneru. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
