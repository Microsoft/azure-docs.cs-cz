---
title: Co jsou Automatické návrhy Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API automatického návrhu bingu vrátí seznam navržených dotazů na základě řetězce částečného dotazu ve vyhledávacím poli.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a90fa0a66fb32b2a885599f09458964188353880
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448836"
---
# <a name="what-is-bing-autosuggest"></a>Co jsou Automatické návrhy Bingu?

Pokud vaše aplikace odesílá dotazy na některý z rozhraní API vyhledávání Bing, můžete použít rozhraní API automatického návrhu Bingu ke zlepšení možnosti vyhledávání uživatelů. Rozhraní API automatického návrhu bingu vrátí seznam navržených dotazů na základě řetězce částečného dotazu ve vyhledávacím poli. Při zadávání znaků do vyhledávacího pole můžete zobrazit návrhy v rozevíracím seznamu.

## <a name="bing-autosuggest-api-features"></a>Funkce rozhraní API automatického návrhu bingu

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/get-suggestions.md) | Vylepšete prostředí aplikací pomocí rozhraní Autosuggest API k zobrazení doporučených hledaných výrazů při psaní. |

## <a name="workflow"></a>Pracovní postup

Bing Autosuggest API je restful webové služby, snadno volat z libovolného programovacího jazyka, který může protokolovat požadavky HTTP a analyzovat JSON. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete požadavek na toto rozhraní API pokaždé, když uživatel zadá nový znak do vyhledávacího pole aplikace.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

Obvykle byste volat toto rozhraní API pokaždé, když uživatel zadá nový znak ve vyhledávacím poli aplikace. Při zadání více znaků vrátí rozhraní API relevantnější navrhované vyhledávací dotazy. Například návrhy rozhraní API může `s` vrátit pro jeden jsou pravděpodobně `sail`méně relevantní než ty pro .

Následující příklad ukazuje rozevírací vyhledávací pole s navrženými termíny dotazu z rozhraní API automatického návrhu Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Když uživatel vybere návrh z rozevíracího seznamu, můžete ho použít k zahájení vyhledávání pomocí jednoho z rozhraní API vyhledávání Bingu nebo přímo přejít na stránku s výsledky hledání Bingu.

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si popis [vytvoření prvního dotazu](quickstarts/csharp.md).

Seznamte se s referenčními informacemi k [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na navrhované výrazy dotazů, a definice objektů odpovědi.

Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.


Naučte se prohledávat web pomocí [rozhraní API pro vyhledávání na webu Bingu](../bing-web-search/search-the-web.md)a prozkoumejte další rozhraní API pro vyhledávání[Bingu](../bing-web-search/index.yml).

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
