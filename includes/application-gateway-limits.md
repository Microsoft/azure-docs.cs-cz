---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628112"
---
| Prostředek | Výchozí omezení | Poznámka |
| --- | --- | --- |
| Application Gateway |1 000 na předplatné | |
| IP konfigurace front-endu |2 |1 veřejný a 1 privátní |
| Porty front-endu |40 | |
| Fondy adres back-endu |40 | |
| Servery back-end na fond |1200 | |
| Naslouchací procesy HTTP |40 | |
| Pravidla vyrovnávání zatížení HTTP |400 |počet naslouchacích procesů HTTP * n |
| Nastavení HTTP back-endu |40 | |
| Instance na jednu bránu |75 | |
| Certifikáty SSL |40 |1 na naslouchací proces HTTP |
| Certifikáty pro ověřování |40 | |
| Minimální časový limit požadavku |1 sekunda | |
| Maximální časový limit požadavku |24 hodin | |
| Počet webů |20 |1 na naslouchací proces HTTP |
| Map URL na naslouchací proces |1 | |
| Mapování maximální pravidel založené na cestě na adresu URL|100|
| Konfigurace přesměrování |40| |
| Připojení pomocí protokolu WebSocket souběžných |5000| |
|Maximální délka adresy URL|8000|
| Maximální velikost pro odeslání Standard |2 GB | |
| Maximální nahrávání velikost WAF |Střední WAF brány – 100 MB<br>Brány WAF velké – 500 MB| |
|Maximální velikost textu WAF (bez souborů)|128 KB|
