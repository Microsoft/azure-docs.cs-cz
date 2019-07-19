---
title: Plány údržby Azure (Preview) | Microsoft Docs
description: Plánování údržby umožňuje zákazníkům plánovat z nezbytných plánovaných událostí údržby, které služba Azure SQL Data Warehouse používá k zavedení nových funkcí, upgradů a oprav.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278110"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Použití plánů údržby ke správě aktualizací a údržby služby

Plány údržby jsou nyní k dispozici ve všech oblastech Azure SQL Data Warehouse. Tato funkce integruje Service Health plánovaná oznámení o údržbě, Resource Health kontrolu monitorování a službu Azure SQL Data Warehouse plánování údržby.

Pomocí plánování údržby můžete zvolit časový interval, kdy je vhodné dostávat nové funkce, upgrady a opravy. V rámci sedmi dnů zvolíte primární a sekundární časové období údržby. Příkladem může být primární okno sobotu 22:00 až neděle 01:00 a sekundární okno z středy 19:00 až 22:00. Pokud SQL Data Warehouse nemůže provést údržbu během primárního okna údržby, pokusí se o údržbu znovu během sekundárního časového období údržby. Údržba služby může nastat jak v primárních, tak v sekundárních oknech. Aby se zajistilo rychlé dokončení všech operací údržby, DW400 (c) a nižší úrovně datového skladu mohou dokončit údržbu mimo určené časové období údržby.

Všechny nově vytvořené Azure SQL Data Warehouse instance budou mít během nasazování použit plán údržby definovaný systémem. Plán se dá upravit hned po dokončení nasazení.

Každé okno údržby může být tři až osm hodin. K údržbě může dojít kdykoli v rámci okna. Při spuštění údržby se zruší všechny aktivní relace a nepotvrzené transakce se vrátí zpět. V připojení byste měli očekávat několik krátkých ztrát, protože služba do datového skladu nasadí nový kód. Po dokončení údržby na datovém skladu budete upozorněni okamžitě.

Chcete-li použít tuto funkci, je nutné určit primární a sekundární okno v rámci samostatného denního rozsahu. Všechny operace údržby by se měly dokončit v plánovaných oknech údržby. Žádná údržba se neprovádí mimo zadaná časová období údržby bez předchozího oznámení. Pokud je váš datový sklad pozastaven během plánované údržby, bude aktualizován během operace obnovení.  

## <a name="alerts-and-monitoring"></a>Výstrahy a monitorování

Integrace s Service Healthmi oznámeními a monitorováním Resource Health kontroly umožňuje zákazníkům průběžně informovat o hrozící činnosti údržby. Nové automatické automatizace využívají Azure Monitor. Můžete rozhodnout, jak chcete být upozorňováni na hrozící události údržby. Také se rozhodněte, které automatizované toky vám pomohou spravovat výpadky a minimalizovat dopad na vaše operace.

24hodinové předběžné oznámení předchází všem událostem údržby s aktuální výjimkou DW400c a nižších úrovní. Chcete-li minimalizovat prostoje instance, ujistěte se, že datový sklad neobsahuje žádné dlouhotrvající transakce před zvoleným obdobím údržby.

> [!NOTE]
> V případě, že je nutné nasadit časově důležitou aktualizaci, může být rozšířená doba oznámení výrazně omezená.

Pokud jste obdrželi předběžné oznámení, že se údržba provede, ale SQL Data Warehouse nemůže během této doby provádět údržbu, obdržíte oznámení o zrušení. Údržba pak bude pokračovat během příští plánované doby údržby.

V části **Service Health-plánovaná údržba** se zobrazí všechny aktivní události údržby. Historie Service Health obsahuje úplný záznam minulých událostí. Údržbu můžete sledovat pomocí Azure Service Health řídicího panelu portálu během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I když ve zvolené oblasti není k dispozici plánování údržby, můžete plán údržby kdykoli zobrazit a upravit. Když bude plánování údržby k dispozici ve vaší oblasti, identifikovaný plán se okamžitě aktivuje v datovém skladu.

## <a name="next-steps"></a>Další postup

- [Přečtěte si další informace](viewing-maintenance-schedule.md) o zobrazení plánu údržby.
- [Přečtěte si další informace](changing-maintenance-schedule.md) o změně plánu údržby.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazování a správě výstrah pomocí Azure monitor.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcích Webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Vytváření a Správa skupin akcí.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.
