---
title: Nejčastější dotazy (FAQ) – pro automatické návrhy Bingu rozhraní API
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na běžné dotazy týkající se rozhraní API pro automatické návrhy Bingu.
services: cognitive-services
author: HeidiSteen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: cc14ceafaf0d9dd913cdef3864b0391f05d721e4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882246"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Časté dotazy (FAQ) o Bing API pro automatické návrhy
 
 Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se rozhraní API pro automatické návrhy pro Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Jak se při volání rozhraní API pro automatické návrhy Bingu z jazyka JavaScript získat záhlaví volitelný klientský?

Následující hlavičky jsou volitelné, ale doporučujeme vám, že jste s nimi zacházet podle potřeby. Tyto hlavičky pomoct vrátit přesnější výsledky API pro automatické návrhy Bingu.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Ale při volání rozhraní API pro automatické návrhy Bingu z jazyka JavaScript v prohlížeči integrovaných funkcí zabezpečení by mohly bránit můžete přístup k hodnoty z těchto záhlaví.

Chcete-li tento problém vyřešit, můžete provést požadavek na rozhraní API pro automatické návrhy Bingu prostřednictvím proxy serveru CORS. Odpověď od takový proxy server má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi seznamů povolených a zpřístupňuje je pro jazyk JavaScript.

Je snadné k instalaci proxy CORS a povolit naše [ukázková aplikace](tutorials/autosuggest.md) záhlaví volitelný klientský přístup. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Potom zadejte následující příkaz z příkazového řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte v souboru HTML, aby koncový bod rozhraní API pro automatické návrhy Bingu:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="next-steps"></a>Další postup

Je vaše otázky týkající se chybějící funkce nebo funkce? Vezměte v úvahu požádat nebo pro ni hlasovat o našich [webu User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Další informace najdete v tématech

- [Přetečení zásobníku: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
