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
ms.openlocfilehash: 52e3e65fb4f42530373decca547674a28a60f759
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446662"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co je rozhraní API pro vizuální vyhledávání Bingu?

Rozhraní API vizuálního vyhledávání Bingu vrací přehledy pro bitovou kopii. Můžete nahrát obrázek nebo zadat adresu URL. Přehledy jsou vizuálně podobné obrázky, nákupní zdroje, webové stránky, které obrázek obsahují, a další. Přehledy vrácené rozhraním API pro vizuální vyhledávání Bingu jsou podobné těm, které jsou zobrazeny na Bing.com/images. 

Pokud používáte [rozhraní API pro vyhledávání obrázků Bingu](../bing-image-search/overview.md), můžete místo nahrání obrázku použít tokeny přehledu z výsledků vyhledávání v tomto rozhraní API pro vizuální vyhledávání Bingu.

> [!IMPORTANT]
> Pokud získáte přehledy obrázků pomocí rozhraní API pro vyhledávání obrázků Bingu, zvažte přepnutí na rozhraní API pro vizuální vyhledávání Bingu, které poskytuje komplexnější přehledy.

## <a name="insights"></a>Insights

Pomocí vizuálního vyhledávání Bingu můžete zjistit následující přehledy:

| Přehled                              | Popis |
|--------------------------------------|-------------|
| Vizuálně podobné obrázky              | Seznam obrázků, které jsou vizuálně podobné vstupnímu obrazu. |
| Vizuálně podobné produkty            | Produkty, které jsou vizuálně podobné zobrazenému produktu.            |
| Nákupní zdroje                     | Místa, kde si můžete koupit položku zobrazenou na vstupním obrázku.            |
| Související vyhledávání                     | Související vyhledávání provedená jinými uživateli nebo, která jsou založena na obsahu obrázku.            |
| Webové stránky, které obsahují obrázek     | Webové stránky, které obsahují vstupní obraz.            |
| Recepty                              | Webové stránky, které obsahují recepty na výrobu misky zobrazené na vstupním obrázku.            |
| Entity                             | Známí lidé, místa a věci. |

Kromě přehledů vrátí vizuální vyhledávání Bingu různé termíny (tj. značky) odvozené ze vstupního obrazu. Značky umožňují uživatelům prozkoumat koncepty nalezené v bitové kopii. Pokud je například vstupní obrázek slavného sportovce, může být jedním z tagů název sportovce, může být další značkou Sport. Nebo pokud je vstupní obrázek jablečného koláče, tagy mohou být Apple Pie, Pies a Dezerty.

Výsledky vizuálního vyhledávání Bingu také obsahují ohraničovací rámečky pro oblasti zájmu v obrázku. Pokud například obrázek obsahuje několik celebrit, výsledky mohou obsahovat ohraničovací rámečky pro každou z rozpoznaných celebrit. Nebo pokud Bing rozpozná produkt nebo oblečení v obrázku, výsledek může obsahovat ohraničovací rámeček pro rozpoznanou položku.

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro vizuální vyhledávání Bingu je webová služba RESTful, která usnadňuje volání z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON. Pro službu můžete použít rozhraní REST API nebo sadu SDK.

1. Vytvořte [účet služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přístup k vyhledávacím apim Bingu. Pokud nemáte předplatné Azure, můžete [si vytvořit účet zdarma](https://azure.microsoft.com/free/). Klíč předplatného můžete získat z [webu Azure po](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) vytvoření účtu nebo webu [Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) po aktivaci bezplatné zkušební verze.
2. Odešlete požadavek do rozhraní API s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte interaktivní [ukázku rozhraní](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)API pro vizuální vyhledávání Bingu .
Ukázka ukazuje, jak můžete rychle přizpůsobit vyhledávací dotaz a prohledat web pro obrázky.

Chcete-li začít rychle s prvním požadavkem, podívejte se na rychlé starty: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Viz také

* [Odkaz Obrázky – vizuální hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) popisuje definice a informace o koncových bodech, záhlavích požadavků, odpovědích a parametrech dotazu, které můžete použít k vyžádání výsledků hledání na základě obrázků.

* Požadavky [na použití a zobrazení rozhraní API pro vyhledávání Bingu](../bing-web-search/use-display-requirements.md) určují přijatelná použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bing.

* Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.