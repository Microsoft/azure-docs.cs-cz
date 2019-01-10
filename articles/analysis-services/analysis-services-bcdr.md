---
title: Vysoká dostupnost služby Azure Analysis Services | Dokumentace Microsoftu
description: Zajišťuje vysokou dostupnost služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191339"
---
# <a name="analysis-services-high-availability"></a>Vysoká dostupnost služby analýzy

Tento článek popisuje, je ujištěním, vysoká dostupnost pro servery služby Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zajišťuje vysokou dostupnost během přerušení služby

Když taková situace vzácná, datové centrum Azure může dojít k výpadku. Pokud dojde k výpadku, dojde k narušení provozu, které může trvat několik minut nebo hodin trvat. Vysoké dostupnosti se dosahuje nejčastěji s redundancí serveru. Pomocí služby Azure Analysis Services můžete dosáhnout redundance tím, že vytvoříte další, sekundární servery v jedné nebo několika oblastech. Při vytváření redundantních serverech, aby bylo zaručeno dat a metadat na těchto serverech je v synchronizaci se serverem v oblasti, který přešel do stavu offline, můžete:

* Nasazování modelů na redundantních serverech v jiných oblastech. Tato metoda vyžaduje, zpracování dat na primárním serveru i redundantních serverech v paralelní, je ujištěním, všechny servery jsou synchronizovaná.

* [Zálohování](analysis-services-backup.md) databází z primárního serveru a obnovení na redundantních serverech. Například můžete automatizovat nočních záloh do služby Azure storage a obnovit na jiné servery redundantní v jiných oblastech. 

V obou případech se primární server dojde k výpadku, musíte změnit připojovací řetězce v generování sestav klienty pro připojení k serveru v jiného místního datového centra. Tato změna by měl být poslední možnost a dochází pouze v případě výpadku katastrofickými místního datového centra. Je větší pravděpodobnost výpadku datového centra hostujícího vaše primární server by návratu do režimu online předtím, než může aktualizovat připojení ve všech klientech. 

Aby se nemusela změnit připojovací řetězce na klienti oznamující, vytvoříte server [alias](analysis-services-server-alias.md) pro primární server. Pokud primární server přestane fungovat, můžete změnit aliasu, aby odkazoval na záložní server v jiné oblasti. Můžete automatizovat alias pro název serveru kódu pro kontrolu stavu koncových bodů na primárním serveru. Pokud se nezdaří Kontrola stavu stejný koncový bod může směrovat na záložní server v jiné oblasti. 

## <a name="related-information"></a>Související informace

[Zálohování a obnovení](analysis-services-backup.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md)   
[Názvy serveru aliasů](analysis-services-server-alias.md) 

