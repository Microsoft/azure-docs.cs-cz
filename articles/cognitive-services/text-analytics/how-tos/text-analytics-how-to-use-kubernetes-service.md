---
title: Spuštění služby Azure Kubernetes – analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte image kontejneru Text Analytics do služby Azure Kubernetes a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877808"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Nasazení kontejneru Analýzy textu do služby Azure Kubernetes

Zjistěte, jak nasadit image kontejneru Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) do služby Azure Kubernetes Service (AKS). Tento postup ukazuje, jak vytvořit prostředek analýzy textu, jak vytvořit přidružený obrázek analýzy mínění a jak tuto orchestraci obou z prohlížeče uplatnit. Pomocí kontejnerů můžete přesunout vaši pozornost od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalovány a spuštěny místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Textový editor, například [Visual Studio Code](https://code.visualstudio.com/download).
* Nainstalované [vázané příkazové k mizér Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) nainstalován.
* Prostředek Azure se správnou cenovou úrovní. S tímto kontejnerem nefungují všechny cenové úrovně:
    * **Azure Text Analytics** prostředek s F0 nebo standardní cenové úrovně jenom.
    * **Prostředek Azure Cognitive Services** s cenovou úrovní S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Další kroky

* Použití [dalších kontejnerů služeb Cognitive Services](../../cognitive-services-container-support.md)
* Použití [propojené služby Analýzy textu](../vs-text-connected-service.md)
