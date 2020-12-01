---
title: Automatické navrhování podmínek vyhledávání – rozhraní API Bingu pro vyhledávání na webu
titleSuffix: Azure Cognitive Services
description: Spárovat rozhraní API Bingu pro vyhledávání na webu s rozhraní API pro automatické návrhy Bingu a poskytnout uživatelům vylepšené možnosti vyhledávání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 1a3f479fbbe68c8880cd7fefb3b57c77d4cfbbfe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349567"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatické navrhování vyhledávacích podmínek Bingu v aplikaci

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Po zadání hledaného výrazu musí být adresa URL zakódovaná, než se nastaví parametr dotazu [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) . Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

Pokud termín dotazu obsahuje pravopisnou chybu, odpověď vyhledávání zahrnuje objekt [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) . Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Tyto informace můžete použít, chcete-li uživateli upozornit, že jste při zobrazení výsledků hledání změnili jejich řetězec dotazu.

![Příklad uživatelského rozhraní kontextu dotazu](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Další kroky  

* Zkontrolujte ukázkové [rozhraní API Bingu pro vyhledávání na webu odpovědi](search-responses.md).  

## <a name="see-also"></a>Viz také  

* [Odkaz na rozhraní API Bingu pro vyhledávání na webu](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)