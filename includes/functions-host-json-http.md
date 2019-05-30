---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 35b087d13099b975a1c9c6d2dbd449935f5f0d1d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248971"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|routePrefix|rozhraní api|Předpona trasy, které platí pro všechny trasy. Chcete-li odebrat výchozí předponu použijte prázdný řetězec. |
|maxOutstandingRequests|200<sup>*</sup>|Maximální počet nevyřízených požadavků, které jsou uloženy v daném okamžiku. Toto omezení zahrnuje požadavků, které jsou zařazeny do fronty, ale nebyla spuštěna, a také v průběhu provádění. Všechny příchozí žádosti o nad tento limit se odmítne kvůli odpověď 429 "Příliš zaneprázdněn a nemůže". Který umožňuje volajícím využívat strategie opakování podle času a také vám umožňuje řídit maximální požadavek latenci. Tím je řízeno jen služby Řízení front, který se nachází v rámci cesty spuštění skriptu hostitele. Další front, jako jsou fronty požadavků ASP.NET zůstanou v platnosti a toto nastavení nemá vliv. <sup>*</sup>Výchozí hodnota pro verzi 1.x je bez vazby (`-1`). Výchozí hodnota pro verzi 2.x v plánu consumption je 200. Výchozí hodnota pro verzi 2.x v vyhrazený plán je bez vazby (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Maximální počet funkce protokolu http, které budou spuštěny paralelně. Umožňuje řízení souběžnosti, který může pomoct spravovat využití prostředků. Například můžete mít funkce protokolem http, který používá velké množství systémových prostředků (procesoru/paměti/sockets) tak, že je způsobuje problémy, když je souběžnost příliš vysoká. Nebo můžete mít funkci, která umožňuje odchozí požadavky na služby třetí strany a tato volání musí být rychlost je omezená. V těchto případech může pomoci použití omezení tady. <sup>*</sup>Výchozí hodnota pro verzi 1.x je bez vazby (`-1`). Výchozí hodnota pro verzi 2.x v plánu consumption je 100. Výchozí hodnota pro verzi 2.x v vyhrazený plán je bez vazby (`-1`).|
|dynamicThrottlesEnabled|Hodnota TRUE<sup>*</sup>|V případě, že povoleno, toto nastavení příčiny v kanálu zpracování požadavků na výkon systému pravidelně kontrolovat čítače, jako je připojení, vlákna a procesy/paměti/procesoru/další a pokud některý z těchto čítačů jsou nad integrovanou vysokou prahovou hodnotou (80 %), si vyžádá bude odmítne kvůli odpověď 429 "Příliš zaneprázdněn a nemůže", dokud counter(s) vrátit na běžnou úroveň. <sup>*</sup>Výchozí hodnota pro verzi 1.x má hodnotu false. Výchozí verze 2.x v plánu consumption má hodnotu true. Výchozí hodnota pro verzi 2.x v vyhrazený plán má hodnotu false.|
