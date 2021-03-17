---
title: Plány údržby pro synapse fond SQL
description: Plánování údržby umožňuje zákazníkům plánovat z nezbytných plánovaných událostí údržby, které Azure synapse Analytics používá k zavedení nových funkcí, upgradů a oprav.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590212"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Použití plánů údržby ke správě aktualizací a údržby služby

Funkce plánu údržby integruje Service Health plánovaná oznámení o údržbě, Resource Health kontrolu monitorování a plánování údržby pro synapse fond SQL (datový sklad) v rámci služby Azure synapse Analytics.

Pro výběr časového okna byste měli použít plánování údržby, pokud je vhodné dostávat nové funkce, upgrady a opravy. V rámci sedmi dnů budete muset zvolit primární a sekundární časové období údržby, každé okno musí být v různých dnech.

Například můžete naplánovat primární okno sobotu 22:00 na neděli 01:00 a potom naplánovat sekundární okno z středy 19:00 na 22:00. Pokud se údržba nedá provést během primárního časového období údržby, pokusí se o údržbu znovu během sekundárního časového období údržby. V primárním i sekundárním systému Windows může dojít k údržbě služeb. Aby se zajistilo rychlé dokončení všech operací údržby, DW400c a nižší úrovně datového skladu mohly dokončit údržbu mimo určené časové období údržby.

Všechny nově vytvořené instance datového skladu budou mít k dispozici plán údržby definovaný systémem, který bude použit během nasazování. Plán se dá upravit hned po dokončení nasazení.

I když může být časový interval pro správu a údržbu mezi třemi a osmi hodinami, znamená to, že datový sklad bude po dobu až do stavu offline. K údržbě může dojít kdykoli v rámci tohoto okna a v průběhu tohoto intervalu byste měli očekávat jedno odpojení. ~ 5 -6 minut, protože služba nasadí nový kód do datového skladu. DW400c a nižší můžou v různých časech během časového období údržby docházet k několika krátkým ztrátám v připojení. Při spuštění údržby se zruší všechny aktivní relace a nepotvrzené transakce se vrátí zpět. Chcete-li minimalizovat prostoje instance, ujistěte se, že datový sklad neobsahuje žádné dlouhotrvající transakce před zvoleným obdobím údržby.

Všechny operace údržby by se měly dokončit v zadaném časovém období údržby, pokud není nutné nasadit aktualizaci s časovým rozlišením. Pokud je váš datový sklad pozastaven během plánované údržby, bude aktualizován během operace obnovení. Po dokončení údržby datového skladu budete upozorněni okamžitě.

## <a name="alerts-and-monitoring"></a>Výstrahy a monitorování

Integrace s Service Healthmi oznámeními a monitorováním Resource Health kontroly umožňuje zákazníkům průběžně informovat o hrozící činnosti údržby. Tato automatizace využívá Azure Monitor. Můžete rozhodnout, jak chcete být upozorňováni na hrozící události údržby. Můžete také zvolit, které automatizované toky vám pomůžou spravovat výpadky a minimalizovat provozní dopad.

24hodinové předběžné oznámení předchází všem událostem údržby, které nejsou pro DWC400c a nižší úrovně.

> [!NOTE]
> V případě, že je nutné nasadit časově důležitou aktualizaci, může být rozšířená doba oznámení výrazně omezená. K tomu může dojít mimo identifikované okno údržby z důvodu kritického charakteru aktualizace.

Pokud jste obdrželi předběžné oznámení o tom, že údržba proběhne, ale během časového období v oznámení nelze provést údržbu, obdržíte oznámení o zrušení. Údržba pak bude pokračovat během příští plánované doby údržby.

V části **Service Health-plánovaná údržba** se zobrazí všechny aktivní události údržby. Historie Service Health obsahuje úplný záznam minulých událostí. Údržbu můžete sledovat pomocí Azure Service Health řídicího panelu portálu během aktivní události.

### <a name="maintenance-schedule-availability"></a>Dostupnost plánu údržby

I když ve zvolené oblasti není k dispozici plánování údržby, můžete plán údržby kdykoli zobrazit a upravit. Když bude plánování údržby k dispozici ve vaší oblasti, identifikovaný plán se okamžitě aktivuje ve vašem synapse fondu SQL.

## <a name="view-a-maintenance-schedule"></a>Zobrazit plán údržby

Ve výchozím nastavení mají všechny nově vytvořené instance datového skladu během nasazení osm hodin primární a sekundární časové období údržby. Jak je uvedeno výše, můžete změnit Windows tak, jak brzo nasazování je dokončeno. Mimo zadaná časová období údržby nedojde bez předchozího upozornění k žádné údržbě.

Chcete-li zobrazit plán údržby, který byl použit pro váš synapse fond SQL, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte synapse fond SQL, který chcete zobrazit.
3. Vybraný synapse fond SQL se otevře v okně Přehled. Plán údržby, který se použije pro datový sklad, se zobrazí pod **plánem údržby**.

![Okno s přehledem](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Změna plánu údržby

Plán údržby lze kdykoli aktualizovat nebo změnit. Pokud vybraná instance projde aktivním cyklem údržby, nastavení se uloží. Budou aktivní během další identifikované doby údržby. [Přečtěte si další informace](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o monitorování datového skladu během aktivní události údržby.

## <a name="identifying-the-primary-and-secondary-windows"></a>Určení primárního a sekundárního okna

Primární a sekundární systém Windows musí mít oddělené rozsahy dnů. Příkladem je primární okno úterý – čtvrtek a sekundární okno v sobotu – neděle.

Chcete-li změnit plán údržby pro synapse fond SQL, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte synapse fond SQL, který chcete aktualizovat. Otevře se stránka s přehledem.
Kliknutím na odkaz **Souhrn plánu údržby** v okně Přehled otevřete stránku nastavení plánu údržby. Případně vyberte možnost **plán údržby** v nabídce na levé straně prostředku.

    ![Možnosti okna Přehled](./media/maintenance-scheduling/maintenance-change-option.png)

3. Pomocí možností v horní části stránky určete upřednostňovaný rozsah dní pro primární časové období údržby. Tento výběr určuje, zda bude primární okno provedeno v den v týdnu nebo za víkend. Váš výběr aktualizuje hodnoty rozevíracího seznamu.
V průběhu období Preview možná některé oblasti ještě nepodporují úplnou sadu dostupných možností **dne** .

   ![Okno nastavení údržby](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Pomocí rozevíracích seznamů vyberte upřednostňovaná primární a sekundární časová období údržby:
   - **Den**: upřednostňovaný den pro provedení údržby během vybraného okna.
   - **Čas spuštění**: upřednostňovaná počáteční doba pro časové období údržby.
   - **Časový interval**: Upřednostňovaná doba trvání časového intervalu.

   Oblast **Souhrn plánu** v dolní části okna je aktualizována na základě hodnot, které jste vybrali.
  
5. Vyberte **Uložit**. Zobrazí se zpráva s potvrzením, že nový plán je teď aktivní.

   Pokud ukládáte plán v oblasti, která nepodporuje plánování údržby, zobrazí se následující zpráva. Vaše nastavení se uloží a stane se aktivním, když je funkce dostupná ve vybrané oblasti.

   ![Zpráva o dostupnosti oblasti](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o vytváření, zobrazování a správě výstrah pomocí Azure monitor.
- [Přečtěte si další informace](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o akcích Webhooku pro pravidla upozornění protokolů.
- [Další informace](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Vytváření a Správa skupin akcí.
- [Přečtěte si další informace](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o Azure Service Health.
