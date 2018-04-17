---
title: Azure vysokou dostupnost služby Analysis Services | Microsoft Docs
description: Zajišťuje vysokou dostupnost služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f8252e47224b96d43551ad389148a1ce4349c308
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="analysis-services-high-availability"></a>Vysoká dostupnost Analysis Services
Tento článek popisuje, zajištění vysoké dostupnosti pro servery Azure Analysis Services. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Zajištění vysoké dostupnosti během přerušení služby
Když je taková situace vzácná, může mít datové centrum Azure k výpadku. Pokud dojde k výpadku, budou přerušení obchodní, který může trvat několik minut nebo může trvat hodiny. Vysoká dostupnost nejčastěji dosáhnout s nadbytečností serveru. S Azure Analysis Services můžete dosáhnout vytvořením další, sekundární servery v oblastech jeden nebo více redundance. Při vytváření redundantních serverů, aby zajistil, data a metadata na těchto serverech je v synchronizaci se serverem v oblasti, který přešel do stavu offline, můžete:

* Modely nasazení na redundantní servery v jiných oblastech. Tato metoda vyžaduje zpracování dat na primární server i redundantní servery v paralelní, zajištění všechny servery jsou v synchronizaci.

* [Zálohování](analysis-services-backup.md) databáze z primárního serveru a obnovení na redundantní serverech. Například můžete automatizovat noční zálohování do úložiště Azure a obnovení na jiné servery redundantní v jiných oblastech. 

V obou případech Pokud primární server dojde k výpadku, musíte změnit připojovací řetězce v generování sestav klientům připojit se k serveru v jiném datovém centru místní. Tato změna považovat za poslední možnost a dochází pouze v případě výpadku center závažné místní data. Je pravděpodobnější, výpadku datacentra hostování primární server by dostane zpět online, než může aktualizovat připojení na všechny klienty. 

Chcete-li předejít chcete změnit připojovací řetězce na generování sestav klientů, můžete vytvořit serveru [alias](analysis-services-server-alias.md) pro primární server. Pokud primární server přestane fungovat, můžete změnit alias tak, aby odkazoval na redundantní server v jiné oblasti. Můžete automatizovat alias pro název serveru pomocí kódování kontrolu stavu koncový bod na primárním serveru. Pokud se nezdaří Kontrola stavu, můžete přímo stejný koncový bod redundantní server v jiné oblasti. 

## <a name="related-information"></a>Související informace
[Zálohování a obnovení](analysis-services-backup.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md)   
[Názvy serverů alias](analysis-services-server-alias.md) 

