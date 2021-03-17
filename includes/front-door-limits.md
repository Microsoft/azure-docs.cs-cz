---
title: zahrnout soubor
description: zahrnout soubor
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 02/18/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 53d837883daefddd5fa3f0f543eae1d116a5e86a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102933"
---
| Prostředek | Omezení |
| --- | --- |
| Prostředky front-dveří Azure na předplatné | 100 |
| Hostitelé front-endu, mezi které patří vlastní domény na prostředek | 500 |
| Pravidla směrování na prostředek | 500 |
| Fondy back-endu na prostředek | 50 |
| Back-endy na fond back-endu | 100 |
| Vzor cesty, který se má shodovat s pravidlem směrování | 25 |
| Adresy URL v jednom volání vyprázdnění mezipaměti | 100 |
| Vlastní pravidla firewallu webových aplikací na jednu zásadu | 100 |
| Zásada firewallu webových aplikací na předplatné | 100 |
| Podmínky shody brány firewall webových aplikací na vlastní pravidlo | 10 |
| Rozsahy IP adres firewallu webových aplikací na shodnou podmínku | 600 |
| Hodnoty shody řetězců firewallu webových aplikací za shodné podmínky | 10 |
| Délka hodnoty shody řetězců firewallu webových aplikací | 256 |
| Délka názvu parametru POST firewallu webových aplikací | 256 |
| Délka názvu záhlaví protokolu HTTP brány firewall webových aplikací | 256 |
| Délka názvu souboru cookie pro bránu firewall webových aplikací | 256 |
| Kontrola velikosti textu požadavku protokolu HTTP v bráně firewall webových aplikací | 128 kB |
| Délka textu vlastní odpovědi firewallu webových aplikací | 2 kB |

### <a name="azure-front-door-standardpremium-preview-service-limits"></a>Omezení služby Azure front-Standard/Premium (Preview)

*** *Maximální počet **500** profilů Standard a Premium na předplatné*

| Prostředek | Limit standardních SKU | Limit SKU úrovně Premium |
| --- | --- | --- |
| Maximální počet koncových bodů na profil  | 10 | 25 |
| Maximální vlastní doména na profil | 100 | 200 |
| Maximální skupina původních souborů na profil | 100 | 200 |
| Maximální počet tajných klíčů na profil | 100 | 200 |
| Maximální zásada zabezpečení na profil | 100 | 200 |
| Maximální nastavená sada pravidel na profil | 100 | 200 |
| Maximální počet pravidel na sadu pravidel | 100 | 100 |
| Maximální původ na skupinu původních zdrojů | 50 | 50 |
| Maximální počet tras na koncový bod | 100 | 200 |
| Podmínky shody brány firewall webových aplikací na vlastní pravidlo | 10 | 10 |
| Rozsahy IP adres firewallu webových aplikací na shodnou podmínku | 600 | 600 |
| Hodnoty shody řetězců firewallu webových aplikací za shodné podmínky | 10 | 10 |
| Délka hodnoty shody řetězců firewallu webových aplikací | 256 | 256 |
| Délka názvu parametru POST firewallu webových aplikací | 256 | 256 |
| Délka názvu záhlaví protokolu HTTP brány firewall webových aplikací | 256 | 256 |
| Délka názvu souboru cookie pro bránu firewall webových aplikací | 256 | 256|
| Kontrola velikosti textu požadavku protokolu HTTP v bráně firewall webových aplikací | 128 kB | 128 kB |
| Délka textu vlastní odpovědi firewallu webových aplikací | 2 kB | 2 kB |

### <a name="timeout-values"></a>Hodnoty časového limitu
#### <a name="client-to-front-door"></a>Klient do front-dveří
* Vypršel časový limit připojení TCP na přední dveře 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Přední dveře do back-endu aplikace
* Pokud je odpověď blokové odpovědi, vrátí se 200, pokud nebo dojde k přijetí prvního bloku.
* Po přeposílání požadavku HTTP na back-end zaznamená přední dveře po dobu 30 sekund prvního paketu z back-endu. Pak vrátí klientovi chybu 503. Tato hodnota se dá nakonfigurovat přes pole sendRecvTimeoutSeconds v rozhraní API.
    * U scénářů pro ukládání do mezipaměti není možné tento časový limit konfigurovat, takže pokud je požadavek uložen do mezipaměti a trvá více než 30 sekund pro první paket z front-endu nebo z back-endu, vrátí se klientovi chyba 504. 
* Po přijetí prvního paketu z back-endu budou přední dveře čekat po dobu 30 sekund v nečinném intervalu. Pak vrátí klientovi chybu 503. Tuto hodnotu časového limitu není možné konfigurovat.
* Časová dvířka pro back-end relaci TCP je 90 sekund.

### <a name="upload-and-download-data-limit"></a>Nahrávání a stahování omezení dat

|  | S kódováním přenosu v bloku dat (CTE) | Bez bloků HTTP |
| ---- | ------- | ------- |
| **Stáhnout** | Velikost stahovaných velikostí není nijak omezena. | Velikost stahovaných velikostí není nijak omezena. |
| **Nahrát** |    Neexistují žádné limity, pokud je každé nahrání CTE menší než 2 GB. | Velikost nemůže být větší než 2 GB. |

### <a name="other-limits"></a>Další omezení
* Maximální velikost adresy URL-8 192 bajtů – určuje maximální délku nezpracované adresy URL (schéma + název hostitele + port + cesta + řetězec dotazu adresy URL).
* Maximální velikost řetězce dotazu-4 096 bajtů – určuje maximální délku řetězce dotazu v bajtech.
* Maximální velikost hlavičky odpovědi HTTP z adresy URL sondy stavu-4 096 bajtů – zadali jste maximální délku všech hlaviček odezvy sond stavu. 
