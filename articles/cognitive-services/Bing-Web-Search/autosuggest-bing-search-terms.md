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
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384865"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatické navrhování vyhledávacích podmínek Bingu v aplikaci

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Po zadání hledaného výrazu musí být adresa URL zakódovaná, než se nastaví parametr dotazu [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) . Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

Pokud termín dotazu obsahuje pravopisnou chybu, odpověď vyhledávání zahrnuje objekt [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) . Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

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

* [Odkaz na rozhraní API Bingu pro vyhledávání na webu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
