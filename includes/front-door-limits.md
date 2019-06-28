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
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333370"
---
| Resource | Výchozí/maximální limit |
| --- | --- |
| Prostředky Azure branou služby na předplatné | 100 |
| Front-endu hostitele, včetně vlastních domén pro každý prostředek | 100 |
| Pravidla směrování pro každý prostředek | 100 |
| Fondy back-end pro každý prostředek | 50 |
| Back-EndY za back endového fondu | 100 |
| Vzorů cest, které je nutné u pravidla směrování | 25 |
| Pravidla brány firewall na vlastní webové aplikace podle zásad | 10 |
| Zásady brány firewall webových aplikací pro každý prostředek | 100 |
| Podmínky shody brány firewall webových aplikací na vlastní pravidlo | 10 |
| Web application firewall rozsahy IP adres za odpovídají podmínce | 600 |
| Hodnoty shody webové aplikace brány firewall na řetězec za podmínce shody | 10 |
| Hodnota délky řetězce shody webové aplikace brány firewall | 256 |
| Firewall webových aplikací délka názvu parametru text příspěvku | 256 |
| Firewall webových aplikací délka názvu záhlaví HTTP | 256 |
| Délka názvu webové aplikace brány firewall souboru cookie | 256 |
| Webové aplikace brány firewall protokolu HTTP požadavku velikost textu zkontroloval | 128 KB |
| Délka textu webových aplikací brány firewall vlastní odpovědi | 2 KB |

### <a name="timeout-values"></a>Hodnoty časového limitu
#### <a name="client-to-front-door"></a>Klient branou
- Přední dveře má TCP připojení časový limit nečinnosti 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Přední dveře k back endové aplikace
- Pokud je odpověď na odezvu bloku, 200 je vrácena, pokud nebo při přijetí prvního bloku.
- Po předá požadavek protokolu HTTP back-endu branou počká 30 sekund pro první paket z back-endu. Došlo k chybě 503 pak vrátí klientovi.
- Po přijetí prvního paketu z back-endu branou počká 30 sekund za časový limit nečinnosti. Došlo k chybě 503 pak vrátí klientovi.
- Přední dveře k back-end časový limit relace TCP je 30 minut.

### <a name="upload-and-download-data-limit"></a>Nahrávání a stahování dat limit

|  | S blokového kódování (CTE) přenosu | Bez dat protokolu HTTP |
| ---- | ------- | ------- |
| **Stáhnout** | Neexistuje žádné omezení na velikost ke stažení. | Neexistuje žádné omezení na velikost ke stažení. |
| **Nahrávání** |  Neexistuje žádné omezení, dokud každý nahraný CTE je menší než 2 GB. | Velikost nesmí být větší než 2 GB. |

### <a name="other-limits"></a>Další omezení
- Maximální velikost adresy URL – 8 192 bajtů - určuje maximální délku nezpracované adresy URL (schéma název hostitele a port + cesta + řetězec adresy URL dotazu) - maximální řetězec dotazu velikost - 4 096 bajtů - určuje maximální délku řetězce dotazu v bajtech.