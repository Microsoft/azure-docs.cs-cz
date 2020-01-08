---
title: Spustit kontejner pro rozpoznávání formulářů v Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner pro rozpoznávání formulářů do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 4e16eb2ad6c2634569f981ddcdce753ecaa43f2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446537"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Nasaďte kontejner pro rozpoznávání formulářů do Azure Container Instances

Přečtěte si, jak nasadit kontejner pro [rozpoznávání formulářů](form-recognizer-container-howto.md) Cognitive Services do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku pro rozpoznávání formulářů Azure. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

> [!IMPORTANT]
> Kontejnery pro rozpoznávání formulářů aktuálně používají rozhraní API pro rozpoznávání formulářů verze 1,0. K nejnovější verzi rozhraní API můžete přistupovat pomocí spravované služby místo toho.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, musíte nejprve dokončit a odeslat [formulář žádosti o přístup kontejnerů pro rozpoznávání formulářů Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) . Tím se také zaregistruje Počítačové zpracování obrazu. Nemusíte se do formuláře žádosti o Počítačové zpracování obrazu zaregistrovat samostatně. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
