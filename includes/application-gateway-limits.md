---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211822"
---
| Prostředek | Výchozí omezení | Poznámka |
| --- | --- | --- |
| Application Gateway |1 000 na předplatné | |
| Konfigurace front-endové IP adresy |2 |1 veřejný a 1 privátní |
| Porty front-endu |100<sup>1</sup> | |
| Fondy adres back endu |100<sup>1</sup> | |
| Servery back-end na fond |1200 | |
| Naslouchací procesy HTTP |100<sup>1</sup> | |
| Pravidla vyrovnávání zatížení HTTP |100<sup>1</sup> | |
| Nastavení HTTP back-end |100<sup>1</sup> | |
| Instance na jednu bránu |32 | |
| Certifikáty SSL |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Certifikáty pro ověřování |100 | |
| Důvěryhodných kořenových certifikátů |100 | |
| Minimální časový limit požadavku |1 sekunda | |
| Maximální časový limit požadavku |24 hodin | |
| Počet webů |100<sup>1</sup> |1 na naslouchací proces HTTP |
| Map URL na naslouchací proces |1 | |
| Mapování maximální pravidel založené na cestě na adresu URL|100||
| Konfigurace přesměrování |100<sup>1</sup>| |
| Souběžná připojení pomocí protokolu WebSocket |5000| |
| Maximální délka adresy URL|8000||
| Maximální velikost pro odeslání Standard |2 GB | |
| Maximální nahrávání velikost WAF |Střední WAF brány – 100 MB<br>Brány WAF velké – 500 MB| |
| Maximální velikost textu WAF (bez souborů)|128 KB||

<sup>1</sup> v případě povoleným WAF SKU se doporučuje omezit počet prostředků na 40 pro zajištění optimálního výkonu.
