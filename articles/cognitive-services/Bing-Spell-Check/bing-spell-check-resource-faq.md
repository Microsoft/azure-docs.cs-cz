---
title: Nejčastější dotazy týkající se pravopisu Bingu najdete rozhraní API
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na běžné dotazy týkající se rozhraní API Bingu pro kontrolu pravopisu zkontrolujte v Azure.
services: cognitive-services
author: HeidiSteen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 552e6fb3375c04e2f157842b13f72151e122f17b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866334"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Nejčastější dotazy týkající se pravopisu Bingu najdete rozhraní API

 Najděte odpovědi na nejčastější dotazy o konceptech, kód a scénáře týkající se rozhraní API Bingu pro kontrolu pravopisu zkontrolujte služeb Microsoft Cognitive Services v Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Jak se při volání rozhraní API Bingu pro kontrolu pravopisu zkontrolujte z jazyka JavaScript získat záhlaví volitelný klientský?

Následující hlavičky jsou volitelné, ale doporučujeme vám, že jste s nimi zacházet podle potřeby. Tyto hlavičky pomáhají Bingu pravopisu rozhraní API pro kontrolu návratové přesnější výsledky.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Ale při volání rozhraní API Bingu pro kontrolu pravopisu zkontrolujte z jazyka JavaScript v prohlížeči integrovaných funkcí zabezpečení by mohly bránit můžete přístup k hodnoty z těchto záhlaví.

Chcete-li vyřešit tento problém, můžete provést požadavek na API kontrola pravopisu Bingu prostřednictvím proxy serveru CORS. Odpověď od takový proxy server má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi seznamů povolených a zpřístupňuje je pro jazyk JavaScript.

Je snadné k instalaci proxy CORS a povolit [ukázková aplikace](tutorials/spellcheck.md) záhlaví volitelný klientský přístup. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Potom zadejte následující příkaz z příkazového řádku.

    npm install -g cors-proxy-server

V dalším kroku změňte API kontrola pravopisu Bingu koncového bodu v souboru HTML:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozšíření hlavičky protokolu HTTP níže v části výsledků hledání, nyní je vidět `X-MSEdge-ClientID` záhlaví (mimo jiné) a ověřte, že je stejný pro každý požadavek.

## <a name="next-steps"></a>Další postup

Je vaše otázky týkající se chybějící funkce nebo funkce? Vezměte v úvahu požádat nebo Hlasujte pro ni [webovou stránku UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Další informace najdete v tématech

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
