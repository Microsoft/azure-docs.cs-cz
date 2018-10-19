---
title: Plány údržby Azure (preview) | Dokumentace Microsoftu
description: Plánování údržby umožňuje zákazníkům naplánovat události nezbytné plánované údržby, které služba Azure SQL Data Warehouse používá k zavedení nových funkcí, upgrady a opravy.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c2ed79673af3563ae62f516057a174770cda99e9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427858"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Umožňuje spravovat aktualizace služby a údržbu a plány údržby

Plánování údržby v Azure SQL Data Warehouse je teď ve verzi preview. Tato funkce se integruje plánované údržby oznámení o stavu služby, zkontrolujte monitorování stavu prostředků a plánování služby Azure SQL Data Warehouse údržby.

Použijete údržby plánování vybrat časový interval, když je vhodné pro příjem nových funkcí, upgrady a opravy. Vyberte primární a sekundární údržby v rámci sedm dnů. Příkladem je primární okno sobota 22:00 pro pondělí 01:00 a sekundární okno ze středu 19:00 do 22:00. Pokud datový sklad SQL nelze provést údržbu období primární údržby, pokusí znovu během období údržby sekundární údržbu.

Všechny nově vytvořené Azure SQL Data Warehouse instance bude mít plán údržby definované v systému použity během nasazení. Plán se dá upravit co nejdříve po dokončení nasazení.

Každé časové období údržby, může být tři až osm hodin. Údržba může probíhat kdykoli v rámci okna. Jak služba nasadí nový kód do datového skladu, měli byste očekávat (BRIEF) ke ztrátě připojení. 

Ve verzi preview funkce identifikujete primární a sekundární windows v rámci samostatné rozsahů. Všechny operace údržby by měla dokončit během plánované údržby systému windows. Žádná údržba bude probíhat mimo zadanou údržbu bez předchozího upozornění. Pokud je váš datový sklad pozastavený během plánované údržby, bude aktualizována při operaci obnovit.  


## <a name="alerts-and-monitoring"></a>Monitorování a výstrahy

Integrace s oznámení o stavu služby a zkontrolujte monitorování stavu prostředků umožňuje zákazníkům tak neustále informováni brzké činnosti údržby. Nové služby automation využívá Azure Monitor. Můžete se rozhodnout, způsob, jak mají být informováni o nadcházejícím události údržby. Také rozhodněte, které automatizované toky vám může pomoct spravovat prostoje a minimalizovat dopad na vaše operace.

24 hodin předběžných oznámeních předchází všechny události údržby. Minimalizovat tak prostoje instance, ujistěte se, že váš datový sklad nemá žádné dlouhotrvající transakce před zvolený udržovací období. Při zahájení údržby, zruší se všechny aktivní relace. Noncommitted transakce bude vrácena zpět a datového skladu dojde k krátký ke ztrátě připojení. Ihned po dokončení údržby na váš datový sklad, budete upozorněni. 

Pokud jste dostali předběžná oznámení, že údržba bude probíhat, ale SQL Data Warehouse nemůže provést údržbu během této doby, obdržíte oznámení o zrušení. Údržba se pak obnoví během dalšího období naplánované údržby.
 
Zobrazí všechny události údržby aktivní v **Service Health – plánovanou údržbu** oddílu. Historie stavu služby zahrnuje celý záznam o minulých událostech. Údržba prostřednictvím služby Azure Service Health kontrola řídicímu panelu portálu můžete monitorovat během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I v případě plánování údržby není k dispozici ve vybrané oblasti, můžete zobrazit a upravit plánu údržby kdykoli. Při plánování údržby k dispozici ve vaší oblasti, identifikovaný plánu je okamžitě aktivní na váš datový sklad.


## <a name="next-steps"></a>Další postup

- [Další informace](viewing-maintenance-schedule.md) o zobrazení plánu údržby. 
- [Další informace](changing-maintenance-schedule.md) o změně plánu údržby.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazení a Správa výstrah pomocí Azure monitoru.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.







