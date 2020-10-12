---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505909"
---
| Číslo portu| V nebo ven | Rozsah portů| Vyžadováno| Poznámky |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|V|Síť LAN|Yes|Tento port se používá pro připojení k Data Box rozhraní REST API služby Blob Storage přes protokol HTTP. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 443 (HTTPS)|V|Síť LAN|Yes|Tento port se používá pro připojení k Data Box rozhraní REST API služby Blob Storage přes protokol HTTPS. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 8443 (HTTPS-ALT)|V|Síť LAN|Yes|Toto je alternativní port pro HTTPS a používá se při připojování k místnímu webovému uživatelskému rozhraní pro správu zařízení. |
| TCP 445 (SMB)|Mimo|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port se vyžaduje jenom v případě, že se připojujete přes SMB. |
| PROTOKOL TCP 2049 (NFS)|Mimo|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes systém souborů NFS. |
| PROTOKOL TCP 111 (NFS)|Mimo|Síť LAN|V některých případech<br>Zobrazit poznámky|Tento port se používá pro mapování rpcbind/port a vyžaduje se jenom v případě, že se připojujete přes systém souborů NFS.  |
