---
title: Časté otázky (FAQ) k hledání webové služby Bing rozhraní API v Azure | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se Microsoft kognitivní služby Bing webové rozhraní API Search v Azure.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342461"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Nejčastější dotazy (FAQ) o Bing webové rozhraní API služby Search (kognitivní služby)
 
 Odpovědi na nejčastější dotazy o konceptech, kód a scénáře související s rozhraním API služby Bing webové vyhledávání pro kognitivní služby společnosti Microsoft na platformě Azure.

## <a name="response-headers-in-javascript"></a>Hlavičky odpovědi v jazyce JavaScript

Následující hlavičky může dojít v odpovědi z rozhraní API pro vyhledávání webové služby Bing.

|||
|-|-|
|`X-MSEdge-ClientID`|Jedinečné ID, který Bing přiřazen uživateli|
|`BingAPIs-Market`|Na trhu, který byl použit ke splnění tohoto požadavku|
|`BingAPIs-TraceId`|Záznam protokolu na serveru Bing rozhraní API pro tuto žádost (pro podporu)|

Je zvláště důležité pro zachování ID klienta a vracet s následných žádostí. Když to uděláte, bude hledání využívat dřívější kontext v řazení výsledků vyhledávání a také zajistit konzistentní prostředí.

Ale při volání rozhraní API služby Bing webové Search z jazyka JavaScript, funkcí integrované zabezpečení prohlížeče (CORS) může zabránit vám v přístupu k hodnoty tyto hlavičky.

K získání přístupu k hlavičky, můžete provést žádost Bing webového vyhledávání rozhraní API prostřednictvím proxy serveru CORS. Odpověď proxy serveru má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi povolených programů a jejich zpřístupní JavaScript.

Je snadné se má nainstalovat proxy CORS umožňující naše [kurz aplikace](tutorial-bing-web-search-single-page-app.md) hlavičky volitelný klientský přístup. První, pokud ještě nemáte, [instalace softwaru Node.js](https://nodejs.org/en/download/). Potom zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod rozhraní API pro Bing webové hledání v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte CORS proxy pomocí následujícího příkazu:

    cors-proxy-server

Nechte Otevřete příkazové okno při používání kurz aplikace; zavřením okna zastaví proxy serveru. V rozšíření hlavičky protokolu HTTP části níže výsledky hledání, se nyní zobrazí `X-MSEdge-ClientID` hlavičky (mimo jiné) a ověřte, zda je stejný pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědi v produkčním prostředí

CORS proxy metody uvedené v předchozí odpověď je vhodný pro vývoj, testování a získávání informací. 

V produkčním prostředí ale by měl hostitel skript na straně serveru ve stejné doméně jako webové stránky, která používá rozhraní API služby Bing webové Search. Tento skript by měl skutečně provést volání rozhraní API na vyžádání z webové stránky JavaScript a předat všechny výsledky, včetně hlavičky, zpět do klienta. Vzhledem k tomu, že tyto dva prostředky (stránka a skriptu) sdílet počátek, CORS nepřejde do play a jsou speciální hlavičky acessible jazyka JavaScript na webové stránce. 

Tento přístup také chrání klíč rozhraní API z expozice public, protože jen skript na straně serveru, musí ho. Tento skript můžete použít jiné metody a ujistěte se, že je požadavek autorizován.

## <a name="next-steps"></a>Další postup

Je váš dotaz o chybějící funkce nebo funkce? Zvažte požaduje nebo volíte se naše [webu User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Další informace najdete v tématech

 [Přetečení zásobníku: Kognitivní služby](http://stackoverflow.com/questions/tagged/bing-api)