---
title: Co je rozhraní API Bingu pro vyhledávání entit?
titleSuffix: Azure Cognitive Services
description: K extrakci a hledání entit a míst z vyhledávacích dotazů použijte rozhraní API Bingu pro vyhledávání entit.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75384515"
---
# <a name="what-is-bing-entity-search-api"></a>Co je rozhraní API Bingu pro vyhledávání entit?

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. Bing vrátí místa, pokud dotaz určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz určuje dobře známé lidi, místa (turistické attractions, státy, země/oblasti atd.) nebo věci.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání v reálném čase](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Poskytněte návrhy hledání, které se dají jako typ uživatele zobrazovat jako rozevírací seznam.       | 
| [Nejednoznačnost entit](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Získejte více entit pro dotazy s více možnými významy. |
| [Najít místa](concepts/search-for-entities.md#find-places) | Hledání a vracení informací o místních firmách a entitách  |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání entit je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) pro rozhraní API Bingu pro vyhledávání entit. 
* Pokud chcete rychle začít s vaším prvním požadavkem, vyzkoušejte si [rychlý Start](quickstarts/csharp.md).
* Referenční část [rozhraní API Bingu pro vyhledávání entit v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
* Navštivte [stránku vyhledávání Bingu centrum rozhraní API](../bing-web-search/search-the-web.md) a Prozkoumejte další dostupná rozhraní API.