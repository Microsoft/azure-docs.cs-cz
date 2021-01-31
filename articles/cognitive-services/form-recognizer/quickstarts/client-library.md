---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů nebo REST API'
titleSuffix: Azure Cognitive Services
description: Použijte klientskou knihovnu pro rozpoznávání formulářů nebo REST API k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: zpracování formulářů, automatizované zpracování dat
ms.openlocfilehash: cc18cc9e54a5a8887ecd3e079285ba5130b88278
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218039"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Rychlý Start: použití klientské knihovny pro rozpoznávání formulářů nebo REST API

Začněte s nástrojem pro rozpoznávání formulářů v jazyce podle vašeho výběru. Nástroj pro rozpoznávání formulářů Azure je softwarová služba, která umožňuje sestavovat automatizovaný software pro zpracování dat pomocí technologie strojového učení. Identifikujte a extrahujte páry text, klíč/hodnota, značky výběru, data tabulky a další z formuláře dokumenty &mdash; . Tato služba obsahuje výstup strukturovaných dat, která zahrnují relace v původním souboru. Nástroj pro rozpoznávání formulářů můžete použít prostřednictvím REST API nebo SDK. Pomocí těchto kroků nainstalujete balíček sady SDK a vyzkoušíte ukázkový kód pro základní úlohy. 

Nástroj pro rozpoznávání formulářů použijte k těmto akcím:

* [Analyzovat rozložení](#analyze-layout)
* [Analyzovat účtenky](#analyze-receipts)
* [Analýza vizitek](#analyze-business-cards)
* [Analyzovat faktury](#analyze-invoices)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
