---
title: Spustit Azure Container Instances – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejnery pro analýzu textu s imagí analýzy mínění do instance kontejneru Azure a otestujte je ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552523"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Nasazení kontejneru Analýza mínění do Azure Container Instances

Naučte se, jak nasadit kontejner Cognitive Services [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) s analýza mínění image do Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup exemplifies vytvoření prostředku Analýza textu, vytvoření přidružené Analýza mínění image a schopnost vykonat tuto orchestraci těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Další postup 

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)
