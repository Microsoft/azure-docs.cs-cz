---
title: Spustit kontejner obličeje v Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner obličeje do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911252"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Nasazení kontejneru obličeje do Azure Container Instances

> [!IMPORTANT]
> Došlo k dosažení limitu počtu uživatelů kontejnerů Rozpoznávání tváře. V současné době nepřijímáme nové přihlášky ke kontejnerům Rozpoznávání tváře.

Přečtěte si, jak nasadit kontejner Cognitive Services [obličeje](../face-how-to-install-containers.md) do Azure [Container Instances](../../../container-instances/index.yml). Tento postup ukazuje vytvoření prostředku Azure Face. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]