---
title: Spustit Azure Container Instances – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejnery pro analýzu textu do instance kontejneru Azure a otestujte je ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383532"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Nasazení kontejneru Analýza textu do Azure Container Instances

Přečtěte si, jak nasadit kontejner Cognitive Services [Analýza textu][install-and-run-containers] do Azure [Container Instances][container-instances]. Tento postup exemplifies vytvoření prostředku Analýza textu, vytvoření přidružené Analýza mínění image a schopnost vykonat tuto orchestraci těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Další kroky 

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances