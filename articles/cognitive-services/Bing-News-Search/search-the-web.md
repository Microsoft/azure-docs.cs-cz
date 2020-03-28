---
title: Co je rozhraní API Bingu pro vyhledávání zpráv?
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak pomocí rozhraní API pro vyhledávání zpráv Bing hledat na webu aktuální nadpisy napříč kategoriemi, včetně nadpisů a populárních témat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e0e99c7f677173c64afad3109b2f4accd7cb3cb9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448450"
---
# <a name="what-is-the-bing-news-search-api"></a>Co je rozhraní API Bingu pro vyhledávání zpráv?

Rozhraní API pro vyhledávání zpráv bingu usnadňuje integraci funkcí vyhledávání kognitivních zpráv bingu do vašich aplikací. Rozhraní API poskytuje podobné prostředí [jako Zprávy Bingu](https://www.bing.com/news), což umožňuje odesílat vyhledávací dotazy a přijímat relevantní diskusní články.

Uvědomte si, že rozhraní API pro vyhledávání zpráv Bingu poskytuje pouze výsledky vyhledávání zpráv. Pro jiné typy webového obsahu použijte [rozhraní API pro vyhledávání na webu Bingu](../bing-web-search/search-the-web.md), rozhraní API pro vyhledávání [videa](../bing-video-search/search-the-web.md) a [rozhraní API pro vyhledávání obrázků.](../bing-image-search/overview.md)

## <a name="bing-news-search-api-features"></a>Funkce rozhraní API pro vyhledávání zpráv bingu

Zatímco rozhraní API pro vyhledávání zpráv Bingu primárně vyhledává a vrací relevantní zpravodajské články, poskytuje několik funkcí pro inteligentní a cílené získávání zpráv na webu.

|Funkce  |Popis  |
|---------|---------|
|[Navrhování a používání hledaných výrazů](concepts/search-for-news.md#suggest-and-use-search-terms)     | Vylepšete vyhledávání pomocí [rozhraní API automatického návrhu Bingu](../bing-autosuggest/get-suggested-search-terms.md) k zobrazení doporučených hledaných výrazů při jejich psaní.         |
|[Získejte obecné novinky](concepts/search-for-news.md#get-general-news)     | Najděte novinky odesláním vyhledávacího dotazu do rozhraní API pro vyhledávání zpráv Bingu a získáním seznamu relevantních zpravodajských článků.           |
|[Dnešní top novinky](concepts/search-for-news.md#get-todays-top-news)      | Získejte nejlepší novinky pro tento den, ve všech kategoriích.       |
|[Novinky podle kategorie](concepts/search-for-news.md)     | Hledat novinky v konkrétních kategoriích.        | 
|[Hlavní titulky](concepts/search-for-news.md)     | Vyhledejte hlavní nadpisy ve všech kategoriích.         |

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro vyhledávání zpráv Bingu je webová služba RESTful, která usnadňuje volání z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte účet rozhraní API služby Cognitive Services s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete [si vytvořit účet zdarma](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.

3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Nejprve vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) rozhraní API pro vyhledávání zpráv Bingu. Tato ukázka ukazuje, jak můžete rychle přizpůsobit vyhledávací dotaz a najít novinky na webu.

Chcete-li rychle začít s prvním požadavkem na rozhraní API, vyzkoušejte rychlý start pro [rozhraní REST API](quickstart.md) nebo některou z sad [SDK](sdk.md).

## <a name="see-also"></a>Viz také

* Referenční část [rozhraní API pro vyhledávání zpráv Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) obsahuje definice a informace o koncových bodech, záhlavích, odpovědích rozhraní API a parametrech dotazu, které můžete použít k vyžádání výsledků hledání na základě obrázků.
* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
* Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.