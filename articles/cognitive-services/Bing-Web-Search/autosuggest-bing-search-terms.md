---
title: Automatické navrhování hledaných výrazů – rozhraní API pro vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Spárujte rozhraní API pro vyhledávání na webu Bing s rozhraním API automatického návrhu bingu a poskytněte uživatelům rozšířené možnosti vyhledávání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "66384865"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatické navrhování vyhledávacích dotazů Bing ve vaší aplikaci

Pokud nabízíte vyhledávací pole, do kterého může uživatel zadat hledaný termín, můžete hledání vylepšit s využitím [rozhraní API pro automatické návrhy Bingu](../bing-autosuggest/get-suggested-search-terms.md). Toto rozhraní API vrací navrhované řetězce dotazů na základě částečné shody hledaných termínů zadávaných uživatelem.

Poté, co uživatel zadá hledaný termín, musí být kódován url před nastavením parametru [dotazu q.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) Pokud uživatel například zadá *sailing dinghies*, nastavte parametr `q` na hodnotu `sailing+dinghies` nebo `sailing%20dinghies`.

Pokud termín dotazu obsahuje pravopisnou chybu, odpověď hledání obsahuje [querycontext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objekt. Objekt zobrazí původní pravopis a opravený pravopis použitý pro vyhledávání Bingu.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Tyto informace můžete dát uživateli vědět, že jste změnili řetězec dotazu při zobrazení výsledků hledání.

![Příklad uživatelského kontextu dotazu](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Další kroky  

* Prohlédněte si ukázkové [odpovědi rozhraní API pro vyhledávání na webu Bingu](search-responses.md).  

## <a name="see-also"></a>Viz také  

* [Odkaz na rozhraní API webového vyhledávání Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
