---
title: Co je rozhraní API pro vyhledávání entit Bingu?
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro vyhledávání entit Bingmůžete extrahovat a vyhledávat entity a místa z vyhledávacích dotazů.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75384515"
---
# <a name="what-is-bing-entity-search-api"></a>Co je rozhraní API pro vyhledávání entit Bingu?

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. Bing vrátí místa, pokud dotaz určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz určuje známé osoby, místa (turistické atrakce, státy, země nebo oblasti atd.) nebo věci.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání v reálném čase](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Poskytněte návrhy hledání, které lze zobrazit jako rozevírací seznam podle typu uživatelů.       | 
| [Rozdvojení entity](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Získejte více entit pro dotazy s více možnými významy. |
| [Hledání míst](concepts/search-for-entities.md#find-places) | Vyhledávání a vracení informací o místních podnicích a subjektech  |

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro vyhledávání entit Bingu je webová služba RESTful, která usnadňuje volání z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON. Tuto službu můžete využívat pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešlete do rozhraní API požadavek s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) rozhraní API pro vyhledávání entit Bingu. 
* Chcete-li s prvním požadavkem rychle začít, vyzkoušejte [úvodní příručku](quickstarts/csharp.md).
* Referenční část [rozhraní API pro vyhledávání entit Bingu v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
* Navštivte [stránku centra rozhraní API pro vyhledávání Binga a](../bing-web-search/search-the-web.md) prozkoumejte další dostupná rozhraní API.