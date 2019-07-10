---
title: Spusťte Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Služba LUIS kontejner nasadí do Instance kontejneru Azure a testování ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711568"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Nasazení kontejneru Language Understanding (LUIS) do služby Azure Container Instances

Informace o nasazení služeb Cognitive Services [LUIS](luis-container-howto.md) kontejneru do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detekce anomálií. Pak si popíšeme, stahování image kontejneru přidružené. Nakonec jsme zvýrazněte schopnost vykonávat Orchestrace dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
