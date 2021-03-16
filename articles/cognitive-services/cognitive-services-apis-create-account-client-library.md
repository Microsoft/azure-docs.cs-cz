---
title: Vytvoření prostředku Cognitive Services pomocí klientské knihovny pro správu Azure
titleSuffix: Azure Cognitive Services
description: Vytvářejte a spravujte prostředky Azure Cognitive Services pomocí klientské knihovny pro správu Azure.
services: cognitive-services
keywords: služby pro rozpoznávání, rozpoznávání, rozpoznávání, rozpoznávání, služby AI
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472151"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Rychlý Start: vytvoření prostředku Cognitive Services pomocí klientské knihovny pro správu Azure

Tento rychlý Start slouží k vytváření a správě prostředků Azure Cognitive Services pomocí klientské knihovny pro správu Azure.

Azure Cognitive Services je skupina cloudových služeb s rozhraními REST API a klientskými knihovnami, které umožňují vývojářům sestavovat do svých aplikací rozpoznávání informací. Vývojáři k dosažení úspěchu nepotřebují přímým umělou Intelligence (AI) nebo dovednosti v oblasti datových věd nebo znalosti. Azure Cognitive Services umožňuje vývojářům snadno přidat funkce rozpoznávání do svých aplikací s rozpoznáváním řešení, která mohou vidět, slyšet, mluvit, pochopit a dokonce i začít.

Jednotlivé služby AI jsou reprezentovány [prostředky](../azure-resource-manager/management/manage-resources-portal.md) Azure, které vytvoříte v rámci vašeho předplatného Azure. Po vytvoření prostředku můžete k ověřování aplikací použít klíče a vygenerované koncové body.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
