---
title: Monitorování a optimalizace výkonu – Azure SQL Database | Dokumentace Microsoftu
description: Tipy pro ladění ve službě Azure SQL Database prostřednictvím hodnocení a zlepšování výkonu.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 0d728d81a29c5520938c8553c026727c0f94cc43
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956999"
---
# <a name="monitoring-and-performance-tuning"></a>Sledování a ladění výkonu

Azure SQL Database automaticky spravuje, a flexibilní datová služba, kde můžete snadno monitorovat využití, přidání nebo odebrání prostředků (procesoru, paměti, vstupně-výstupní operace), najít doporučení, která může zlepšit výkon vaší databáze, nebo nechat databáze přizpůsobit vaší úloze a automaticky optimalizace výkonu.

## <a name="monitoring-database-performance"></a>Monitorování výkonu databáze

Monitorování výkonu databáze SQL v Azure začíná sledováním využití prostředků relativně ke zvolené úrovni výkonu databáze. Azure SQL Database vám pomůže identifikovat příležitosti, jak vylepšit a optimalizovat výkon dotazů beze změny zdroje kontrolou [doporučení pro optimalizaci výkonu](sql-database-advisor.md). Častým důvodem toho, že databáze je pomalá, jsou chybějící indexy a nedostatečně optimalizované dotazy. Můžete použít tato doporučení pro vyladění výkonu vašich úloh. Můžete také umožňují Azure SQL database a [automaticky optimalizace výkonu vašich dotazů](sql-database-automatic-tuning.md) použitím všechny určené doporučení a ověřuje, že pomáhají zvýšit výkon databáze.

Máte následující možnosti pro monitorování a řešení potíží s výkonem databáze:

- V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **databází SQL**, vyberte databázi a pak použít graf monitorování k vyhledání prostředků blíží jejich maximální. Ve výchozím nastavení se zobrazí spotřeba DTU. Klikněte na tlačítko **upravit** změnit časový rozsah a hodnoty.
- Použití [Query Performance Insight](sql-database-query-performance.md) identifikovat dotazy, které tráví nejvíce prostředků.
- Použití [služby SQL Database Advisor](sql-database-advisor-portal.md) Chcete-li zobrazit doporučení pro vytváření a vyřazení indexů, parametrické dotazy a řešení problémů schématu.
- Použití [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pro automatické monitorování výkonu vaší databáze. Po zjištění problému s výkonem se vygeneruje protokol diagnostiky s podrobnostmi a kořenové příčiny analýzy RCA () problému. Pokud je to možné, poskytuje doporučení pro zlepšení výkonu.
- [Povolení automatického ladění](sql-database-automatic-tuning-enable.md) a nechat Azure SQL database automaticky opravě zjištěných problémů s výkonem.
- Použití [zobrazení dynamické správy (DMV)](sql-database-monitoring-with-dmvs.md), [rozšířených událostí](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)a [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) podrobný Poradce při potížích s problémy s výkonem.

> [!TIP]
> Zobrazit [Průvodce výkonem](sql-database-performance-guidance.md) najít techniky, které můžete použít ke zlepšení výkonu služby Azure SQL Database po identifikaci problému výkonu pomocí jednoho nebo více z výše uvedených metod.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorování databází na portálu Azure

V [webu Azure portal](https://portal.azure.com/), můžete monitorovat využití izolované databáze s výběrem databáze a kliknutím na **monitorování** grafu. Zobrazí se okno **Metrika**, které můžete upravit kliknutím na **Upravit graf**. Přidejte následující metriky:

- Procento CPU
- Procento DTU
- Procento datových V/V
- Procento velikosti databáze

Jakmile přidáte tyto metriky, můžete pokračovat jejich zobrazením v **monitorování** graf s dalšími informacemi o **metrika** okna. Tyto čtyři metriky uvádějí průměrné využití v procentech vzhledem k hodnotě **DTU** vaší databáze. Zobrazit [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) články pro další informace o úrovních služeb.  

![Monitorování výkonu databáze v rámci úrovně služeb](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Můžete také nastavit upozornění na výkonové metriky. Klikněte na tlačítko **Přidat upozornění** v okně **Metrika**. Nastavte upozornění podle pokynů průvodce. Můžete určit, zda chcete být upozorněni na překročení zadané prahové hodnoty, nebo naopak když metrika poklesne pod zadanou mez.

Například pokud očekáváte nárůst zatížení databáze, můžete nastavit e-mailové upozornění pro případ, že databáze překročí 80 % kterékoli výkonové metriky. Můžete to použít jako včasné varování zjistit, když bude pravděpodobně nutné přepnout na další nejvyšší velikost výpočetní prostředky.

Metriky výkonu také můžete zjistit, zda je možné nižší výpočty velikosti. Předpokládejme, že používáte databáze S2 v úrovni Standard a všechny metriky ukazují, že databáze v průměru nevyužívá více než 10 % dostupného výkonu. Je pravděpodobné, že databáze bude dobře fungovat i v úrovni Standard S1. Nezapomínejte, úloh, které výskytu špiček nebo náhlého před provedením kolísání nižší výpočty velikosti.

## <a name="troubleshoot-performance-issues"></a>Řešení potíží s výkonem

K diagnostikování a vyřešení problémů s výkonem, začněte tím, že vysvětlení stavu každý aktivní dotaz a podmínek, které způsobují problémy s výkonem relevantní pro každý stav úlohy. Kvůli zvýšení výkonu Azure SQL Database se pochopit, že každý požadavek aktivní dotaz z vaší aplikace je buď ve stavu čekání nebo spuštěné. Při řešení problému s výkonem ve službě Azure SQL Database, mějte v následujícím grafu jako jste si tento článek k diagnostikování a vyřešení problémů s výkonem.

![Stavy úlohy](./media/sql-database-monitor-tune-overview/workload-states.png)

Pro úlohy s problémy s výkonem, tyto problémy s výkonem pravděpodobně z důvodu kolize procesoru ( **týkající se spouštění** podmínky) nebo jednotlivé dotazy čekají na libovolnou položku ( **související čekání** podmínku ).

## <a name="running-related-performance-issues"></a>Problémy s výkonem souvisejících s spuštění

V rámci obecných pokynů Pokud vaše využití procesoru je trvale dosahovalo nebo přesahovalo 80 %, je nutné problém s výkonem souvisejících s spuštěná. Pokud máte potíže se související se spuštěná, může být způsobena nedostatkem prostředků procesoru nebo může mít relaci k jednomu z následujících podmínek:

- Moc velký počet spuštěných dotazů
- Příliš mnoho kompilaci dotazů
- Jeden nebo více provádění dotazů používají plán neoptimálním průběhem dotazu

Pokud zjistíte, že máte problém s výkonem souvisejících s běží, je vaším cílem je k jeho identifikaci přesné pomocí jedné nebo několika metod. Nejběžnější metody k identifikaci problémů souvisejících se spuštění jsou následující:

- Použití [webu Azure portal](#monitor-databases-using-the-azure-portal) k monitorování procenta využití procesoru.
- Pomocí následujících [zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi. Historická data se udržují za jednu hodinu.
  - [Sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) vrátí data o využití a úložiště CPU pro službu Azure SQL Database. Data se shromažďují a agregují v pětiminutovém intervalu.

> [!IMPORTANT]
> Sada dotazů T-SQL pomocí těchto zobrazení dynamické správy k řešení potíží využití procesoru, naleznete v tématu [procesoru identifikovat problémy s výkonem](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

Jakmile identifikujete problém, můžete ladit problémových dotazů nebo upgradovat výpočetního prostředí nebo ke zvýšení kapacity databáze Azure SQL a chránit před požadavky na procesor úroveň služby. Informace o škálování prostředků pro izolované databáze, najdete v části [škálování izolované databáze prostředků ve službě Azure SQL Database](sql-database-single-database-scale.md) a škálování prostředků pro elastické fondy, najdete v části [škálování elastického fondu prostředků ve službě Azure SQL Databáze](sql-database-elastic-pool-scale.md). Informace o vertikálním navýšení managed instance najdete v tématu [omezení prostředků na úrovni Instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

## <a name="waiting-related-performance-issues"></a>Problémy s výkonem souvisejících s čekání

Jakmile jste si jisti, že nejsou připojena vysoké využití procesoru, problémy s výkonem souvisejících s spuštění, máte problém s výkonem souvisejících s čekání. Konkrétně vaše prostředky procesoru nejsou používány efektivně protože procesoru čeká na jiný prostředek. V takovém případě bude dalším krokem je identifikovat, co čeká prostředky procesoru. Nejběžnější metody pro zobrazení na začátek čekání kategorií typů:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) poskytuje statistiky čekání každý dotaz v čase. Na Query Store čekání typy jsou sloučeny do kategorií čekání. Je k dispozici v mapování kategorií wait čekat typy [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [Sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) vrátí informace o všech čeká tak vlákna, která provedených během operace. Toto souhrnné zobrazení můžete použít k diagnostice problémů s výkonem s Azure SQL Database a také s konkrétní dotazy a dávek.
- [Sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) vrátí informace o frontě čekání úlohy, které čekají na některých prostředků.

Jak je znázorněno v předchozí tabulce, jsou nejčastěji používané čeká:

- Zámky (blokování)
- VSTUPNĚ-VÝSTUPNÍCH OPERACÍ
- `tempdb`-týkající se sporů
- Paměť udělení čeká

> [!IMPORTANT]
> Sada dotazů T-SQL pomocí těchto zobrazení dynamické správy k řešení těchto problémů souvisejících s čekání najdete tady:
>
> - [Identifikace problémů s výkonem vstupně-výstupních operací](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikujte `tempdb` problémy s výkonem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifikujte paměti udělení čeká](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

## <a name="improving-database-performance-with-more-resources"></a>Zlepšení výkonu databáze s více prostředky

Nakonec pokud nebyly nalezeny žádné užitečné položky, které může zlepšit výkon vaší databáze, můžete změnit množství prostředků, které jsou k dispozici ve službě Azure SQL Database. Můžete přiřadit více prostředků tak, že změníte [jednotek DTU úrovně](sql-database-service-tiers-dtu.md) izolované databáze nebo zvýšení Edtu elastického fondu v každém okamžiku. Případně pokud používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md), můžete změnit úroveň služby nebo zvýšit prostředky přidělené k vaší databázi.

1. Pro izolované databáze můžete [změna úrovní služeb](sql-database-service-tiers-dtu.md) nebo [výpočetní prostředky](sql-database-service-tiers-vcore.md) na vyžádání ke zlepšení výkonu databáze.
2. Pro více databází, zvažte použití [elastické fondy](sql-database-elastic-pool-guidance.md) automaticky škálovat prostředky.

## <a name="tune-and-refactor-application-or-database-code"></a>Ladění a Refaktorujte aplikace nebo kódu databáze

Můžete změnit kód aplikace více optimálně použít databázi, změňte indexy, vynutit plány nebo používání pomocné parametry ručně přizpůsobit databáze vaší úloze. Najdete některé pokyny a tipy pro ruční optimalizace a přepsáním kódu v [tématu pokyny k výkonu](sql-database-performance-guidance.md) článku.

## <a name="next-steps"></a>Další postup

- Povolení automatického ladění ve službě Azure SQL Database a nechat funkce automatického ladění plně spravovat vaše úlohy najdete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li použít ruční ladění, můžete zkontrolovat [doporučení na webu Azure portal pro optimalizaci](sql-database-advisor-portal.md) a aplikovat ručně ty, které zlepšují výkon vašich dotazů.
- Změnit prostředky, které jsou k dispozici v databázi tak, že změníte [úrovně služby Azure SQL Database](sql-database-performance-guidance.md)
