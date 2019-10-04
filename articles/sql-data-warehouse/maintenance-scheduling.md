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
ms.openlocfilehash: 0e5df583112bbb6db9651004e5deaceb6b5b9d12
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958899"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Použití plánů údržby ke správě aktualizací a údržby služby

Plány údržby jsou nyní k dispozici ve všech oblastech Azure SQL Data Warehouse. Funkce plán údržby integruje Service Health plánovaná oznámení o údržbě, Resource Health kontrolu monitorování a službu Azure SQL Data Warehouse plánování údržby.

Pomocí plánování údržby můžete zvolit časový interval, kdy je vhodné dostávat nové funkce, upgrady a opravy. V rámci sedmi dnů zvolíte primární a sekundární časové období údržby. Chcete-li použít tuto funkci, je nutné určit primární a sekundární okno v rámci samostatného denního rozsahu.

Například můžete naplánovat primární okno sobotu 22:00 na neděli 01:00 a potom naplánovat sekundární okno z středy 19:00 na 22:00. Pokud SQL Data Warehouse nemůže provést údržbu během primárního okna údržby, pokusí se o údržbu znovu během sekundárního časového období údržby. Údržba služby by mohla probíhat během primárního i sekundárního okna. Aby se zajistilo rychlé dokončení všech operací údržby, DW400 (c) a nižší úrovně datového skladu mohou dokončit údržbu mimo určené časové období údržby.

Všechny nově vytvořené Azure SQL Data Warehouse instance budou mít během nasazování použit plán údržby definovaný systémem. Plán se dá upravit hned po dokončení nasazení.

Každé časové období údržby může být tři až osm hodin. K údržbě může dojít kdykoli v rámci okna. Při spuštění údržby se zruší všechny aktivní relace a nepotvrzené transakce se vrátí zpět. V připojení byste měli očekávat několik krátkých ztrát, protože služba do datového skladu nasadí nový kód. Po dokončení údržby datového skladu budete upozorněni okamžitě.

 Všechny operace údržby by se měly dokončit v plánovaných oknech údržby. Žádná údržba se neprovádí mimo zadaná časová období údržby bez předchozího oznámení. Pokud je váš datový sklad pozastaven během plánované údržby, bude aktualizován během operace obnovení. 

## <a name="alerts-and-monitoring"></a>Výstrahy a monitorování

Integrace s Service Healthmi oznámeními a monitorováním Resource Health kontroly umožňuje zákazníkům průběžně informovat o hrozící činnosti údržby. Nové automatické automatizace využívají Azure Monitor. Můžete rozhodnout, jak chcete být upozorňováni na hrozící události údržby. Můžete také zvolit, které automatizované toky vám pomůžou spravovat výpadky a minimalizovat provozní dopad.

24hodinové předběžné oznámení předchází všem událostem údržby, které nejsou pro DWC400c a nižší úrovně. Chcete-li minimalizovat prostoje instance, ujistěte se, že datový sklad neobsahuje žádné dlouhotrvající transakce před zvoleným obdobím údržby.

> [!NOTE]
> V případě, že je nutné nasadit časově důležitou aktualizaci, může být rozšířená doba oznámení výrazně omezená.

Pokud jste obdrželi předběžné oznámení, že se údržba provede, ale SQL Data Warehouse nemůže během této doby provádět údržbu, obdržíte oznámení o zrušení. Údržba pak bude pokračovat během příští plánované doby údržby.

V části **Service Health-plánovaná údržba** se zobrazí všechny aktivní události údržby. Historie Service Health obsahuje úplný záznam minulých událostí. Údržbu můžete sledovat pomocí Azure Service Health řídicího panelu portálu během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I když ve zvolené oblasti není k dispozici plánování údržby, můžete plán údržby kdykoli zobrazit a upravit. Když bude plánování údržby k dispozici ve vaší oblasti, identifikovaný plán se okamžitě aktivuje v datovém skladu.

## <a name="view-a-maintenance-schedule"></a>Zobrazit plán údržby 

### <a name="portal"></a>Bran

Ve výchozím nastavení mají všechny nově vytvořené Azure SQL Data Warehouse instance během nasazování po dobu osmi hodin primární a sekundární okno údržby. Jak je uvedeno výše, můžete změnit Windows tak, jak brzo nasazování je dokončeno. Žádná údržba se neprovádí mimo zadaná časová období údržby bez předchozího oznámení.

Chcete-li zobrazit plán údržby, který byl použit pro datový sklad, proveďte následující kroky:

1.  Přihlaste se k [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete zobrazit. 
3.  Vybraný datový sklad se otevře v okně Přehled. Plán údržby, který se použije pro datový sklad, se zobrazí pod **plánem údržby**.

![Okno s přehledem](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Změna plánu údržby 

### <a name="portal"></a>Bran
Plán údržby lze kdykoli aktualizovat nebo změnit. Pokud vybraná instance projde aktivním cyklem údržby, nastavení se uloží. Budou aktivní během další identifikované doby údržby. [Přečtěte si další informace](https://docs.microsoft.com/azure/service-health/resource-health-overview) o monitorování datového skladu během aktivní události údržby. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Určení primárního a sekundárního okna

Primární a sekundární systém Windows musí mít oddělené rozsahy dnů. Příkladem je primární okno úterý – čtvrtek a sekundární okno v sobotu – neděle.

Chcete-li změnit plán údržby pro datový sklad, proveďte následující kroky:
1.  Přihlaste se k [Azure Portal](https://portal.azure.com/).
2.  Vyberte datový sklad, který chcete aktualizovat. Otevře se stránka s přehledem. 
3.  Otevřete stránku nastavení plánu údržby výběrem odkazu **Souhrn plánu údržby (Preview)** v okně Přehled. Případně vyberte možnost **plán údržby** v nabídce na levé straně prostředku.  

    ![Možnosti okna Přehled](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Pomocí možností v horní části stránky určete upřednostňovaný rozsah dní pro primární časové období údržby. Tento výběr určuje, zda bude primární okno provedeno v den v týdnu nebo za víkend. Váš výběr aktualizuje hodnoty rozevíracího seznamu. V průběhu období Preview možná některé oblasti ještě nepodporují úplnou sadu dostupných možností **dne** .

   ![Okno nastavení údržby](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Pomocí rozevíracích seznamů vyberte upřednostňovaná primární a sekundární časová období údržby:
   - **Den**: upřednostňovaný den pro provedení údržby během vybraného okna.
   - **Čas spuštění**: upřednostňovaná počáteční doba pro časové období údržby.
   - **Časový interval**: Upřednostňovaná doba trvání časového intervalu.

   Oblast **Souhrn plánu** v dolní části okna je aktualizována na základě hodnot, které jste vybrali. 
  
6. Vyberte **Uložit**. Zobrazí se zpráva s potvrzením, že nový plán je teď aktivní. 

   Pokud ukládáte plán v oblasti, která nepodporuje plánování údržby, zobrazí se následující zpráva. Vaše nastavení se uloží a stane se aktivním, když je funkce dostupná ve vybrané oblasti.    

   ![Zpráva o dostupnosti oblasti](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Další kroky
- [Přečtěte si další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) o vytváření, zobrazování a správě výstrah pomocí Azure monitor.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) o akcích Webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Vytváření a Správa skupin akcí.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health.