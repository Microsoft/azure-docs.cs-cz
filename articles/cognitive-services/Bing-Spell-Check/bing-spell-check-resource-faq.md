---
title: Nejčastější dotazy týkající se Bing pravopisu rozhraní API pro kontrolu - kognitivní služby Azure | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se rozhraní API zkontrolujte služby Bing pravopisu v Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343553"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Nejčastější dotazy týkající se pravopisu Bing zaškrtnutí rozhraní API

 Odpovědi na nejčastější dotazy o konceptech, kód a scénáře související s rozhraním API služby Bing pravopisu zkontrolujte pro kognitivní služby společnosti Microsoft na platformě Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Jak získám volitelný klientský hlavičky, při volání metody Bing pravopisu zkontrolujte API z JavaScriptu?

Následující hlavičky jsou volitelné, ale doporučujeme považovat je podle potřeby. Tyto hlavičky pomoci vráceny přesnější výsledky API kontrola pravopisu Bing.

- Umístění X vyhledávání
- X-MSEdge-ClientID
- X-MSEdge když

Ale při volání API kontrola pravopisu Bing z jazyka JavaScript, funkcí integrované zabezpečení prohlížeče může zabránit vám v přístupu k hodnoty tyto hlavičky.

Chcete-li vyřešit tento problém, můžete provést požadavku Bing pravopisu zkontrolujte API prostřednictvím proxy serveru CORS. Odpověď proxy serveru má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi povolených programů a jejich zpřístupní JavaScript.

Je snadné se má nainstalovat proxy CORS umožňující [kurz aplikace](tutorials/spellcheck.md) hlavičky volitelný klientský přístup. První, pokud ještě nemáte, [instalace softwaru Node.js](https://nodejs.org/en/download/). Potom zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod rozhraní API Bing pravopisu zkontrolujte v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Nakonec spusťte CORS proxy pomocí následujícího příkazu:

    cors-proxy-server

Nechte Otevřete příkazové okno při používání kurz aplikace; zavřením okna zastaví proxy serveru. V rozšíření hlavičky protokolu HTTP části níže výsledky hledání, se nyní zobrazí `X-MSEdge-ClientID` hlavičky (mimo jiné) a ověřte, zda je stejný pro každý požadavek.

## <a name="next-steps"></a>Další postup

Je váš dotaz o chybějící funkce nebo funkce? Zvažte požaduje nebo volíte ho [webu UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Další informace najdete v tématech

 [StackOverflow: Kognitivní služby](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
