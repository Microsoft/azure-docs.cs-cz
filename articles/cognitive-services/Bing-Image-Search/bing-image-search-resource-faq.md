---
title: Nejčastější dotazy (FAQ) – Rozhraní API pro vyhledávání obrázků bingem
titleSuffix: Azure Cognitive Services
description: Najděte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraním API pro vyhledávání obrázků Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881702"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Nejčastější dotazy (nejčastější dotazy) k rozhraní API pro vyhledávání obrázků bingu

Najděte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraním API pro vyhledávání obrázků Bingu pro služby Microsoft Cognitive Services v Azure.

## <a name="response-headers-in-javascript"></a>Záhlaví odpovědí v Jazyce JavaScript

Následující záhlaví může dojít v odpovědích z rozhraní API pro vyhledávání obrázků Bing.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Jedinečné ID, které bing přiřadil uživateli |
| `BingAPIs-Market`   |Trh, který byl použit ke splnění požadavku |
| `BingAPIs-TraceId`  |Položka protokolu na serveru rozhraní API bingu pro tento požadavek (pro podporu) |

Je obzvláště důležité zachovat ID klienta a vrátit jej s následnými požadavky. Když toto provedete, hledání bude používat minulý kontext v pořadí výsledků hledání a také poskytují konzistentní uživatelské prostředí.

Pokud však zavoláte rozhraní API pro vyhledávání obrázků Bingz JavaScriptu, mohou vám integrované funkce zabezpečení (CORS) v prohlížeči zabránit v přístupu k hodnotám těchto záhlaví.

Chcete-li získat přístup k záhlaví, můžete vytvořit požadavek rozhraní API pro vyhledávání obrázků Bingprostřed proxy CORS. Odpověď z takového proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby naše [výukové aplikace](tutorial-bing-image-search-single-page-app.md) pro přístup k volitelným záhlavím klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Poté zadejte následující příkaz na příkazovém řádku.

    npm install -g cors-proxy-server

Dále změňte koncový bod rozhraní API pro vyhledávání obrázků Bingu v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědí v produkčním prostředí

Cors proxy přístup popsaný v předchozí odpovědi je vhodný pro vývoj, testování a učení.

V produkčním prostředí byste však měli hostovat skript na straně serveru ve stejné doméně jako webová stránka, která používá rozhraní API pro vyhledávání na webu Bingu. Tento skript by měl skutečně provést volání rozhraní API na žádost z webové stránky JavaScript a předat všechny výsledky, včetně záhlaví, zpět klientovi. Vzhledem k tomu, že dva prostředky (stránka a skript) sdílejí původ, CORS nevstoupí do hry a speciální záhlaví jsou přístupná javascriptu na webové stránce.

Tento přístup také chrání klíč rozhraní API před vystavením veřejnosti, protože jej potřebuje pouze skript na straně serveru. Skript můžete použít jinou metodu (například odkazovat httper) ujistěte se, že požadavek je autorizován.

## <a name="next-steps"></a>Další kroky

Je vaše otázka týkající se chybějící funkce nebo funkce? Zvažte žádost nebo hlasování pro něj na našich [webových stránkách User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Viz také

 [Přetečení zásobníku: Kognitivní služby](https://stackoverflow.com/questions/tagged/bing-api)
