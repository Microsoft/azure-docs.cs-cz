---
title: Spuštění služby Azure Kubernetes – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte Analýza textu image kontejneru do služby Azure Kubernetes a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243764"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Nasazení kontejneru Analýza textu do služby Azure Kubernetes

Přečtěte si, jak nasadit image kontejneru Azure Cognitive Services [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) do služby Azure Kubernetes Service (AKS). Tento postup ukazuje, jak vytvořit prostředek Analýza textu, jak vytvořit přidružený obrázek analýzy mínění a jak tuto orchestraci těchto dvou způsobů vykonat z prohlížeče. Pomocí kontejnerů můžete posouvat pozornost od správy infrastruktury a místo toho se zaměřit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services).
* Textový editor, například [Visual Studio Code](https://code.visualstudio.com/download).
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalované.
* Rozhraní [CLI Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) je nainstalované.
* Prostředek Azure se správnou cenovou úrovní. Ne všechny cenové úrovně v tomto kontejneru fungují:
    * **Azure analýza textu** prostředky s cenovou úrovní F0 nebo Standard.
    * Prostředek **Azure Cognitive Services** s cenovou úrovní S0

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

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)
