---
title: Co je rozhraní API pro vizuální vyhledávání Bingu?
titleSuffix: Azure Cognitive Services
description: Vizuální vyhledávání Bingu poskytuje podrobnosti nebo přehledy o obrázku, například podobné obrázky nebo nákupní zdroje.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 7dfc704fb38550993adb7835d4500dee890117a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486976"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co je rozhraní API pro vizuální vyhledávání Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API pro vizuální vyhledávání Bingu vrátí přehledy pro obrázek. Můžete buď nahrát obrázek, nebo zadat adresu URL k jednomu. Přehledy jsou vizuálně podobné obrázky, nákupní zdroje, webové stránky, které obsahují obrázek, a další. Přehledy vrácené rozhraní API pro vizuální vyhledávání Bingu jsou podobné těm, které jsou zobrazené na Bing.com/images. 

Pokud použijete [rozhraní API Bingu pro vyhledávání obrázků](../bing-image-search/overview.md), můžete použít tokeny Insight z výsledků hledání rozhraní API pro vizuální vyhledávání Bingu místo nahrání obrázku.

> [!IMPORTANT]
> Pokud získáte přehledy imagí pomocí rozhraní API Bingu pro vyhledávání obrázků, zvažte přechod na rozhraní API pro vizuální vyhledávání Bingu, který poskytuje komplexnější přehledy.

## <a name="insights"></a>Insights

Pomocí Vizuální vyhledávání Bingu můžete zjistit následující přehledy:

| Přehled                              | Popis |
|--------------------------------------|-------------|
| Vizuálně podobné obrázky              | Seznam obrázků, které jsou vizuálně podobné vstupní imagi. |
| Vizuálně podobné produkty            | Produkty, které jsou vizuálně podobné uvedenému produktu.            |
| Nákupy zdrojů                     | Místa, kde si můžete koupit položku zobrazenou ve vstupní imagi.            |
| Související hledání                     | Související hledání provedené jinými uživateli nebo, která jsou založena na obsahu obrázku.            |
| Webové stránky, které obsahují obrázek     | Webové stránky, které obsahují vstupní image.            |
| Návody                              | Webové stránky, které obsahují recepty pro vytváření misky ve vstupní imagi.            |
| Entity                             | Dobře známé osoby, místa a věci. |

Kromě přehledů Vizuální vyhledávání Bingu vrací různé výrazy (tj. značky) odvozené ze vstupní image. Značky umožňují uživatelům prozkoumat koncepty nalezené v imagi. Pokud je vstupní obrázek například slavných Athlete, jedna z značek může být název Athlete, další značka by mohla být sportovní. Nebo, pokud je vstupní obrázek z Apple výsečového grafu, značky by mohly být Apple koláče, koláče a desserts.

Výsledky Vizuální vyhledávání Bingu také obsahují ohraničující pole pro oblasti zájmu v obrázku. Pokud například obrázek obsahuje několik celebrit, mohou výsledky zahrnovat ohraničovací rámečky pro každou rozpoznanou celebrit. Nebo pokud Bing rozpoznává produkt nebo oděvy v imagi, výsledek může obsahovat ohraničovací rámeček pro rozpoznanou položku.

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro vizuální vyhledávání Bingu je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Pro službu můžete použít buď REST API, nebo sadu SDK.

1. Pro přístup k rozhraní API pro vyhledávání Bingu vytvořte [účet Cognitive Services](../cognitive-services-apis-create-account.md) . Pokud nemáte předplatné Azure, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/cognitive-services/).
2. Odešle požadavek do rozhraní API platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte rozhraní API pro vizuální vyhledávání Bingu [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Ukázka vám ukáže, jak rychle upravit vyhledávací dotaz a projeďte web pro obrázky.

Pokud chcete rychle začít s prvním požadavkem, přečtěte si rychlý Start:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Viz také

* Odkazy na [vizuální vyhledávání obrázky](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) popisují definice a informace o koncových bodech, hlavičkách požadavků, odpovědích a parametrech dotazů, které můžete použít k vyžádání výsledků hledání na základě bitové kopie.

* [Požadavky na použití a zobrazení rozhraní api vyhledávání Bingu](../bing-web-search/use-display-requirements.md) určují přijatelné využití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.

* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/overview.md) a Prozkoumejte další dostupná rozhraní API.