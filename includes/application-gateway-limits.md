---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689098"
---
| Resource | Výchozí/maximální limit | Poznámka |
| --- | --- | --- |
| Azure Application Gateway |1 000 na předplatné | |
| Front-endové konfigurace protokolu IP |2 |1 veřejný a 1 privátní |
| Porty front-endu |100<sup>1</sup> | |
| Fondy adres back endu |100<sup>1</sup> | |
| Servery back-end na fond |1,200 | |
| Naslouchací procesy HTTP |100<sup>1</sup> | |
| Pravidla Vyrovnávání zatížení HTTP |100<sup>1</sup> | |
| Nastavení HTTP back-end |100<sup>1</sup> | |
| Instance na jednu bránu |32 | |
| Certifikáty SSL |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Maximální velikost certifikátu SSL |V1 SKU - 10 KB<br>V2 SKU – 25 KB| |
| Certifikáty pro ověřování |100 | |
| Důvěryhodných kořenových certifikátů |100 | |
| Minimální časový limit žádosti |1 sekunda | |
| Maximální časový limit žádosti |24 hodin | |
| Počet webů |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Map URL na naslouchací proces |1 | |
| Mapování maximální pravidel založené na cestě na adresu URL|100||
| Konfigurace přesměrování |100<sup>1</sup>| |
| Souběžná připojení pomocí protokolu WebSocket |Střední brány 20 tis.<br> Velké brány 50 tis.| |
| Maximální délka adresy URL|8 000||
| Maximální velikost nahrání, Standard |2 GB | |
| Maximální nahrávání velikost WAF |Střední brány WAF, 100 MB<br>Velké brány WAF, 500 MB| |
| Omezení velikosti textu WAF, bez souborů|128 KB||
|Maximální vlastní pravidla firewallu webových aplikací|100||

<sup>1</sup> v případě povoleným WAF SKU, doporučujeme vám, že omezíte počet prostředků na 40 pro zajištění optimálního výkonu.
