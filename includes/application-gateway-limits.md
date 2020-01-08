---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 0ba21fe3789fba03cd4814d19fb103c3a2559c13
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75662932"
---
| Prostředek | Výchozí/maximální limit | Poznámka |
| --- | --- | --- |
| Azure Application Gateway |1 000 na předplatné | |
| Konfigurace front-endové IP adresy |2 |1 veřejný a 1 privátní |
| Porty front-endu |100<sup>1</sup> | |
| Fondy back-endové adresy |100<sup>1</sup> | |
| Back-endové servery na fond |1,200 | |
| Naslouchací procesy HTTP |100<sup>1</sup> | |
| Pravidla vyrovnávání zatížení HTTP |100<sup>1</sup> | |
| Nastavení HTTP back-endu |100<sup>1</sup> | |
| Instance na jednu bránu |32 | |
| Certifikáty SSL |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Maximální velikost certifikátu SSL |SKU V1 – 10 KB<br>V2 SKU – 16 KB| |
| Certifikáty pro ověřování |100 | |
| Důvěryhodné kořenové certifikáty |100 | |
| Minimální časový limit požadavku |1 sekunda | |
| Maximální časový limit požadavku |24 hodin | |
| Počet webů |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Mapování adres URL na naslouchací proces |1\. místo | |
| Maximální počet pravidel založených na cestách na mapování adresy URL|100||
| Konfigurace přesměrování |100<sup>1</sup>| |
| Souběžná připojení WebSocket |Střední brány 20 tisíc<br> Velké brány 50 tis| |
| Maximální délka adresy URL|32 KB| |
| Maximální velikost hlavičky pro HTTP/2 |4KB| |
| Maximální velikost nahrávání souborů, Standard |2 GB | |
| Maximální velikost nahrávání souborů WAF |V1 střední WAF brány, 100 MB<br>Velké WAF brány V1, 500 MB<br>V2 WAF, 750 MB| |
| Omezení velikosti těla WAF, bez souborů|128 kB||
| Maximální počet vlastních pravidel WAF|100||
| Maximální počet vyloučení WAF|100||

<sup>1</sup> v případě SKU s POVOLENým WAF doporučujeme omezit počet prostředků na 40 pro zajištění optimálního výkonu.
