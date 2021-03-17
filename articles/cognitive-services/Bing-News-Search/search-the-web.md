---
title: Co je rozhraní API Bingu pro vyhledávání zpráv?
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API Bingu pro vyhledávání zpráv k prohledávání webu pro aktuální titulky napříč kategoriemi, včetně titulků a témat trendů.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c6fad3a006d2f3da74638e0680d6ba65f736ab7b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351227"
---
# <a name="what-is-the-bing-news-search-api"></a>Co je rozhraní API Bingu pro vyhledávání zpráv?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro vyhledávání zpráv usnadňuje integraci funkcí vyhledávání zpráv Bingu ve vašich aplikacích. Rozhraní API nabízí podobné možnosti [zpráv Bingu](https://www.bing.com/news), které vám umožní odeslat vyhledávací dotazy a získat relevantní články s novinkami.

Uvědomte si, že rozhraní API Bingu pro vyhledávání zpráv poskytuje pouze výsledky hledání zpráv. Pro jiné typy webového obsahu použijte [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/overview.md) [vyhledávání videí API](../bing-video-search/overview.md) a [rozhraní API vyhledávání obrázků](../bing-image-search/overview.md) .

## <a name="bing-news-search-api-features"></a>rozhraní API Bingu pro vyhledávání zpráv funkce

I když rozhraní API Bingu pro vyhledávání zpráv primárně najde a vrátí relevantní články s příspěvky, nabízí několik funkcí pro inteligentní a cílené načítání zpráv na webu.

|Funkce  |Popis  |
|---------|---------|
|[Navrhování a používání hledaných výrazů](concepts/search-for-news.md#suggest-and-use-search-terms)     | Vylepšete možnosti hledání pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) k zobrazení navrhovaných hledaných výrazů při jejich psaní.         |
|[Získat obecné novinky](concepts/search-for-news.md#get-general-news)     | Vyhledá novinky odesláním vyhledávacího dotazu na rozhraní API Bingu pro vyhledávání zpráv a vrátí seznam relevantních novinek.           |
|[Dnešní hlavní zprávy](concepts/search-for-news.md#get-todays-top-news)      | Získejte nejdůležitější novinky ve všech kategoriích.       |
|[Zprávy podle kategorie](concepts/search-for-news.md)     | Hledání zpráv v konkrétních kategoriích.        | 
|[Nadpis zprávy](concepts/search-for-news.md)     | Vyhledejte horních nadpisů ve všech kategoriích.         |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání zpráv je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/free/cognitive-services/).
2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) pro rozhraní API Bingu pro vyhledávání zpráv. Tato ukázka vám ukáže, jak rychle upravit vyhledávací dotaz a najít novinky na webu.

Pokud chcete rychle začít s prvním požadavkem na rozhraní API, vyzkoušejte si rychlý Start pro [REST API](./csharp.md) nebo jednu ze [sad SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp).

## <a name="see-also"></a>Viz také

* Oddíl Reference [rozhraní API Bingu pro vyhledávání zpráv v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) obsahuje definice a informace o koncových bodech, hlavičkách, ODPOVĚDÍCH rozhraní API a parametrech dotazů, které můžete použít k vyžádání výsledků hledání na základě bitové kopie.
* [Požadavky Bingu na zobrazení a použití](../bing-web-search/use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/overview.md) a Prozkoumejte další dostupná rozhraní API.