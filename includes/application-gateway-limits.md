---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334919"
---
| Prostředek | Omezení | Poznámka |
| --- | --- | --- |
| Azure Application Gateway |1 000 na jedno předplatné | |
| Konfigurace IP adres front-endu |2 |1 veřejný a 1 privátní |
| Přední porty |100<sup>1</sup> | |
| Fondy adres back-endu |100<sup>1</sup> | |
| Servery back-end na fond |1 200 | |
| Http posluchače |200<sup>1</sup> |Omezeno na 100 aktivních naslouchacích procesů, které směrují provoz. Aktivní posluchači = celkový počet posluchačů - posluchači nejsou aktivní.<br>Pokud je výchozí konfigurace uvnitř pravidla směrování nastavena na směrování provozu (například má naslouchací proces, back-endový fond a nastavení HTTP), pak se to také počítá jako naslouchací proces.|
| Pravidla vyrovnávání zatížení PROTOKOLU HTTP |100<sup>1</sup> | |
| Nastavení protokolu HTTP back-end |100<sup>1</sup> | |
| Instance na jednu bránu |V1 Skladová položka – 32<br>V2 Skladová položka - 125 | |
| Certifikáty SSL |100<sup>1</sup> |1 na http posluchače |
| Maximální velikost certifikátu SSL |V1 Skladová položka - 10 KB<br>V2 Skladová položka - 16 KB| |
| Certifikáty pro ověřování |100 | |
| Důvěryhodné kořenové certifikáty |100 | |
| Minimální časový čas požadavku |1 sekunda | |
| Maximální časový limit požadavku |24 hodin | |
| Počet webů |100<sup>1</sup> |1 na http posluchače |
| Mapy adres URL na posluchače |1 | |
| Maximální pravidla založená na cestě na mapu adres URL|100||
| Konfigurace přesměrování |100<sup>1</sup>| |
| Souběžná připojení websocketu |Střední brány 20k<br> Velké brány 50k| |
| Maximální délka adresy URL|32 KB| |
| Maximální velikost záhlaví pro HTTP/2 |4 KB| |
| Maximální velikost nahrávání souborů, standardní |2 GB | |
| Maximální velikost nahrávání souborů WAF |V1 Střední WAF brány, 100 MB<br>V1 Velké WAF brány, 500 MB<br>V2 WAF, 750 MB| |
| Limit velikosti těla WAF bez souborů|128 kB||
| Maximální vlastní pravidla WAF|100||
| Maximální vyloučení WAF|100||

<sup>1</sup> V případě skutů SKU s podporou WAF doporučujeme omezit počet prostředků na 40 pro optimální výkon.
