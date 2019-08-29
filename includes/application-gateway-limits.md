---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: cd0ec07ebfffc839fedcdc76c9aff8e86556a7de
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130074"
---
| Resource | Výchozí/maximální limit | Poznámka |
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
| Mapování adres URL na naslouchací proces |1 | |
| Maximální počet pravidel založených na cestách na mapování adresy URL|100||
| Konfigurace přesměrování |100<sup>1</sup>| |
| Souběžná připojení WebSocket |Střední brány 20 tisíc<br> Velké brány 50 tis| |
| Maximální délka adresy URL|8 000||
| Maximální velikost nahrávání souborů, Standard |2 GB | |
| Maximální velikost nahrávání souborů WAF |Střední brány WAF, 100 MB<br>Velké brány WAF, 500 MB| |
| Omezení velikosti těla WAF, bez souborů|128 KB||
| Maximální počet vlastních pravidel WAF|100||
| Maximální počet vyloučení WAF|100||

<sup>1</sup> v případě SKU s POVOLENým WAF doporučujeme omezit počet prostředků na 40 pro zajištění optimálního výkonu.
