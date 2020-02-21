---
title: 'Rychlý Start: Klientská knihovna Analýza textu V3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete své aplikace připojit k rozhraní API pro analýzu textu z Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/23/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 79dd6160b65469d6a44167913adb7e319f0f583a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525341"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Rychlý Start: použití klientské knihovny Analýza textu

Začněte s klientskou knihovnou Analýza textu. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu Analýza textu k provedení těchto akcí:

* Analýza mínění
* Rozpoznávání jazyka
* Rozpoznávání entit
* Extrakce klíčových frází

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Nejnovější verze Preview rozhraní API pro analýzu textu je `3.0-preview`, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Nejnovější stabilní verze je `2.1`.
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Tento rychlý Start je určen pouze pro `3.0-preview` verze Analýza textu klientské knihovny, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Nejnovější verze Preview rozhraní API pro analýzu textu je `3.0-preview`, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Nejnovější stabilní verze je `2.1`.
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Nejnovější verze Preview rozhraní API pro analýzu textu je `3.0-preview`, která zahrnuje verzi Public Preview pro vylepšené [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) a [rozpoznávání pojmenovaných entit (ner)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Nejnovější stabilní verze je `2.1`.
>    * Nezapomeňte pouze postupovat podle pokynů pro verzi, kterou používáte.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Pro produkční scénáře doporučujeme použití dávkových asynchronních metod pro výkon a škálovatelnost. Další informace najdete v referenční dokumentaci níže. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Podpora dalších jazyků

Pokud jste na tuto kartu klikli, pravděpodobně jste v oblíbeném programovacím jazyce nezobrazili rychlý Start. Nedělejte si starosti, máme k dispozici další rychlé starty. Použijte tabulku k vyhledání správné ukázky pro programovací jazyk.

| Jazyk | Dostupná verze | 
|----------|------------------------|
| Ruby     | [Verze 2,1](ruby-sdk.md) | 
| Přejít       | [Verze 2,1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumat řešení](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Přehled rozhraní API pro analýzu textu](../overview.md)
* [Analýza mínění](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání entit](../how-tos/text-analytics-how-to-entity-linking.md)
* [Zjistit jazyk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznávání jazyka](../how-tos/text-analytics-how-to-language-detection.md)
