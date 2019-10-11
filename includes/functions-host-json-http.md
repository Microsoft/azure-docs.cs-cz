---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263324"
---
|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|routePrefix|API|Předpona trasy, která se vztahuje na všechny trasy. K odebrání výchozí předpony použijte prázdný řetězec. |
|maxOutstandingRequests|200<sup>*</sup>|Maximální počet nezpracovaných požadavků, které jsou v daném okamžiku uchovávány. Tento limit zahrnuje požadavky, které jsou ve frontě, ale nezačaly běžet, a také jakékoli probíhající provádění. Všechny příchozí žádosti přes toto omezení se odmítnou s 429 "příliš zaneprázdněnou" odezvou. Umožňuje volajícím využívat strategie opakování na základě času a také vám pomůže řídit maximální latenci žádostí. Tato možnost řídí služby Řízení front zpráv, ke kterým dochází v cestě spuštění hostitele skriptu. Další fronty, například fronta žádostí ASP.NET, budou stále platit a nebudou ovlivněny tímto nastavením. <sup>*</sup> Výchozí hodnota pro verzi 1. x je bez vazby (`-1`). Výchozí hodnota pro verzi 2. x v plánu spotřeby je 200. Výchozí hodnota pro verzi 2. x ve vyhrazeném plánu není ohraničená (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Maximální počet funkcí http, které se spustí paralelně. To vám umožňuje řídit souběžnost, což pomáhá spravovat využití prostředků. Můžete mít například funkci http, která používá velké množství systémových prostředků (paměť/procesor/sokety), což způsobuje problémy, pokud je souběžnost příliš vysoká. Nebo může být funkce, která vytváří odchozí požadavky na službu třetí strany, a tyto hovory musí být omezené na míru. V těchto případech vám může pomáhat použití omezení. <sup>*</sup> Výchozí hodnota pro verzi 1. x je bez vazby (`-1`). Výchozí hodnota pro verzi 2. x v plánu spotřeby je 100. Výchozí hodnota pro verzi 2. x ve vyhrazeném plánu není ohraničená (`-1`).|
|dynamicThrottlesEnabled|true<sup>*</sup>|Pokud je toto nastavení povolené, bude v kanálu zpracování požadavků pravidelně kontrolovat čítače výkonu systému, jako jsou připojení/vlákna/procesy/paměť/CPU/atd. Pokud některý z těchto čítačů překročí vestavěnou vysokou prahovou hodnotu (80%), požadavky budou odmítnuto s odpovědí 429 "je zaneprázdněn", dokud čítače nevrátí na normální úrovně. <sup>*</sup> Výchozí hodnota pro verzi 1. x je false. Výchozí hodnota pro verzi 2. x v plánu spotřeby je true. Výchozí hodnota pro verzi 2. x ve vyhrazeném plánu je false.|
