---
title: zahrnout soubor
description: zahrnout soubor
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335091"
---
| Prostředek | Omezení |
| --- | --- |
| Prostředky Azure Front Door na jedno předplatné | 100 |
| Front-endoví hostitelé, která zahrnuje vlastní domény na prostředek | 500 |
| Pravidla směrování podle prostředku | 500 |
| Fondy back-endu na prostředek | 50 |
| Zadní konce na back-end ový fond | 100 |
| Vzorky cesty odpovídající pravidlu směrování | 25 |
| Adresy URL v jednom volání vymazání mezipaměti | 100 |
| Vlastní pravidla brány firewall webových aplikací podle zásad | 100 |
| Zásady brány firewall webové aplikace na jedno předplatné | 100 |
| Podmínky pro bránu firewall webové aplikace odpovídají vlastnímu pravidlu | 10 |
| Rozsahy IP adres brány firewall webové aplikace podle podmínky shody | 600 |
| Řetězec řetězce brány firewall webové aplikace odpovídá hodnotám podle podmínky shody | 10 |
| Délka hodnoty řetězce brány firewall webové aplikace | 256 |
| Délka názvu parametru objektu post brány firewall webové aplikace | 256 |
| Délka názvu hlavičky HTTP brány firewall webové aplikace | 256 |
| Délka názvu souboru cookie brány firewall webové aplikace | 256 |
| Kontrola velikosti těla požadavku HTTP brány firewall webové aplikace | 128 kB |
| Délka těla vlastní odezvy brány firewall webové aplikace | 2 kB |

### <a name="timeout-values"></a>Hodnoty časového opojení
#### <a name="client-to-front-door"></a>Klient k předním dveřím
* Front Door má nečinné vypršení připojení TCP 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Přední dveře k aplikaci back-end
* Pokud odpověď je bloková odpověď, 200 je vrácena, pokud nebo při přijetí prvního bloku.
* Po předání požadavku HTTP do back-endu front door čeká 30 sekund na první paket ze zadní části. Potom vrátí chybu 503 klientovi. Tato hodnota je konfigurovatelná prostřednictvím pole sendRecvTimeoutSeconds v rozhraní API.
    * Pro scénáře ukládání do mezipaměti tento časový rozsah není konfigurovatelný, a proto pokud je požadavek uložen do mezipaměti a trvá více než 30 sekund pro první paket z front door nebo z back-endu, je klientovi vrácena chyba 504. 
* Po přijetí prvního paketu ze zadníčásti front door čeká 30 sekund v vypršení časového limitu nečinnosti. Potom vrátí chybu 503 klientovi. Tato hodnota časového opovce není konfigurovatelná.
* Časový rozsah relace Front Door to the back-end TCP je 90 sekund.

### <a name="upload-and-download-data-limit"></a>Limit nahrávání a stahování dat

|  | S blokovým kódováním přenosu (CTE) | Bez bloků HTTP |
| ---- | ------- | ------- |
| **Stáhnout** | Velikost stahování není nijak omezena. | Velikost stahování není nijak omezena. |
| **Nahrát** |    Neexistuje žádný limit, pokud je každé nahrání CTE menší než 2 GB. | Velikost nesmí být větší než 2 GB. |

### <a name="other-limits"></a>Jiné limity
* Maximální velikost adresy URL - 8 192 bajtů - Určuje maximální délku nezpracované adresy URL (schéma + název_hostitele + port + cesta + řetězec dotazu adresy URL)
* Maximální velikost řetězce dotazu - 4 096 bajtů - Určuje maximální délku řetězce dotazu v bajtech.
* Maximální velikost hlavičky odpovědi HTTP z adresy URL sondy stavu - 4 096 bajtů - Zadaná maximální délka všech hlaviček odpovědí sond se stavem. 
