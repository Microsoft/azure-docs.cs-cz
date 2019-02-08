---
title: Co je API pro vyhledávání entit Bingu?
titlesuffix: Azure Cognitive Services
description: Použití rozhraní API Bingu pro vyhledávání entit extrahovat a vyhledávat entity a místa z vyhledávacích dotazů.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: scottwhi
ms.openlocfilehash: 957a104b8fea7274763f16320cc094ede1f94567
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871829"
---
# <a name="what-is-bing-entity-search-api"></a>Co je API pro vyhledávání entit Bingu?

Rozhraní API Bingu pro vyhledávání entit odešle vyhledávací dotaz do Bingu a načte výsledky, které zahrnují entity a místa. Mezi místa patří například restaurace, hotely nebo jiné místní firmy. Bing vrátí místa, pokud dotaz určuje název místní firmy nebo vyzve k zadání typu podniku (například restaurace v okolí). Bing vrátí entity, pokud dotaz specifikuje dobře známé osobnosti, místa (turistické zajímavosti, státy, země atd.) nebo věci.

|Funkce  |Popis  |
|---------|---------|
|[Návrhy hledání v reálném čase](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Pošlete nám návrhy hledání, které mohou být zobrazeny jako rozevíracího seznamu jako typ uživatele.       | 
| [Odstraňování mnohoznačnosti entity](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Získejte více entit pro dotazy s několika možných významů. |
| [Najít místa](concepts/search-for-entities.md#find-places) | Vyhledání a vrácení informací o místních firmách a entit  |

## <a name="workflow"></a>Pracovní postup

Rozhraní API Bingu pro vyhledávání entit je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON. Můžete použít službu pomocí rozhraní REST API nebo sady SDK.

1. Vytvořte [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pokud nemáte předplatné Azure, můžete si zdarma [vytvořit účet](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).
2. Odeslat požadavek na rozhraní API pomocí platný vyhledávacího dotazu.
3. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

* Zkuste [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) pro rozhraní API Bingu pro vyhledávání entit. 
* Abyste mohli rychle začít s vaší první žádost, zkuste [rychlý Start](quickstarts/csharp.md).
* [API pro vyhledávání entit Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odkazovat na části.
* [Požadavky Bingu na zobrazení a použití](./use-display-requirements.md) určují přijatelné způsoby použití obsahu a informací získaných prostřednictvím rozhraní API pro vyhledávání Bingu.
