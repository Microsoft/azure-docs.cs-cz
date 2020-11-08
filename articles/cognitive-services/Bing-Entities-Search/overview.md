---
title: Co je rozhraní API Bingu pro vyhledávání entit?
titleSuffix: Azure Cognitive Services
description: Přečtěte si podrobnosti o rozhraní API Bingu pro vyhledávání entit a postup extrakce a hledání entit a míst z vyhledávacích dotazů.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 59f2edd30bd47f5da63cbbe79af1142eaf86a747
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367909"
---
# <a name="what-is-bing-entity-search-api"></a>Co je rozhraní API Bingu pro vyhledávání entit?

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. Bing vrátí místa, pokud dotaz určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz určuje dobře známé lidi, místa (turistické attractions, státy, země/oblasti atd.) nebo věci.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání v reálném čase](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Poskytněte návrhy hledání, které se dají jako typ uživatele zobrazovat jako rozevírací seznam.       | 
| [Nejednoznačnost entit](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Získejte více entit pro dotazy s více možnými významy. |
| [Najít místa](concepts/search-for-entities.md#find-places) | Hledání a vracení informací o místních firmách a entitách  |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání entit je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](../cognitive-services-apis-create-account.md) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/free/cognitive-services/).
2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) pro rozhraní API Bingu pro vyhledávání entit. 
* Pokud chcete rychle začít s vaším prvním požadavkem, vyzkoušejte si [rychlý Start](quickstarts/csharp.md).
* Referenční část [rozhraní API Bingu pro vyhledávání entit v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
* [Požadavky Bingu na zobrazení a použití](../bing-web-search/use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/overview.md) a Prozkoumejte další dostupná rozhraní API.