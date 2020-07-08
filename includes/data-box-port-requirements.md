---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67839743"
---
| Číslo portu| V nebo ven | Rozsah portů| Vyžadováno| Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|V|LAN|Yes|Tento port se používá pro připojení k Data Box rozhraní REST API pro úložiště blogů přes protokol HTTP. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 443 (HTTPS)|V|LAN|Yes|Tento port se používá pro připojení k Data Box rozhraní REST API pro úložiště blogů přes protokol HTTPS. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 8443 (HTTPS-ALT)|V|LAN|Yes|Toto je alternativní port pro HTTPS a používá se při připojování k místnímu webovému uživatelskému rozhraní pro správu zařízení. |
| TCP 445 (SMB)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port se vyžaduje jenom v případě, že se připojujete přes SMB. |
| PROTOKOL TCP 2049 (NFS)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes systém souborů NFS. |
| PROTOKOL TCP 111 (NFS)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port se používá pro mapování rpcbind/port a vyžaduje se jenom v případě, že se připojujete přes systém souborů NFS.  |
