---
title: Vysoká dostupnost služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak služba Azure Analysis Services poskytuje vysokou dostupnost během přerušení služby.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 78a6d41b638d79111a58830f0cb0d5190ea0796c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408673"
---
# <a name="analysis-services-high-availability"></a>Analýza Services vysoká dostupnost

Tento článek popisuje zajištění vysoké dostupnosti pro servery Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zajištění vysoké dostupnosti během přerušení služby

Zatímco vzácné, datové centrum Azure může mít výpadek. Dojde-li k výpadku, způsobí přerušení podnikání, které může trvat několik minut nebo může trvat několik hodin. Vysoká dostupnost je nejčastěji dosažena s redundancí serveru. Pomocí služby Azure Analysis Services můžete dosáhnout redundance vytvořením dalších sekundárních serverů v jedné nebo více oblastech. Chcete-li zajistit, aby data a metadata na těchto serverech byla synchronizována se serverem v oblasti, která přešla do režimu offline, můžete zajistit, aby data a metadata na těchto serverech byla synchronizována se serverem v oblasti, která přešla do režimu offline, můžete:

* Nasaďte modely na redundantní servery v jiných oblastech. Tato metoda vyžaduje zpracování dat na primárním i redundantním serveru paralelně, což je zajištění synchronizace všech serverů.

* [Zálohujte](analysis-services-backup.md) databáze z primárního serveru a obnovujte je na redundantních serverech. Můžete například automatizovat noční zálohování do úložiště Azure a obnovit na jiné redundantní servery v jiných oblastech. 

V obou případech, pokud váš primární server dojde k výpadku, je nutné změnit připojovací řetězce v hlášení klientů pro připojení k serveru v jiném místním datovém centru. Tato změna by měla být považována za poslední možnost a pouze v případě, že dojde ke katastrofickému výpadku regionálního datového centra. Je pravděpodobnější, že výpadek datového centra hostující primární server se vrátí do režimu online dříve, než budete muset aktualizovat připojení na všech klientech. 

Chcete-li se vyhnout nutnosti měnit připojovací řetězce u klientů sestav, můžete vytvořit [alias](analysis-services-server-alias.md) serveru pro primární server. Pokud primární server přejde dolů, můžete změnit alias tak, aby ukazoval na redundantní server v jiné oblasti. Alias na název serveru můžete automatizovat kódováním kontroly stavu koncového bodu na primárním serveru. Pokud se kontrola stavu nezdaří, stejný koncový bod může být přímo na redundantní server v jiné oblasti. 

## <a name="related-information"></a>Související informace

[Zálohování a obnovení](analysis-services-backup.md)   
[Správa služby Azure Analysis Services](analysis-services-manage.md)   
[Alias názvy serverů](analysis-services-server-alias.md) 

