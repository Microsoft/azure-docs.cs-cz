---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200296"
---
| Číslo portu| V nebo ven | Rozsah portů| Povinné| Poznámky |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|V|LAN|Ano|Tento port se používá pro připojení k Data Box rozhraní REST API pro úložiště blogů přes protokol HTTP. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 443 (HTTPS)|V|LAN|Ano|Tento port se používá pro připojení k Data Box rozhraní REST API pro úložiště blogů přes protokol HTTPS. Pokud se nepřipojujete k rozhraní REST API, tento postup automaticky přesměruje do místního webového uživatelského rozhraní přes 8443. |
| TCP 8443 (HTTPS-ALT)|V|LAN|Ano|Toto je alternativní port pro HTTPS a používá se při připojování k místnímu webovému uživatelskému rozhraní pro správu zařízení. |
| TCP 445 (SMB)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port se vyžaduje jenom v případě, že se připojujete přes SMB. |
| PROTOKOL TCP 2049 (NFS)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes systém souborů NFS. |
| PROTOKOL TCP 111 (NFS)|Mimo|LAN|V některých případech<br>Zobrazit poznámky|Tento port se používá pro mapování rpcbind/port a vyžaduje se jenom v případě, že se připojujete přes systém souborů NFS.  |
