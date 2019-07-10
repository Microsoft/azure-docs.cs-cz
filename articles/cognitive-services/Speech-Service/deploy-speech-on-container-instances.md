---
title: Spusťte Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Speech Service kontejner nasadí do Instance kontejneru Azure a testování ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711513"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Nasazení kontejneru Speech Service do služby Azure Container Instances

Informace o nasazení služeb Cognitive Services [Speech Service](speech-container-howto.md) kontejneru do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku Azure Speech Service. Pak si popíšeme, stahování image kontejneru přidružené. Nakonec jsme zvýrazněte schopnost vykonávat Orchestrace dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejnerech řeči Cognitive Services](https://aka.ms/speechcontainerspreview/) chcete požádat o přístup ke kontejneru. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
