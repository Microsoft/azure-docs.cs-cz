---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839743"
---
| Port č.| Snížení nebo navýšení kapacity | Rozsah portů| Požadováno| Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|V|LAN|Ano|Tento port se používá pro připojení k úložišti Blog služby Data Box rozhraní REST API přes protokol HTTP. Pokud není připojení k rozhraní REST API, to automaticky přesměruje na místního webového uživatelského rozhraní nad 8443. |
| TCP 443 (HTTPS)|V|LAN|Ano|Tento port se používá pro připojení k úložišti Blog služby Data Box rozhraní REST API přes protokol HTTPS. Pokud není připojení k rozhraní REST API, to automaticky přesměruje na místního webového uživatelského rozhraní nad 8443. |
| TCP 8443 (HTTPS-Alt)|V|LAN|Ano|Toto je alternativní port pro protokol HTTPS a používá se při připojování k místního webového uživatelského rozhraní pro správu zařízení. |
| TCP 445 (SMB)|Out nebo přihlášení.|LAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že se připojujete přes SMB. |
| TCP 2049 (NFS)|Out nebo přihlášení.|LAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že se připojujete pomocí systému souborů NFS. |
| TCP 111 (NFS)|Out nebo přihlášení.|LAN|V některých případech<br>Viz poznámky|Tento port se používá k mapování rpcbind/port a vyžaduje se, jenom Pokud se připojujete pomocí systému souborů NFS.  |
