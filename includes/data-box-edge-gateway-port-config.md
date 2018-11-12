---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263600"
---
| Port č.| Snížení nebo navýšení kapacity | Rozsah portů| Požaduje se|   Poznámky |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Výstup|WAN |Ne|Odchozí port se používá pro přístup k Internetu pro načtení aktualizací. <br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| TCP 443 (HTTPS)|Výstup|WAN|Ano|Odchozí port se používá pro přístup k datům v cloudu.<br>Odchozí webový proxy server je konfigurovatelná uživatelem.|   
| UDP 53 (DNS)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte DNS server s procesorem na Internetu.<br>Doporučujeme používat místní server DNS. |
| UDP 123 (NTP)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je požadován, pouze v případě, že používáte NTP server s procesorem na Internetu.  |
| UDP 67 (DHCP)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte DHCP server.  |
| TCP 80 (HTTP)|V|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. <br>Přístup k místním uživatelského rozhraní pomocí protokolu HTTP bude automaticky přesměrovat na protokol HTTPS.  |
| TCP 443 (HTTPS)|V|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. |