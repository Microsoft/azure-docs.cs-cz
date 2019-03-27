---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494652"
---
| Prostředek | Výchozí omezení | Poznámka |
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

<sup>1</sup> v případě povoleným WAF SKU, doporučujeme vám, že omezíte počet prostředků na 40 pro zajištění optimálního výkonu.
