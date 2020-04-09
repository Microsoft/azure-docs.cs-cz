---
title: Spuštění kontejneru nástroje pro rozpoznávání formulářů v instanci kontejneru Azure
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner rozpoznávání formulářů do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879526"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Nasazení kontejneru nástroje pro rozpoznávání formulářů do instancí kontejnerů Azure

Zjistěte, jak nasadit kontejner [rozpoznávání formulářů](form-recognizer-container-howto.md) cognitive services do [instancí kontejnerů](https://docs.microsoft.com/azure/container-instances/)Azure . Tento postup ukazuje vytvoření prostředku nástroje azure form recognizer. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

> [!IMPORTANT]
> Kontejnery nástroje pro rozpoznávání formulářů aktuálně používají verzi 1.0 rozhraní API pro rozpoznávání formulářů. K nejnovější verzi rozhraní API můžete získat přístup pomocí spravované služby.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Musíte nejprve vyplnit a odeslat [formulář pro rozpoznávání formulářů Cognitive Services Kontejnery přístup formulář požádat](https://aka.ms/FormRecognizerContainerRequestAccess) o přístup ke kontejneru. Přitom také podepíše vás na počítačové vidění. Není nutné se přihlásit do formuláře žádosti o počítačové vize samostatně. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
