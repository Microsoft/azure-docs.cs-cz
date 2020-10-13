---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 92ec7c0a1469c9f02855cd6191faa8514e54c8f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829093"
---
| Prostředek | Omezení | Poznámka |
| --- | --- | --- |
| Azure Application Gateway |1 000 na předplatné | |
| Konfigurace front-endové IP adresy |2 |1 veřejný a 1 privátní |
| Porty front-endu |100<sup>1</sup> | |
| Fondy back-endové adresy |100<sup>1</sup> | |
| Back-endové servery na fond |1 200 | |
| Naslouchací procesy HTTP |200<sup>1</sup> |Omezeno na 100 aktivních naslouchací procesy, které směrují provoz. Aktivní naslouchací procesy = celkový počet naslouchacího procesu – naslouchací procesy nejsou aktivní.<br>Pokud je výchozí konfigurace v pravidle směrování nastavená na směrování provozu (například má naslouchací proces, fond back-end a nastavení HTTP), pak se také počítá jako naslouchací proces.|
| Pravidla vyrovnávání zatížení HTTP |100<sup>1</sup> | |
| Nastavení HTTP back-endu |100<sup>1</sup> | |
| Instance na jednu bránu |V1 SKU-32<br>V2 SKU – 125 | |
| Certifikáty SSL |100<sup>1</sup> |1 za naslouchací proces HTTP |
| Maximální velikost certifikátu SSL |SKU V1 – 10 KB<br>V2 SKU – 16 KB| |
| Certifikáty pro ověřování |100 | |
| Důvěryhodné kořenové certifikáty |100 | |
| Minimální časový limit požadavku |1 sekunda | |
| Maximální časový limit požadavku |24 hodin | |
| Počet webů |100<sup>1</sup> |1 za naslouchací proces HTTP |
| Mapování adres URL na naslouchací proces |1 | |
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

<sup>1</sup> v případě SKU s POVOLENým WAF je nutné omezit počet prostředků na 40.
