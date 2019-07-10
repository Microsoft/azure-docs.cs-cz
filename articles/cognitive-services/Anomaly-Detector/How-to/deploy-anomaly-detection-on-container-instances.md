---
title: Spusťte Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Detekce anomálií kontejner nasadí do Instance kontejneru Azure a testování ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711689"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Nasazení kontejneru detekce anomálií do služby Azure Container Instances

Informace o nasazení služeb Cognitive Services [detekce anomálií](../anomaly-detector-container-howto.md) kontejneru do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detekce anomálií. Pak si popíšeme, stahování image kontejneru přidružené. Nakonec jsme zvýrazněte schopnost vykonávat Orchestrace dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejneru detekce anomálií](https://aka.ms/adcontainer) chcete požádat o přístup ke kontejneru.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]