---
title: Co jsou Automatické návrhy Bingu?
titlesuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: c7ac631ded5d781b2d2949d65f6197e194521055
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268919"
---
# <a name="what-is-bing-autosuggest"></a>Co jsou Automatické návrhy Bingu?

Pokud vaše aplikace posílá dotazy na některý z rozhraní API pro vyhledávání Bingu, můžete zlepšit možnosti vašich uživatelů hledání rozhraní API pro automatické návrhy Bingu. Rozhraní API pro automatické návrhy Bingu vrátí seznam navrhovaných dotazů na základě řetězce dotazu částečné do vyhledávacího pole. Jak znaky do vyhledávacího pole, můžete zobrazit návrhy v rozevíracím seznamu.

## <a name="bing-autosuggest-api-features"></a>Funkce rozhraní API pro automatické návrhy Bingu

| Funkce                                                                                                                                                                                 | Popis                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Navrhování hledaných termínů v reálném čase](concepts/get-suggestions.md) | Vylepšit aplikaci pomocí rozhraní API pro automatické návrhy pro zobrazení navrhované hledané výrazy, jako jste zadali. |

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro automatické návrhy Bingu je RESTful webová služba, volání z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. 

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešle žádost do tohoto rozhraní API pokaždé, když uživatel zadá znak nového vyhledávacího pole vaší aplikace.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

Obvykle by volat toto rozhraní API pokaždé, když uživatel zadá znak nového vyhledávacího pole vaší aplikace. Jako další znaky, rozhraní API vrátí relevantnější navrhované vyhledávací dotazy. Například může vrátit návrhy rozhraní API pro jeden `s` můžou mít méně závažné než těch, které jsou pro `sail`.

Následující příklad ukazuje rozevíracího seznamu vyhledávací pole s navrhované výrazy z rozhraní API pro automatické návrhy Bingu.

![Rozevírací seznam vyhledávacího pole s automatickými návrhy](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Když uživatel vybere z rozevíracího seznamu návrh, můžete ji použít k zahájení hledání s jedním z rozhraní API pro vyhledávání Bingu nebo přímo přejít na stránku výsledků vyhledávání Bingu.

## <a name="next-steps"></a>Další postup

Pokud chcete rychle začít s vaším prvním požadavkem, projděte si popis [vytvoření prvního dotazu](quickstarts/csharp.md).

Seznamte se s referenčními informacemi k [rozhraní API pro automatické návrhy Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Tyto referenční informace obsahují seznam koncových bodů, hlaviček a parametrů dotazů, které můžete použít při odesílání požadavků na navrhované výrazy dotazů, a definice objektů odpovědi.

Naučte prohledávat web s využitím [rozhraní API Bingu pro vyhledávání na webu](../bing-web-search/search-the-web.md).

Nezapomeňte si přečíst [požadavky Bingu na zobrazení a použití](./useanddisplayrequirements.md), abyste neporušili žádná pravidla používání výsledků hledání.
