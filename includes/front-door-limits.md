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
ms.openlocfilehash: 37ebe2f0c5cbbaca712e69ab4484379ecf0f4830
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237319"
---
| Prostředek | Výchozí/maximální limit |
| --- | --- |
| Prostředky služby front-dveří pro Azure na předplatné | 100 |
| Hostitelé front-endu, mezi které patří vlastní domény na prostředek | 100 |
| Pravidla směrování na prostředek | 100 |
| Fondy back-endu na prostředek | 50 |
| Back-endy na fond back-endu | 100 |
| Vzor cesty, který se má shodovat s pravidlem směrování | 25 |
| Vlastní pravidla firewallu webových aplikací na jednu zásadu | 10 |
| Zásada firewallu webových aplikací na předplatné | 100 |
| Podmínky shody brány firewall webových aplikací na vlastní pravidlo | 10 |
| Rozsahy IP adres firewallu webových aplikací na shodnou podmínku | 600 |
| Hodnoty shody řetězců firewallu webových aplikací za shodné podmínky | 10 |
| Délka hodnoty shody řetězců firewallu webových aplikací | 256 |
| Délka názvu parametru POST firewallu webových aplikací | 256 |
| Délka názvu záhlaví protokolu HTTP brány firewall webových aplikací | 256 |
| Délka názvu souboru cookie pro bránu firewall webových aplikací | 256 |
| Kontrola velikosti textu požadavku protokolu HTTP v bráně firewall webových aplikací | 128 KB |
| Délka textu vlastní odpovědi firewallu webových aplikací | 2 KB |

### <a name="timeout-values"></a>Hodnoty časového limitu
#### <a name="client-to-front-door"></a>Klient do front-dveří
- Vypršel časový limit připojení TCP na přední dveře 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Přední dveře do back-endu aplikace
- Pokud je odpověď blokové odpovědi, vrátí se 200, pokud nebo dojde k přijetí prvního bloku.
- Po přeposílání požadavku HTTP na back-end zaznamená přední dveře po dobu 30 sekund prvního paketu z back-endu. Pak vrátí klientovi chybu 503.
- Po přijetí prvního paketu z back-endu budou přední dveře čekat po dobu 30 sekund v nečinném intervalu. Pak vrátí klientovi chybu 503.
- Přední dveře do back-endu TCP relace vypršela 30 minut.

### <a name="upload-and-download-data-limit"></a>Nahrávání a stahování omezení dat

|  | S kódováním přenosu v bloku dat (CTE) | Bez bloků HTTP |
| ---- | ------- | ------- |
| **Stáhnout** | Velikost stahovaných velikostí není nijak omezena. | Velikost stahovaných velikostí není nijak omezena. |
| **Nahrát** |  Neexistují žádné limity, pokud je každé nahrání CTE menší než 2 GB. | Velikost nemůže být větší než 2 GB. |

### <a name="other-limits"></a>Další omezení
- Maximální velikost adresy URL-8 192 bajtů – určuje maximální délku nezpracované adresy URL (schéma + název hostitele + port + cesta + řetězec dotazu adresy URL).
- Maximální velikost řetězce dotazu-4 096 bajtů – určuje maximální délku řetězce dotazu v bajtech.
