---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440067"
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
| Počet webů |40 |1 na naslouchací proces HTTP |
| Map URL na naslouchací proces |1 | |
| Mapování maximální pravidel založené na cestě na adresu URL|100|
| Konfigurace přesměrování |40| |
| Připojení pomocí protokolu WebSocket souběžných |5000| |
|Maximální délka adresy URL|8000|
| Maximální velikost pro odeslání Standard |2 GB | |
| Maximální nahrávání velikost WAF |Střední WAF brány – 100 MB<br>Brány WAF velké – 500 MB| |
|Maximální velikost textu WAF (bez souborů)|128 KB|
