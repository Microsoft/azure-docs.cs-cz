---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711272"
---
| Název dimenze | Description |
| ------------------- | ----------------- |
| **Typ** | Transakce z primárního nebo sekundárního clusteru. Dostupné hodnoty zahrnují **Primary** a **Secondary**. Vztahuje se na geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) při čtení objektů ze sekundárního tenanta. |
| **ResponseType** | Typ odpovědi transakce. Dostupné hodnoty zahrnují: <br/><br/> <li>**ServerOtherError**: Všechny ostatní chyby na straně serveru kromě zde popsaných </li> <li>**ServerBusyError**: Ověřená žádost, která vrátila stavový kód HTTP 503. </li> <li>**ServerTimeoutError**: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Časový limit vypršel kvůli chybě serveru. </li> <li>**AuthorizationError**: Ověřená žádost, která selhala kvůli neoprávněnému přístupu k datům nebo chybě autorizace. </li> <li>**NetworkError**: Ověřená žádost, která selhala kvůli chybě sítě. K tomu nejčastěji dochází, když klient předčasně ukončí spojení před vypršením časového limitu. </li><li>**ClientAccountBandwidthThrottlingError**: požadavek se omezuje na šířku pásma pro překročení [limitů škálovatelnosti účtu úložiště](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: požadavek se omezuje na míru požadavků pro překročení [limitů škálovatelnosti účtu úložiště](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: Další chyba omezování na straně klienta. ClientAccountBandwidthThrottlingError a ClientAccountRequestThrottlingError jsou vyloučené.</li> <li>**ClientTimeoutError**: Ověřená žádost s vypršeným časovým limitem, který vrátil stavový kód HTTP 500. Pokud je časový limit sítě klienta nebo časový limit žádosti nastavený na hodnotu nižší, než služba úložiště očekávala, jde o očekávané vypršení časového limitu. V opačném případě bude ohlášeno jako ServerTimeoutError. </li> <li>**ClientOtherError**: Všechny ostatní chyby na straně klienta kromě zde popsaných. </li> <li>**Success**: Úspěšná žádost</li> <li> **SuccessWithThrottling**: požadavek byl úspěšný, když se klient SMB omezí při prvním pokusu, ale po opakování dojde k úspěšnému dokončení.</li> |
| **ApiName** | Název operace. 
| **Authentication** | Typ ověřování používaný v transakcích. Dostupné hodnoty zahrnují: <br/> <li>**AccountKey**: transakce se ověřuje pomocí klíče účtu úložiště.</li> <li>**SAS**: transakce je ověřena pomocí sdílených přístupových podpisů.</li> <li>**OAuth**: transakce je ověřená pomocí přístupových tokenů OAuth.</li> <li>**Anonymní**: transakce se požaduje anonymně. Neobsahuje požadavky na kontrolu před výstupem.</li> <li>**AnonymousPreflight**: transakce je požadavkem na kontrolu před výstupem.</li> |