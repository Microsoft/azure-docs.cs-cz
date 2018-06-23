---
title: Nejčastější dotazy (FAQ) týkající se Azure pro automatické návrhy v rozhraní API | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se Azure rozhraní API pro automatické návrhy kognitivní služby v Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343395"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Časté otázky (FAQ) o pro automatické návrhy v rozhraní API (kognitivní služby)
 
 Odpovědi na nejčastější dotazy o konceptech, kód a scénáře související s rozhraní API pro automatické návrhy pro kognitivní služby Azure.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Jak získám volitelný klientský hlavičky, při volání rozhraní API pro automatické návrhy v Bingu z jazyka JavaScript?

Následující hlavičky jsou volitelné, ale doporučujeme považovat je podle potřeby. Tyto hlavičky pomoci API pro automatické návrhy v Bingu přesnější výsledky.

- Umístění X vyhledávání
- X-MSEdge-ClientID
- X-MSEdge když

Ale při volání rozhraní API pro automatické návrhy v Bingu z jazyka JavaScript, funkcí integrované zabezpečení prohlížeče může zabránit vám v přístupu k hodnoty tyto hlavičky.

To vyřešit, můžete provést žádost o rozhraní API pro automatické návrhy v Bingu prostřednictvím proxy serveru CORS. Odpověď proxy serveru má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi povolených programů a jejich zpřístupní JavaScript.

Je snadné se má nainstalovat proxy CORS umožňující naše [kurz aplikace](tutorials/autosuggest.md) hlavičky volitelný klientský přístup. První, pokud ještě nemáte, [instalace softwaru Node.js](https://nodejs.org/en/download/). Potom zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod rozhraní API pro automatické návrhy v Bingu v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Nakonec spusťte CORS proxy pomocí následujícího příkazu:

    cors-proxy-server

Nechte Otevřete příkazové okno při používání kurz aplikace; zavřením okna zastaví proxy serveru. V rozšíření hlavičky protokolu HTTP části níže výsledky hledání, se nyní zobrazí `X-MSEdge-ClientID` hlavičky (mimo jiné) a ověřte, zda je stejný pro každý požadavek.

## <a name="next-steps"></a>Další postup

Je váš dotaz o chybějící funkce nebo funkce? Zvažte požaduje nebo volíte se naše [webu User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Další informace najdete v tématech

- [Přetečení zásobníku: Kognitivní služby](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
