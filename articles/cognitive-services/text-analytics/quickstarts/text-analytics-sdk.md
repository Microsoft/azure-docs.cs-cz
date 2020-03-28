---
title: 'Úvodní příručka: Klientská knihovna Text Analytics v3 | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start můžete použít k připojení aplikací k rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240135"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Úvodní příručka: Použití klientské knihovny Analýzy textu

Začínáme s klientskou knihovnou Text Analytics. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Pomocí klientské knihovny Text Analytics můžete provádět:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Nejnovější verze rozhraní PREVIEW rozhraní API `3.0-preview`pro analýzu textu je , která obsahuje verzi public preview pro [vylepšenou analýzu mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Nejnovější stabilní verze `2.1`je .
>    * Ujistěte se, že budete postupovat pouze podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a un-zabezpečené úložiště přihlašovacích údajů pro jednoduchost důvodů. Pro produkční scénáře doporučujeme použít dávkové asynchronní metody pro výkon a škálovatelnost. Viz referenční dokumentace níže.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Tento rychlý start je `3.0-preview` určen pouze pro verzi klientské knihovny Text Analytics, která obsahuje veřejný náhled pro vylepšenou [analýzu mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)
> * Kód v tomto článku používá synchronní metody a un-zabezpečené úložiště přihlašovacích údajů pro jednoduchost důvodů. Pro produkční scénáře doporučujeme použít dávkové asynchronní metody pro výkon a škálovatelnost. Viz referenční dokumentace níže.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Nejnovější verze rozhraní PREVIEW rozhraní API `3.0-preview`pro analýzu textu je , která obsahuje verzi public preview pro [vylepšenou analýzu mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Nejnovější stabilní verze `2.1`je .
>    * Ujistěte se, že budete postupovat pouze podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a un-zabezpečené úložiště přihlašovacích údajů pro jednoduchost důvodů. Pro produkční scénáře doporučujeme použít dávkové asynchronní metody pro výkon a škálovatelnost. Viz referenční dokumentace níže.
> * Tuto verzi klientské knihovny Text Analytics můžete také spustit [v prohlížeči](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Nejnovější verze rozhraní PREVIEW rozhraní API `3.0-preview`pro analýzu textu je , která obsahuje verzi public preview pro [vylepšenou analýzu mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Nejnovější stabilní verze `2.1`je .
>    * Ujistěte se, že budete postupovat pouze podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a un-zabezpečené úložiště přihlašovacích údajů pro jednoduchost důvodů. Pro produkční scénáře doporučujeme použít dávkové asynchronní metody pro výkon a škálovatelnost. Viz referenční dokumentace níže. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Další jazyková podpora

Pokud jste klikli na tuto kartu, pravděpodobně jste v oblíbeném programovacím jazyce neuviděli rychlý start. Nebojte se, máme k dispozici další rychlé starty. V tabulce najdete tu správnou ukázku pro váš programovací jazyk.

| Jazyk | Dostupná verze | 
|----------|------------------------|
| Ruby     | [Verze 2.1](ruby-sdk.md) | 
| Přejít       | [Verze 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte řešení](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Přehled analýzy textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjištění jazyka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
