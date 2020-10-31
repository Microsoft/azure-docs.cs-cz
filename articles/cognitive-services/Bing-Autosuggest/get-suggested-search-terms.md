---
title: Co jsou Automatické návrhy Bingu?
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě částečného řetězce dotazu ve vyhledávacím poli.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d55ff4ed8717a449628e94a940c0896d31818da2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101931"
---
# <a name="what-is-bing-autosuggest"></a>Co jsou Automatické návrhy Bingu?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Pokud vaše aplikace odesílá dotazy do některého z rozhraní API pro vyhledávání Bingu, můžete pomocí rozhraní API pro automatické návrhy Bingu vylepšit možnosti vyhledávání vašich uživatelů. Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě částečného řetězce dotazu ve vyhledávacím poli. Do vyhledávacího pole se jako znaky zadávají návrhy, které můžete zobrazit v rozevíracím seznamu.

## <a name="bing-autosuggest-api-features"></a>rozhraní API pro automatické návrhy Bingu funkce

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/get-suggestions.md) | Vylepšete prostředí vaší aplikace pomocí rozhraní API pro automatické návrhy, abyste zobrazili navrhované hledané výrazy při psaní. |

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro automatické návrhy Bingu je webová služba RESTful, která se dá snadno volat ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON.

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/free/cognitive-services/).
2. Pokaždé, když uživatel do vyhledávacího pole aplikace zadá nový znak, pošle se mu požadavek na toto rozhraní API.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

Toto rozhraní API obvykle vyvoláte pokaždé, když uživatel zadá do vyhledávacího pole aplikace nový znak. Po zadání více znaků vrátí rozhraní API relevantnější navrhované vyhledávací dotazy. Například návrhy, které může rozhraní API vracet pro jednu z nich, `s` budou pravděpodobně méně relevantní než pro `sail` .

Následující příklad ukazuje rozevírací vyhledávací pole s navrhovanými výrazy dotazu z rozhraní API pro automatické návrhy Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Když uživatel vybere návrh z rozevíracího seznamu, můžete ho použít k zahájení hledání v jednom z rozhraní API pro vyhledávání Bingu, nebo přímo přejít na stránku výsledků hledání Bingu.

## <a name="next-steps"></a>Další kroky

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si popis [vytvoření prvního dotazu](quickstarts/csharp.md).

Seznamte se s referenčními informacemi k [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na navrhované výrazy dotazů, a definice objektů odpovědi.

Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/search-the-web.md) a Prozkoumejte další dostupná rozhraní API.


Naučte se hledat na webu pomocí [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md)a prozkoumat druhý[rozhraní API pro vyhledávání Bingu](../bing-web-search/index.yml).

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
