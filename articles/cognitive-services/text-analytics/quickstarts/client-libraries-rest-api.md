---
title: 'Rychlý Start: dolování textu pomocí klientské knihovny Analýza textu'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete provádět analýzy mínění a další informace pomocí rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: aahi
keywords: dolování textu, analýza míněníí, analýza textu
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 527936e1da385a3622358fe12efa0f87af4b3fd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765048"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Rychlý Start: použití klientské knihovny Analýza textu a REST API

Pomocí tohoto článku můžete začít s Analýza textu klientské knihovny a REST API. Pomocí těchto kroků můžete vyzkoušet ukázkový kód pro text dolování:

* Analýza mínění
* Dolování názoru
* Rozpoznávání jazyka
* rozpoznávání entit,
* Rozpoznávání osobních údajů pro identifikaci
* Extrakce klíčových frází


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Nejnovější stabilní verze rozhraní API pro analýzu textu je `3.0` .
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.
> * Pokud chcete použít analýza textu pro stav nebo asynchronní operace, přečtěte si příklady na GitHubu pro [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) nebo [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) .

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Nejnovější stabilní verze rozhraní API pro analýzu textu je `3.0` .
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.
Pokud chcete použít analýza textu pro stav nebo asynchronní operace, přečtěte si příklady na GitHubu pro [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) nebo [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) .

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Nejnovější stabilní verze rozhraní API pro analýzu textu je `3.0` .
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.
> * Tuto verzi klientské knihovny Analýza textu můžete také spustit [v prohlížeči](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Nejnovější stabilní verze rozhraní API pro analýzu textu je `3.0` .
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže. Pokud chcete použít analýza textu pro stav nebo asynchronní operace, přečtěte si příklady na GitHubu pro [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) nebo [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) .

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Nejnovější stabilní verze rozhraní API pro analýzu textu je `3.0` .
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumat řešení](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Přehled analýzy textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rozpoznání jazyka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
