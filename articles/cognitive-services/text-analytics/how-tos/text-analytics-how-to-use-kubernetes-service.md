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
ms.openlocfilehash: 744be742d9f638df14f9d6720c9dad9678685af0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366889"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Nasazení kontejneru Analýza textu do služby Azure Kubernetes

Přečtěte si, jak nasadit image kontejneru Azure Cognitive Services [Analýza textu](./text-analytics-how-to-install-containers.md) do služby Azure Kubernetes Service (AKS). Tento postup ukazuje, jak vytvořit prostředek Analýza textu, jak vytvořit přidružený obrázek analýzy mínění a jak tuto orchestraci těchto dvou způsobů vykonat z prohlížeče. Pomocí kontejnerů můžete posouvat pozornost od správy infrastruktury a místo toho se zaměřit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services).
* Textový editor, například [Visual Studio Code](https://code.visualstudio.com/download).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalované.
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

**_

## <a name="next-steps"></a>Další kroky

_ Použít více [Cognitive Servicesch kontejnerů](../../cognitive-services-container-support.md)
* Použití [připojené služby analýza textu](../index.yml)