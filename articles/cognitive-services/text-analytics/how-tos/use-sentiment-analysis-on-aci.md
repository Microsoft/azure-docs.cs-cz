---
title: Spusťte Azure Container Instances
titleSuffix: Text Analytics - Azure Cognitive Services
description: Nasazení kontejnerů text analytics Image analýzy mínění, do Azure Container Instance a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454980"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Nasazení kontejneru analýzu subjektivního hodnocení na Azure Container Instances (ACI)

Informace o nasazení služeb Cognitive Services [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) kontejneru s analýzu subjektivního hodnocení imagí do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Tento postup exemplifies vytvoření prostředku pro analýzu textu, vytváření přidružené image analýzu subjektivního hodnocení a schopnost vykonávat tuto orchestraci dvě z prohlížeče. Pomocí kontejnerů můžete posunout vývojářů pozornost od správy infrastruktury na místo toho soustředit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Další postup 

* Použití více [kontejnery Cognitive Services](../../cognitive-services-container-support.md)
* Použití [rozhraní Text Analytics připojená služba](../vs-text-connected-service.md)
