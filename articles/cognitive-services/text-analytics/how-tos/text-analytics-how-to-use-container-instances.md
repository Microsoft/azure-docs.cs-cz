---
title: Spustit Azure Container Instances – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejnery pro analýzu textu do instance kontejneru Azure a otestujte je ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 1dde59456126ce1221bdb71f09957c0fd3609427
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108701"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Nasazení kontejneru Analýza textu do Azure Container Instances

Přečtěte si, jak nasadit kontejner Cognitive Services [Analýza textu][install-and-run-containers] do Azure [Container Instances][container-instances]. Tento postup exemplifies vytvoření prostředku Analýza textu, vytvoření přidružené Analýza mínění image a schopnost vykonat tuto orchestraci těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Analýza textu pro stav](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Další kroky 

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances