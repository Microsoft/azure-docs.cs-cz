---
title: Spuštění instancí kontejnerů Azure – analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejnery pro analýzu textu do instance kontejneru Azure a otestujte je ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e4b61c6fe2f62745d0f5268221cbb5c84803eb10
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876401"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Nasazení kontejneru analýzy textu do instancí kontejnerů Azure

Přečtěte si, jak nasadit kontejner [Azure Data Analýzy textu][install-and-run-containers] do instance [kontejneru][container-instances]Azure . Tento postup ilustruje vytvoření prostředku analýzy textu, vytvoření přidruženého obrázku analýzy mínění a schopnost vykonávat tuto orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

* Použijte předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Další kroky 

* Použití více [kontejnerů služeb Cognitive Services](../../cognitive-services-container-support.md)
* Použití [propojené služby Analýzy textu](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances