---
title: Spustit kontejner pro rozpoznávání formulářů v Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner pro rozpoznávání formulářů do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913160"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Nasaďte kontejner pro rozpoznávání formulářů do Azure Container Instances

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Přečtěte si, jak nasadit kontejner pro [rozpoznávání formulářů](form-recognizer-container-howto.md) Cognitive Services do Azure [Container Instances](../../container-instances/index.yml). Tento postup ukazuje vytvoření prostředku pro rozpoznávání formulářů Azure. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]