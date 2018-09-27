---
title: Plány údržby Azure (Preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat nezbytné plánované údržby události služby Azure SQL Data warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228309"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Použití plány údržby ke správě aktualizací služby a údržba

Azure SQL Data Warehouse údržby plánování je teď ve verzi preview. Tato nová funkce bez problémů integruje plánované údržby oznámení o stavu služby, zkontrolujte monitorování stavu prostředků a plánování služby Azure SQL Data Warehouse údržby.

Plánování údržby umožňuje naplánovat časový interval, kdy je vhodné pro příjem nových funkcí, upgrady a opravy. Zákazníci se vybrat primární a sekundární údržby v období 7 dní, to znamená sobota 22:00 – Neděle 01:00 (primární) a středu 19:00 – 22:00 (sekundární). Pokud se nám nedaří provést údržbu období primární údržby, pokusíme znova během období sekundární údržby.

Všechny nově vytvořené Azure SQL Data Warehouse instance bude mít plán údržby definované v systému použity během nasazení. Plán se dá upravit co nejdříve po dokončení nasazení.

Každé časové období údržby může být v rozmezí 3 až 8 hodinách, s 3hrs aktuálně prováděné nejkratší k dispozici možnost. Údržba může probíhat kdykoli v rámci okna a jako službu na váš datový sklad nasadí nový kód, měli byste očekávat (BRIEF) ke ztrátě připojení. 

Ve verzi preview funkce žádáme zákazníky k identifikaci jejich primárního a sekundárního systému windows v rámci samostatné rozsahů.   
Všechny operace údržby by se měl dokončit v rámci plánované údržby systému windows a žádná údržba bude probíhat mimo zadanou údržbu bez předchozího upozornění. Pokud je váš datový sklad pozastavený během plánované údržby, bude aktualizována při operaci obnovit.  


## <a name="alerts-and-monitoring"></a>Monitorování a výstrahy

Bezproblémová integrace s oznámení o stavu služby a stav prostředků zkontrolujte, zda monitorování umožňuje zákazníkům tak neustále informováni brzké činnosti údržby. Nové služby automation využívá Azure Monitor a umožňuje zákazníkům určíte, jak chtějí informováni o nadcházejícím události údržby a které automatizované toky aktivované ke správě prostoje a minimalizovat dopad na jejich operace.

Všechny události údržby předchází předběžných oznámeních 24 hod. Minimalizovat tak prostoje instance, měli byste zajistit, že nejsou žádné dlouhotrvající transakce na váš datový sklad před zahájením období zvolené údržby. Při zahájení údržby se zruší všechny aktivní relace, -potvrzené transakce bude vrácena zpět a datového skladu dojde k krátký ke ztrátě připojení. Ihned po dokončení údržby na váš datový sklad, budete upozorněni. 

Pokud jste dostali předběžná oznámení, že údržba bude probíhat, ale nepovedlo se nám provádění údržby během této doby, zobrazí se oznámení o zrušení. Údržba se pak obnoví během dalšího období naplánované údržby.
 
Všechny aktivní údržby události se zobrazí v "Stav služby - plánovanou údržbu" oddílu. Historie stavu služby v rámci budou uchovávat úplné záznamy o minulých událostech. Údržba se dá monitorovat prostřednictvím služby Azure Service Health kontrola řídicímu panelu portálu během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I v případě, že plánování údržby ještě není k dispozici ve vybrané oblasti, můžete pořád zobrazit a upravit plánu údržby kdykoli. Při plánování údržby k dispozici ve vaší oblasti, plán identifikovat okamžitě aktivní na váš datový sklad.


## <a name="next-steps"></a>Další postup

- [Další informace](viewing-maintenance-schedule.md) o zobrazení plánu údržby 
- [Další informace](changing-maintenance-schedule.md) o změně plán údržby
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazení a Správa výstrah pomocí Azure monitoru
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce Webhooku pro pravidla upozornění protokolů
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health







