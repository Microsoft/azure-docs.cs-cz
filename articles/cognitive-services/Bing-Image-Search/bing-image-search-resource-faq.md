---
title: Nejčastější dotazy – rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraní API Bingu pro vyhledávání obrázků.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342188"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Nejčastější dotazy týkající se rozhraní API Bingu pro vyhledávání obrázků

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Získejte odpovědi na nejčastější dotazy týkající se konceptů, kódu a scénářů souvisejících s rozhraní API Bingu pro vyhledávání obrázků pro Microsoft Cognitive Services v Azure.

## <a name="response-headers-in-javascript"></a>Hlavičky odpovědi v JavaScriptu

V odpovědích z rozhraní API Bingu pro vyhledávání obrázků mohou nastat následující hlavičky.

| Atribut           | Popis   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Jedinečné ID, které Bingu přiřadilo uživateli |
| `BingAPIs-Market`   |Trh, který se použil ke splnění žádosti |
| `BingAPIs-TraceId`  |Položka protokolu serveru rozhraní API Bingu pro tuto žádost (pro podporu) |

Je obzvláště důležité zachovat ID klienta a vrátit ho s následnými požadavky. Když to uděláte, bude hledání používat minulý kontext v rámci řazení výsledků hledání a zároveň poskytuje konzistentní uživatelské prostředí.

Když však zavoláte rozhraní API Bingu pro vyhledávání obrázků z JavaScriptu, můžou vám integrované funkce zabezpečení (CORS) v prohlížeči bránit v přístupu k hodnotám těchto hlaviček.

Pokud chcete získat přístup k hlavičkám, můžete žádost o rozhraní API Bingu pro vyhledávání obrázků vytvořit prostřednictvím serveru proxy CORS. Odpověď z takového serveru proxy má `Access-Control-Expose-Headers` záhlaví, které filtruje hlavičky odpovědí a zpřístupňuje je pro JavaScript.

Je snadné nainstalovat proxy CORS, aby mohla naše [aplikace](tutorial-bing-image-search-single-page-app.md) získat přístup k volitelným hlavičkám klienta. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak na příkazovém řádku zadejte následující příkaz.

```console
npm install -g cors-proxy-server
```

Dále změňte koncový bod rozhraní API Bingu pro vyhledávání obrázků v souboru HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="response-headers-in-production"></a>Hlavičky odpovědí v produkčním prostředí

Přístup k proxy CORS, který je popsaný v předchozí odpovědi, je vhodný pro vývoj, testování a učení.

V produkčním prostředí byste ale měli hostovat skript na straně serveru ve stejné doméně jako webová stránka, která používá rozhraní API Bingu pro vyhledávání na webu. Tento skript by měl ve skutečnosti dělat volání rozhraní API na vyžádání z webové stránky JavaScript a předat všem výsledkům, včetně hlaviček, zpátky klientovi. Vzhledem k tomu, že dva prostředky (stránka a skript) sdílí počátek, CORS nepřichází do hry a speciální hlavičky jsou přístupné pro JavaScript na webové stránce.

Tento přístup také chrání klíč rozhraní API před expozicí veřejnosti, protože ho potřebuje jenom skript na straně serveru. Skript může použít jinou metodu (například odkazující na HTTP), aby se zajistilo, že bude požadavek autorizován.

## <a name="next-steps"></a>Další kroky

Máte dotaz ohledně chybějící funkce nebo funkce? Zvažte vyžádání nebo hlasování na webu našeho [uživatelského hlasu](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Viz také

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)