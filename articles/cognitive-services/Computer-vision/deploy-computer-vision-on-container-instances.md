---
title: Spusťte Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasazení kontejneru pro počítačové zpracování obrazu k službě Azure Container Instance a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711612"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Nasazení kontejneru pro počítačové zpracování obrazu do služby Azure Container Instances

Informace o nasazení služeb Cognitive Services [pro počítačové zpracování obrazu](computer-vision-how-to-install-containers.md) kontejneru do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku pro počítačové zpracování obrazu. Pak si popíšeme, stahování image kontejneru přidružené. Nakonec jsme zvýrazněte schopnost vykonávat Orchestrace dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]