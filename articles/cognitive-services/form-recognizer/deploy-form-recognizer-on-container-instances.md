---
title: Spusťte Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasazení kontejneru Rozlišovač formuláře do Instance kontejneru Azure a testování ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711392"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Nasazení kontejneru Rozlišovač formuláře do služby Azure Container Instances

Informace o nasazení služeb Cognitive Services [formuláře Rozlišovač](form-recognizer-container-howto.md) kontejneru do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku Azure formuláře Rozlišovače. Pak si popíšeme, stahování image kontejneru přidružené. Nakonec jsme zvýrazněte schopnost vykonávat Orchestrace dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [Cognitive Services formuláře Rozlišovač kontejnery přístup formulář žádosti o](https://aka.ms/FormRecognizerRequestAccess) chcete požádat o přístup ke kontejneru. Tím také přihlásíte pro počítačové zpracování obrazu. Není nutné se zaregistrovat do služby pro počítačové zpracování obrazu formulář žádosti o samostatně. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
