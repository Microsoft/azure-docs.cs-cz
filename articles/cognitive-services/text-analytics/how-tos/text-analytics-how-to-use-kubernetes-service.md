---
title: Spuštění služby Azure Kubernetes – Analýza textu
titleSuffix: Azure Cognitive Services
description: Nasaďte Analýza textu image kontejneru do služby Azure Kubernetes a otestujte ji ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: db1c26805f76b0ca8991a562531b4476b6899d86
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130079"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Nasazení kontejneru Analýza textu do služby Azure Kubernetes

Přečtěte si, jak nasadit image kontejneru Azure Cognitive Services [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) do služby Azure Kubernetes Service (AKS). Tento postup ukazuje, jak vytvořit prostředek Analýza textu, jak vytvořit přidružený obrázek analýzy mínění a jak tuto orchestraci těchto dvou způsobů vykonat z prohlížeče. Pomocí kontejnerů můžete posouvat pozornost od správy infrastruktury a místo toho se zaměřit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Textový editor, například [Visual Studio Code](https://code.visualstudio.com/download).
* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalované.
* Rozhraní [CLI Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) je nainstalované.
* Prostředek Azure se správnou cenovou úrovní. Ne všechny cenové úrovně v tomto kontejneru fungují:
    * **Azure analýza textu** prostředky s cenovou úrovní F0 nebo Standard.
    * Prostředek **Azure Cognitive Services** s cenovou úrovní S0

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Analýza subjektivního hodnocení](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Další kroky

* Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../vs-text-connected-service.md)
