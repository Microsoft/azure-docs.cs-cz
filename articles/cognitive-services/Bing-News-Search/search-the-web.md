---
title: Co je rozhraní API pro vyhledávání zpráv Bingu?
titlesuffix: Azure Cognitive Services
description: Další informace o použití rozhraní API pro vyhledávání zpráv Bingu pro vyhledávání na webu pro aktuální titulky v několika kategoriích, včetně titulky a sledování trendů témata.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 32c18508d07fc911366ffc77ebe347efd3c1b6fa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261447"
---
# <a name="what-is-the-bing-news-search-api"></a>Co je rozhraní API pro vyhledávání zpráv Bingu?

Rozhraní API pro vyhledávání zpráv Bingu umožňuje snadno do svých aplikací integrovat funkce vyhledávání Bingu cognitive zprávy. Rozhraní API poskytuje podobné možnosti jako na [zprávy Bing](https://www.bing.com/news), že vám dovolí vyhledávací dotazy odesílat a přijímat relevantní novinky články.

Mějte na paměti, že rozhraní API pro vyhledávání zpráv Bingu poskytuje jen výsledky hledání zpráv. Použití [API vyhledávání na webu Bingu](../bing-web-search/search-the-web.md), [API pro vyhledávání videí](../bing-video-search/search-the-web.md) a [API pro vyhledávání obrázků](../bing-image-search/overview.md) pro ostatní typy webového obsahu.

## <a name="bing-news-search-api-features"></a>Funkce rozhraní API pro vyhledávání zpráv Bingu

Rozhraní API pro vyhledávání zpráv Bingu primárně najde a vrátí odpovídající vybrané články, poskytuje několik funkcí pro načítání zpráv inteligentní a cílené na webu.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy a pomocí hledané termíny](concepts/search-for-news.md#suggest-and-use-search-terms)     | Zlepšení možností vyhledávání pomocí [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md) zobrazíte navrhované hledané výrazy, jako jste zadali.         |
|[Dostávat obecné informace](concepts/search-for-news.md#get-general-news)     | Hledání zpráv tak, že odesílání vyhledávací dotaz na rozhraní API pro vyhledávání zpráv Bingu a návrat seznam článků relevantní novinky.           |
|[Dnešní hlavní zprávy](concepts/search-for-news.md#get-todays-top-news)      | Získání nejnovějších zpráv za den, ve všech kategoriích.       |
|[Zprávy podle kategorie](concepts/search-for-news.md)     | Hledání zpráv v konkrétní kategorie.        | 
|[Nadpis zprávy](concepts/search-for-news.md)     | Hledání hlavní titulky ve všech kategoriích.         |

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro vyhledávání zpráv Bingu je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. Můžete použít službu pomocí rozhraní REST API nebo sady SDK.

1. Vytvoření účtu rozhraní API služeb Cognitive Services s přístupem k rozhraní API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si [zdarma vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Odeslat požadavek na rozhraní API pomocí platný vyhledávacího dotazu.

3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

Nejprve zkuste [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) pro rozhraní API pro vyhledávání zpráv Bingu. Tato ukázka ukazuje, jak můžete rychle přizpůsobení vyhledávací dotaz a hledání zpráv na webu.

Pokud chcete rychle začít s první žádosti o rozhraní API, vyzkoušejte si rychlé zprovoznění pro [rozhraní REST API](quickstart.md) nebo jeden z [sady SDK](sdk.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [API Bingu pro vyhledávání zpráv v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) odkaz na oddíl obsahuje definice a informace o koncových bodech, záhlaví, odpovědi rozhraní API a parametry dotazu, které slouží k vyžádání výsledky hledání založené na bitové kopii.

* [Požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
