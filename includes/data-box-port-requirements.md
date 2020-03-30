---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839743"
---
| Port č.| Dovnitř nebo ven | Rozsah portu| Požaduje se| Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| Protokol TCP 80 (HTTP)|V|LAN|Ano|Tento port se používá pro připojení k úložišti rest úložiště datové schránky přes protokol HTTP. Pokud se nepřipojujete k rozhraním REST API, automaticky se přesměruje na místní webové uživatelské rozhraní nad 8443. |
| Protokol TCP 443 (HTTPS)|V|LAN|Ano|Tento port se používá pro připojení k úložišti úložiště datových schránka úložiště REST přes HTTPS. Pokud se nepřipojujete k rozhraním REST API, automaticky se přesměruje na místní webové uživatelské rozhraní nad 8443. |
| Protokol TCP 8443 (HTTPS-Alt)|V|LAN|Ano|Toto je alternativní port pro protokol HTTPS a používá se při připojování k místnímu webovému uživatelskému rozhraní pro správu zařízení. |
| TCP 445 (SMB)|Ven/Dovnitř|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes SMB. |
| TCP 2049 (NFS)|Ven/Dovnitř|LAN|V některých případech<br>Zobrazit poznámky|Tento port je vyžadován pouze v případě, že se připojujete přes nfs. |
| TCP 111 (NFS)|Ven/Dovnitř|LAN|V některých případech<br>Zobrazit poznámky|Tento port se používá pro mapování rpcbind/port a vyžaduje se pouze v případě, že se připojujete prostřednictvím služby NFS.  |
