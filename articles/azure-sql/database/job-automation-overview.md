---
title: Přehled automatizace úloh s elastickými úlohami
description: Použití elastických úloh pro automatizaci úloh ke spouštění skriptů jazyka Transact-SQL (T-SQL) v sadě jedné nebo více databází
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 942698e5c42e1f46ff05dacdacdb0d124135a6c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390755"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatizace úloh správy pomocí elastických úloh (Preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Můžete vytvářet a plánovat elastické úlohy, které se můžou pravidelně spouštět v jedné nebo mnoha databázích SQL Azure a spouštět dotazy Transact-SQL (T-SQL) a provádět úlohy údržby. 

Můžete definovat cílovou databázi nebo skupinu databází, ve kterých se úloha spustí, a také plány spouštění úlohy.
Úloha zajišťuje přihlášení k cílové databázi. Můžete také definovat, spravovat a uchovávat skripty Transact-SQL, které se spustí pro skupinu databází.

Každá úloha protokoluje stav spuštění a v případě jakéhokoli selhání také automaticky opakuje operace.

## <a name="when-to-use-elastic-jobs"></a>Kdy použít elastické úlohy

K dispozici je několik scénářů, kdy můžete použít automatizaci elastických úloh:

- Automatizujte úlohy správy a naplánujte, aby se spouštěly každý den v týdnu, a to po hodinách atd.
  - Nasazování změn schématu, správa přihlašovacích údajů, shromažďování dat o výkonu nebo shromažďování telemetrických dat tenantů (zákazníků).
  - Aktualizujte referenční data (společné informace napříč všemi databázemi), načtěte data z úložiště objektů BLOB v Azure.
- Konfigurace spouštění úloh pro kolekci databází nebo jejich opakovaného spouštění, například v době mimo špičku.
  - Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Shromažďování dat pro účely generování sestav
  - Agregovaná data z kolekce databází do jedné cílové tabulky.
  - Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.
- Přesuny dat 

### <a name="automation-on-other-platforms"></a>Automatizace na jiných platformách

Vezměte v úvahu následující technologie plánování úloh na různých platformách:

- **Elastické úlohy** jsou služby plánování úloh, které spouštějí vlastní úlohy v jedné nebo mnoha databázích v Azure SQL Database.
- **Úlohy agenta SQL** provádí služba SQL Agent, která se používá k automatizaci úloh v SQL Server a je také součástí spravovaných instancí Azure SQL. Úlohy agenta SQL nejsou k dispozici v Azure SQL Database.

Elastické úlohy můžou cílit na [databáze SQL Azure](sql-database-paas-overview.md), [Azure SQL Database elastické fondy](elastic-pool-overview.md)a databáze SQL Azure v [mapách horizontálních oddílů](elastic-scale-shard-map-management.md).

V případě automatizace úloh skriptů T-SQL v SQL Server a spravované instanci SQL Azure zvažte možnost [Agent SQL](job-automation-managed-instances.md). 

V případě automatizace úloh skriptů T-SQL ve službě Azure synapse Analytics zvažte [kanály s opakovanými triggery](/azure/synapse-analytics/data-integration/concepts-data-factory-differences.md), které jsou [založené na Azure Data Factory](/azure/synapse-analytics/data-integration/concepts-data-factory-differences).

Je potřeba poznamenat rozdíly mezi agentem SQL (dostupnými v SQL Server a jako součást spravované instance SQL) a Agent elastické úlohy databáze (který může spouštět T-SQL ve službě Azure SQL Database nebo databázích v SQL Server a Azure SQL Managed instance, Azure synapse Analytics).

| |Elastické úlohy |Agent SQL |
|---------|---------|---------|
|**Scope** | Libovolný počet databází v Azure SQL Database nebo datových skladech ve stejném cloudu Azure jako Agent úlohy. Cíle můžou být na různých serverech, předplatných a/nebo oblastech. <br><br>Cílové skupiny se dají skládat z jednotlivých databází nebo datových skladů nebo všech databází na serveru, ve fondu nebo v mapě horizontálních oddílů (dynamicky výčtově v modulu runtime úloh). | Všechny jednotlivé databáze ve stejné instanci jako Agent SQL. Funkce správy více serverů SQL Server agenta umožňuje, aby instance hlavního/cíle koordinovaly provádění úloh, i když tato funkce není k dispozici ve spravované instanci SQL. |
|**Podporovaná rozhraní API a nástroje** | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Cíle elastické úlohy

**Elastické úlohy** umožňují paralelní spouštění jednoho nebo více skriptů T-SQL napříč velkým počtem databází, podle plánu nebo na vyžádání.

Můžete spustit naplánované úlohy pro libovolnou kombinaci databází: jednu nebo více jednotlivých databází, všechny databáze na serveru, všechny databáze v elastickém fondu nebo mapa horizontálních oddílů s přidanou flexibilitou pro zahrnutí nebo vyloučení konkrétní databáze. Úlohy se můžou spouštět na více serverech, ve více fondech a dokonce i pro databáze v různých předplatných. Servery a fondy se dynamicky zjišťují za běhu, takže se úlohy spouští pro všechny databáze, které existují v cílové skupině v době spuštění.

Následující obrázek ukazuje agenta úloh, který spouští úlohy napříč různými typy cílových skupin:

![Konceptuální model agenta elastických úloh](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Komponenty elastické úlohy

|Součást | Popis (další podrobnosti jsou uvedené pod tabulkou) |
|---------|---------|
|[**Agent elastických úloh**](#elastic-job-agent) | Prostředek Azure, který vytvoříte pro spouštění a správu úloh. |
|[**Databáze úloh**](#elastic-job-database) | Databáze v Azure SQL Database, kterou Agent úlohy používá k ukládání dat souvisejících s úlohami, definic úloh atd. |
|[**Cílová skupina**](#target-group) | Sada serverů, fondu, databází a map horizontálních oddílů, pro které se má úloha spustit. |
|[**Úloha**](#elastic-jobs-and-job-steps) | Úloha je jednotka práce, která se skládá z jednoho nebo více kroků úlohy. Kroky úlohy určují skript T-SQL, který se má spustit, a také další podrobnosti potřebné ke spuštění skriptu. |

#### <a name="elastic-job-agent"></a>Agent elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent elastických úloh je prostředek Azure, který vytvoříte na portálu (podporuje se také [PowerShell](elastic-jobs-powershell-create.md) a REST).

Vytvoření **agenta elastické úlohy** vyžaduje existující databázi v Azure SQL Database. Agent nakonfiguruje existující Azure SQL Database jako [*databázi úloh*](#elastic-job-database).

Agent elastických úloh je bezplatný. Databáze úloh se fakturuje se stejnou sazbou jako jakákoli databáze v Azure SQL Database.

#### <a name="elastic-job-database"></a>Databáze elastické úlohy

*Databáze úloh* slouží k definování úloh a sledování stavu a historie spouštění úloh. *Databáze úloh* se také používá k ukládání metadat agenta, protokolů, výsledků, definic úloh a také obsahuje mnoho užitečných uložených procedur a dalších databázových objektů pro vytváření, spouštění a správu úloh pomocí T-SQL.

Pro aktuální verzi Preview se k vytvoření agenta elastické úlohy vyžaduje existující databáze v Azure SQL Database (S0 nebo vyšší).

*Databáze úloh* by měla být čistá, prázdná, S0 nebo vyšší Azure SQL Database cíle služby. Doporučený cíl služby *databáze úloh* je S1 nebo vyšší, ale optimální volba závisí na potřebách výkonu vaší úlohy: počet kroků úlohy, počet cílů úlohy a četnost spouštění úloh. 

Pokud jsou operace s databází úloh pomalejší, než se čekalo, [Sledujte](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) výkon databáze a využití prostředků v databázi úloh během období pomalé míry pomocí Azure Portal nebo [Sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Pokud se využití prostředku, jako je například CPU, datový vstup/výstup nebo zápis do protokolu, blíží 100% a koreluje s periodami zpomalení, zvažte možnost přírůstkového škálování databáze na vyšší cíle služby (buď v [modelu DTU](service-tiers-dtu.md) , nebo v [modelu Vcore](service-tiers-vcore.md)), dokud nebude dostatečně vylepšen výkon databáze úloh.

##### <a name="elastic-job-database-permissions"></a>Oprávnění databáze elastické úlohy

Při vytváření agenta úloh se v *databázi úloh* vytvoří schéma, tabulky a role *jobs_reader*. Tato role se vytvoří s následujícími oprávněními a je určená k tomu, aby správcům poskytovala podrobnější řízení přístupu pro účely monitorování úloh:

|Název role |Oprávnění ke schématu jobs |Oprávnění ke schématu jobs_internal |
|---------|---------|---------|
|**jobs_reader** | SELECT | Žádné |

> [!IMPORTANT]
> Jako správce databáze zvažte před udělením přístupu k *databázi úloh* všechny bezpečnostní důsledky. Uživatel se zlými úmysly, který má oprávnění vytvořit nebo upravit úlohy, může vytvořit nebo Upravit úlohu, která používá uložené přihlašovací údaje pro připojení k databázi v rámci ovládacího prvku uživatele se zlými úmysly, což by mohlo uživateli se zlými úmysly určit heslo přihlašovacích údajů.

#### <a name="target-group"></a>Cílová skupina

*Cílová skupina* definuje sadu databází, pro které se provede určitý krok úlohy. Cílová skupina může obsahovat libovolný počet a kombinaci následujících položek:

- **Logický SQL Server** – Pokud je zadaný server, všechny databáze, které existují na serveru v době provádění úlohy, jsou součástí skupiny. Je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina určit a aktualizovat před spuštěním úlohy. Další informace o logických serverech najdete v tématu [co je server v Azure SQL Database a Azure synapse Analytics?](logical-servers.md).
- **Elastický fond** – pokud je zadaný elastický fond, součástí skupiny jsou všechny databáze, které jsou v elastickém fondu v době spuštění úlohy. Stejně jako u serveru je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina aktualizovat před spuštěním úlohy.
- **Izolovaná databáze** – zadejte jednu nebo několik samostatných databází, které mají být součástí skupiny.
- **Mapa horizontálních oddílů** – databáze mapy horizontálních oddílů

> [!TIP]
> V okamžiku spuštění úlohy *dynamický výčet* znovu vyhodnotí sadu databází v cílových skupinách, které zahrnují servery nebo fondy. Dynamický výčet zajišťuje, že se **úlohy spustí pro všechny databáze, které existují na serveru nebo ve fondu v době spuštění úlohy**. Opětovné vyhodnocení seznamu databází za běhu je zejména užitečné pro scénáře, kdy se členství ve fondu nebo na serveru často mění.

Fondy a izolované databáze je možné zahrnout do skupiny nebo je z ní vyloučit. Díky tomu můžete vytvořit cílovou skupinu s jakoukoli kombinací databází. Do cílové skupiny například můžete přidat server, ale vyloučit z ní konkrétní databáze v elastickém fondu (nebo vyloučit celý fond).

Cílová skupina může obsahovat databáze v několika předplatných a v několika oblastech. Mějte na paměti, že spouštění napříč oblastmi má větší latenci než spouštění v rámci jedné oblasti.

Následující příklady ukazují, jak se při spuštění úlohy dynamicky zjišťují různé definice cílových skupin kvůli určení, které databáze úloha spustí:

![Příklady cílových skupin](./media/job-automation-overview/targetgroup-examples1.png)

**Příklad 1** ukazuje cílovou skupinu, která se skládá ze seznamu jednotlivých databází. Když se spustí krok úlohy s použitím této cílové skupiny, akce kroku úlohy se provede ve všech těchto databázích.<br>
**Příklad 2** ukazuje cílovou skupinu, která obsahuje server jako cíl. Když se spustí krok úlohy s použitím této cílové skupiny, automaticky se zjistí server a určí se seznam databází, které se aktuálně na serveru nacházejí. Akce kroku úlohy se provede ve všech těchto databázích.<br>
**Příklad 3** ukazuje podobnou cílovou skupinu jako *příklad 2*, ale s jednou výslovně vyloučenou databází. Akce kroku úlohy se ve vyloučené databázi *neprovede*.<br>
**Příklad 4** ukazuje cílovou skupinu, která jako cíl obsahuje elastický fond. Podobně jako v *příkladu 2* se fond automaticky zjistí za běhu úlohy a určí se seznam databází ve fondu.
<br><br>

![Příklady dalších cílových skupin](./media/job-automation-overview/targetgroup-examples2.png)

**Příklad 5** a **Příklad 6** ukazují pokročilé scénáře, kdy se servery, elastické fondy a databáze dají kombinovat pomocí pravidel zahrnutí a vyloučení.<br>
**Příklad 7** ukazuje, že za běhu úlohy je možné vyhodnotit také horizontální oddíly v mapě horizontálních oddílů.

> [!NOTE]
> Samotná databáze úlohy může být cílem úlohy. V tomto scénáři se databáze úlohy zpracuje stejně jako jakákoli jiná cílová databáze. Je nutné vytvořit uživatele úlohy a udělit dostatečná oprávnění v databázi úloh a v databázi úlohy musí existovat také přihlašovací údaje v oboru databáze, stejně jako v případě jakékoli jiné cílové databáze.

#### <a name="elastic-jobs-and-job-steps"></a>Elastické úlohy a kroky úlohy

*Úloha* je pracovní jednotka, která se spouští podle plánu nebo jako jednorázová úloha. Úloha se skládá z jednoho nebo několika *kroků úlohy*.

Každý krok úlohy určuje skript T-SQL, který se má spustit, jednu nebo několik cílových skupin, pro které se má skript T-SQL spustit, a přihlašovací údaje, které agent úloh potřebuje k připojení k cílové databázi. Každý krok úlohy má přizpůsobitelný časový limit a zásady opakování a volitelně může určovat výstupní parametry.

#### <a name="job-output"></a>Výstup úlohy

Výstupy kroků úloh pro každou cílovou databázi se podrobně zaznamenávají a výstup skriptu je možné zachytávat do určené tabulky. Můžete určit databázi, do které se budou ukládat všechna data vrácená z úlohy.

#### <a name="job-history"></a>Historie úlohy

Pomocí [dotazu na Jobs.job_executions tabulky](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)zobrazte historii spuštění elastické úlohy v *databázi úloh* . Úloha vyčištění systému vyprázdní historii spouštění starší než 45 dnů. Pokud chcete odebrat historii mladší než 45 dnů, zavolejte v *databázi úloh* uloženou proceduru **sp_purge_history**.

#### <a name="job-status"></a>Stav úlohy

Pomocí [dotazu na Jobs.job_executions tabulky](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)můžete monitorovat provádění elastických úloh v *databázi úloh* . 

### <a name="agent-performance-capacity-and-limitations"></a>Výkon, kapacita a omezení agenta

Elastické úlohy při čekání na dokončení dlouhotrvajících úloh využívají minimum výpočetních prostředků.

V závislosti na velikosti cílové skupiny databází a požadované době spuštění úlohy (počet souběžných pracovních procesů) vyžaduje agent pro *databázi úloh* různé úrovně výpočetních prostředků a výkonu (čím je více cílů a úloh, tím je potřeba více výpočetních prostředků).

V současné době je limit 100 souběžných úloh.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zabránění snižování výkonu cílových databází úlohami

Pokud chcete zajistit, aby při spouštění úloh pro databáze v elastickém fondu SQL nedocházelo k přetížení prostředků, můžete pro úlohy nakonfigurovat omezení počtu databází, pro které se můžou najednou spouštět.

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a spravovat elastické úlohy](elastic-jobs-overview.md)
- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell-create.md)
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)