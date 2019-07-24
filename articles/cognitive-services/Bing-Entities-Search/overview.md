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
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 8f43401296a154ee40e7c214ad63da878129244a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424011"
---
# <a name="what-is-bing-entity-search-api"></a>Co je rozhraní API Bingu pro vyhledávání entit?

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. Bing vrátí místa, pokud dotaz určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz určuje dobře známé lidi, místa (turistické attractions, státy, země/oblasti atd.) nebo věci.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání v reálném čase](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Poskytněte návrhy hledání, které se dají jako typ uživatele zobrazovat jako rozevírací seznam.       | 
| [Nejednoznačnost entit](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Získejte více entit pro dotazy s více možnými významy. |
| [Najít místa](concepts/search-for-entities.md#find-places) | Hledání a vracení informací o místních firmách a entitách  |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání entit je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON. Službu můžete použít buď pomocí REST API, nebo pomocí sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odešle požadavek do rozhraní API s platným vyhledávacím dotazem.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

* Vyzkoušejte si [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) pro rozhraní API Bingu pro vyhledávání entit. 
* Pokud chcete rychle začít s vaším prvním požadavkem, vyzkoušejte si [rychlý Start](quickstarts/csharp.md).
* Referenční část [rozhraní API Bingu pro vyhledávání entit v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
