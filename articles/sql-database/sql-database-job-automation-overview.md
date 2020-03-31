---
title: Automatizace úloh
description: Použití automatizace úloh ke spuštění skriptů Transact-SQL (T-SQL) v sadě jedné nebo více databází Azure SQL
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 03/10/2020
ms.openlocfilehash: dcaaf3c2f793e7148e1695cdfaa68c768db5fff6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240538"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatizace úloh správy pomocí úloh databáze

Azure SQL Database umožňuje vytvářet a plánovat úlohy, které by mohly být pravidelně spouštěny proti jedné nebo více databázím ke spuštění dotazů T-SQL a provádění úloh údržby.
Každá úloha zaznamenává stav provádění a také automaticky opakuje operace, pokud dojde k selhání.
Můžete definovat cílovou databázi nebo skupiny databází Azure SQL, kde se úloha spustí, a také definovat plány pro spuštění úlohy.
Úloha zpracovává úlohu přihlášení do cílové databáze. Můžete také definovat, udržovat a uchovávat skripty Transact-SQL, které mají být provedeny ve skupině databází Azure SQL.

## <a name="when-to-use-automated-jobs"></a>Kdy použít automatické úlohy

Existuje několik scénářů, kdy můžete použít automatizaci úloh:

- Automatizujte úlohy správy a naplánujte jejich spouštění každý všední den, po pracovní době atd.
  - Nasazování změn schématu, správa přihlašovacích údajů, shromažďování dat o výkonu nebo shromažďování telemetrických dat tenantů (zákazníků).
  - Aktualizujte referenční data (informace společné ve všech databázích), načtěte data z úložiště objektů blob Azure.
  - Vylepšení výkonu dotazů díky opětovnému sestavení indexů. Konfigurace spouštění úloh pro kolekci databází nebo jejich opakovaného spouštění, například v době mimo špičku.
  - Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Shromažďování dat pro účely generování sestav
  - Agregace dat z kolekce databází Azure SQL do jedné cílové tabulky.
  - Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.
- Přesuny dat
  - Vytvořte úlohy, které replikují změny provedené v databázích do jiných databází, nebo shromažďujte aktualizace provedené ve vzdálených databázích a aplikujte změny v databázi.
  - Vytvořte úlohy, které načítají data z nebo do databází pomocí služby SQL Server Integration Services (SSIS).

## <a name="overview"></a>Přehled

V Azure SQL Database jsou k dispozici následující technologie plánování úloh:

- **Úlohy agenta SQL jsou** klasické a bitvami testované součásti plánování úloh serveru SQL Server, která je k dispozici ve spravované instanci. Úlohy agenta SQL nejsou dostupné v databázích Azure SQL single.
- **Úlohy elastické databáze (preview)** jsou služby plánování úloh, které spouštějí vlastní úlohy v jedné nebo mnoha databázích Azure SQL.

Stojí za zmínku několik rozdílů mezi SQL Agent (k dispozici místně a jako součást spravované instance SQL Database) a agenta elastic Job databáze (k dispozici pro jednotlivé databáze v databázi Azure SQL a databáze v SQL Data Warehouse).

| |Elastické úlohy |SQL Agent |
|---------|---------|---------|
|Rozsah | Libovolný počet databází Azure SQL nebo datových skladů ve stejném cloudu Azure jako agent úloh. Cíle mohou být v různých serverech databáze SQL, odběry nebo oblasti. <br><br>Cílové skupiny se můžou skládat z jednotlivých databází nebo datových skladů nebo ze všech databází na serveru, ve fondu nebo v mapě horizontálních oddílů (dynamicky se zjišťují za běhu úlohy). | Všechny jednotlivé databáze ve stejné instanci serveru SQL jako agent SQL. |
|Podporovaná rozhraní API a nástroje | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |

## <a name="sql-agent-jobs"></a>Úlohy agenta SQL

Sql Agent Jobs jsou zadanou řadou skriptů T-SQL proti databázi. Pomocí úloh definujte úlohu správy, kterou lze spustit jednou nebo vícekrát a sledovat z hlediska úspěchu nebo neúspěchu.
Úloha může být spuštěna na jednom místním serveru nebo na více vzdálených serverech. Úlohy agenta SQL jsou interní součást databázového stroje, která je spuštěna v rámci služby Spravované instance.
Existuje několik klíčových konceptů v SQL Agent úlohy:

- **Sada kroků úlohy** jednoho nebo více kroků, které by měly být provedeny v rámci úlohy. Pro každý krok úlohy můžete definovat strategii opakování a akci, která by se měla stát, pokud krok úlohy proběhne úspěšně nebo se nezdaří.
- **Plány** definují, kdy má být úloha provedena.
- **Oznámení** umožňují definovat pravidla, která budou použita k upozornění operátorů e-mailem po dokončení úlohy.

### <a name="job-steps"></a>Kroky úlohy

Kroky úlohy agenta SQL agenta jsou sekvence akcí, které by měl agent SQL provést. Každý krok má následující krok, který by měl být proveden, pokud je krok úspěšný nebo neúspěšný, počet opakování v případě selhání.
SQL Agent umožňuje vytvářet různé typy kroků úlohy, jako je například krok úlohy Transact-SQL, který spouští jednu dávku Transact-SQL proti databázi, nebo kroky příkazu/prostředí OS, které mohou spouštět vlastní skript operačního systému, kroky úlohy SSIS umožňují načíst data pomocí modulu runtime SSIS nebo kroky [replikace,](sql-database-managed-instance-transactional-replication.md) které mohou publikovat změny z databáze do jiných databází.

[Transakční replikace](sql-database-managed-instance-transactional-replication.md) je funkce databázového stroje, která umožňuje publikovat změny provedené v jedné nebo více tabulkách v jedné databázi a publikovat nebo distribuovat je do sady databází odběratelů. Publikování změn je implementováno pomocí následujících typů kroků úlohy agenta SQL:

- Čtečka protokolů transakcí.
- Snímek.
- Distributor.

Další typy kroků úlohy nejsou aktuálně podporovány, včetně:

- Krok úlohy slučovací replikace není podporován.
- Program pro čtení front není podporován.
- Služby Analysis Services nejsou podporovány.

### <a name="job-schedules"></a>Plány úlohy

Plán určuje, kdy je úloha spuštěna. Více než jedna úloha může být spuštěna podle stejného plánu a pro stejnou úlohu lze použít více než jeden plán.
Plán může definovat následující podmínky pro dobu spuštění úlohy:

- Vždy, když instance je restartován (nebo při spuštění agenta SQL Server). Úloha je aktivována po každém převzetí služeb při selhání.
- Jednou, v určitém datu a čase, což je užitečné pro zpožděné spuštění některé úlohy.
- Podle opakovaného plánu.

> [!Note]
> Spravovaná instance aktuálně neumožňuje spustit úlohu, když je instance "nečinná".

### <a name="job-notifications"></a>Oznámení o úloze

Úlohy agenta SQL umožňují získat oznámení po úspěšném dokončení úlohy nebo selhání. Oznámení můžete dostávat e-mailem.

Nejprve budete muset nastavit e-mailový účet, který bude použit k odeslání e-mailových oznámení a přiřadit účet k e-mailovému profilu s názvem `AzureManagedInstance_dbmail_profile`, jak je znázorněno v následující ukázce:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)'

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.' ;

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Budete také muset povolit databázi pošty na spravované instanci:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Můžete upozornit operátora, že se něco stalo s úlohami SQL Agent. Operátor definuje kontaktní informace pro osobu odpovědnou za údržbu jedné nebo více spravovaných instancí. V některých případě jsou odpovědnosti operátora přiřazeny jedné osobě.
V systémech s více spravovanými instancemi nebo servery SQL může mnoho jednotlivců sdílet odpovědnosti operátora. Operátor neobsahuje informace o zabezpečení a nedefinuje zaregistrovaný objekt zabezpečení.

Operátory můžete vytvořit pomocí SSMS nebo skriptu Transact-SQL zobrazeného v následujícím příkladu:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'Mihajlo Pupun',
    @enabled=1,
    @email_address=N'mihajlo.pupin@contoso.com'
```

Můžete upravit libovolnou úlohu a přiřadit operátory, které budou upozorněny e-mailem, pokud úloha dokončí, selže nebo uspěje pomocí SSMS nebo následujícího skriptu Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Omezení úloh y agenta SQL

Některé funkce agenta SQL, které jsou k dispozici na serveru SQL Server, nejsou ve spravované instanci podporovány:

- Nastavení agenta SQL jsou pouze pro čtení. Procedura `sp_set_agent_properties` není ve spravované instanci podporována.
- Povolení nebo zakázání agenta SQL není aktuálně podporováno ve spravované instanci. Agent SQL je vždy spuštěn.
- Oznámení jsou částečně podporována.
  - Pager není podporován.
  - Program NetSend není podporován.
  - Výstrahy nejsou podporovány.
- Proxy servery nejsou podporovány.
- Protokol událostí není podporován.

Informace o agentovi serveru SQL Server naleznete v tématu [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

## <a name="elastic-database-jobs-preview"></a>Úlohy elastické databáze (náhled)

**Úlohy elastické databáze** poskytují možnost paralelně spouštět jeden nebo několik skriptů T-SQL nad velkým počtem databází, a to podle plánu nebo na vyžádání.

**Spouštění úloh pro jakoukoli kombinací databází:** jedna nebo několik samostatných databází, všechny databáze na serveru, všechny databáze v elastickém fondu nebo mapa horizontálních oddílů s dodatečnou možností zahrnout nebo vyloučit jakoukoli konkrétní databázi. **Úlohy se můžou spouštět na více serverech, ve více fondech a dokonce i pro databáze v různých předplatných.** Servery a fondy se dynamicky zjišťují za běhu, takže se úlohy spouští pro všechny databáze, které existují v cílové skupině v době spuštění.

Následující obrázek ukazuje agenta úloh, který spouští úlohy napříč různými typy cílových skupin:

![Konceptuální model agenta elastických úloh](media/elastic-jobs-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Komponenty elastických úloh

|Komponenta | Popis (další podrobnosti jsou uvedené pod tabulkou) |
|---------|---------|
|[**Agent elastických úloh**](#elastic-job-agent) | Prostředek Azure, který vytvoříte pro spouštění a správu úloh. |
|[**Databáze úloh**](#job-database) | Databáze Azure SQL, do které agent úloh ukládá data související s úlohami, definice úloh atd. |
|[**Cílová skupina**](#target-group) | Sada serverů, fondu, databází a map horizontálních oddílů, pro které se má úloha spustit. |
|[**Úloha**](#job) | Úloha je jednotka práce, která se skládá z jednoho nebo více [kroků úlohy](#job-step). Kroky úlohy určují skript T-SQL, který se má spustit, a také další podrobnosti potřebné ke spuštění skriptu. |

#### <a name="elastic-job-agent"></a>Agent elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent elastických úloh je prostředek Azure, který vytvoříte na portálu (podporuje se také [PowerShell](elastic-jobs-powershell.md) a REST).

K vytvoření **agenta elastických úloh** je potřeba existující databáze SQL. Agent nakonfiguruje tuto existující databázi jako [*databázi úloh*](#job-database).

Agent elastických úloh je bezplatný. Databáze úloh se účtuje stejnou rychlostí jako jakákoli databáze SQL.

#### <a name="job-database"></a>Databáze úloh

*Databáze úloh* slouží k definování úloh a sledování stavu a historie spouštění úloh. *Databáze úloh* se také používá k ukládání metadat agenta, protokolů, výsledků, definic úloh a také obsahuje mnoho užitečných uložených procedur a dalších databázových objektů pro vytváření, spouštění a správu úloh pomocí T-SQL.

V aktuální verzi Preview je k vytvoření agenta elastických úloh potřeba existující databáze Azure SQL (S0 nebo vyšší).

*Databáze úloh* nemusí být doslova nová, ale měla by být čistým, prázdným, S0 nebo vyšším cílem služby. Doporučený cíl služby *databáze úloh* je S1 nebo vyšší, ale optimální volba závisí na potřebách výkonu úlohy: počet kroků úlohy, počet cílů úlohy a jak často jsou úlohy spouštěny. Například databáze S0 může být dostatečná pro agenta úlohy, který spouští několik úloh za hodinu cílení méně než deset databází, ale spuštění úlohy každou minutu nemusí být dostatečně rychlé s databází S0 a vyšší úroveň služeb může být lepší.

Pokud jsou operace proti databázi úloh pomalejší, než bylo očekáváno, [sledujte](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring) výkon databáze a využití prostředků v databázi úloh během období pomalostpomocí portálu Azure portal nebo [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Pokud využití prostředku, jako je například procesor, vod voj nebo zápis protokolu přístupy 100 % a koreluje s období pomalost, zvažte postupně škálování databáze na vyšší cíle služby (buď v [modelu DTU](sql-database-service-tiers-dtu.md) nebo v [modelu virtuálních jader](sql-database-service-tiers-vcore.md)), dokud není dostatečně lepší výkon databáze úloh.

##### <a name="job-database-permissions"></a>Oprávnění k databázi úloh

Při vytváření agenta úloh se v *databázi úloh* vytvoří schéma, tabulky a role *jobs_reader*. Tato role se vytvoří s následujícími oprávněními a je určená k tomu, aby správcům poskytovala podrobnější řízení přístupu pro účely monitorování úloh:

|Název role |Oprávnění ke schématu jobs |Oprávnění ke schématu jobs_internal |
|---------|---------|---------|
|**jobs_reader** | SELECT | Žádný |

> [!IMPORTANT]
> Jako správce databáze zvažte před udělením přístupu k *databázi úloh* všechny bezpečnostní důsledky. Uživatel se zlými úmysly s oprávněními k vytváření nebo úpravě úloh by mohl vytvořit nebo upravit úlohu, která se pomocí uložených přihlašovacích údajů připojí k databázi pod jeho kontrolou a uživatel se zlými úmysly by tak mohl zjistit přihlašovací heslo.

#### <a name="target-group"></a>Cílová skupina

*Cílová skupina* definuje sadu databází, pro které se provede určitý krok úlohy. Cílová skupina může obsahovat libovolný počet a kombinaci následujících položek:

- **SQL Database server** - pokud je zadán server, všechny databáze, které existují na serveru v době spuštění úlohy jsou součástí skupiny. Je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina určit a aktualizovat před spuštěním úlohy.
- **Elastický fond** – pokud je zadaný elastický fond, součástí skupiny jsou všechny databáze, které jsou v elastickém fondu v době spuštění úlohy. Stejně jako u serveru je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina aktualizovat před spuštěním úlohy.
- **Izolovaná databáze** – zadejte jednu nebo několik samostatných databází, které mají být součástí skupiny.
- **Mapa horizontálních oddílů** – databáze mapy horizontálních oddílů.

> [!TIP]
> V okamžiku spuštění úlohy *dynamický výčet* znovu vyhodnotí sadu databází v cílových skupinách, které zahrnují servery nebo fondy. Dynamický výčet zajišťuje, že se **úlohy spustí pro všechny databáze, které existují na serveru nebo ve fondu v době spuštění úlohy**. Opětovné vyhodnocení seznamu databází za běhu je zejména užitečné pro scénáře, kdy se členství ve fondu nebo na serveru často mění.

Fondy a izolované databáze je možné zahrnout do skupiny nebo je z ní vyloučit. Díky tomu můžete vytvořit cílovou skupinu s jakoukoli kombinací databází. Do cílové skupiny například můžete přidat server, ale vyloučit z ní konkrétní databáze v elastickém fondu (nebo vyloučit celý fond).

Cílová skupina může obsahovat databáze v několika předplatných a v několika oblastech. Mějte na paměti, že spouštění napříč oblastmi má větší latenci než spouštění v rámci jedné oblasti.

Následující příklady ukazují, jak se při spuštění úlohy dynamicky zjišťují různé definice cílových skupin kvůli určení, které databáze úloha spustí:

![Příklady cílových skupin](media/elastic-jobs-overview/targetgroup-examples1.png)

**Příklad 1** ukazuje cílovou skupinu, která se skládá ze seznamu jednotlivých databází. Když se spustí krok úlohy s použitím této cílové skupiny, akce kroku úlohy se provede ve všech těchto databázích.<br>
**Příklad 2** ukazuje cílovou skupinu, která jako cíl obsahuje Azure SQL Server. Když se spustí krok úlohy s použitím této cílové skupiny, automaticky se zjistí server a určí se seznam databází, které se aktuálně na serveru nacházejí. Akce kroku úlohy se provede ve všech těchto databázích.<br>
**Příklad 3** ukazuje podobnou cílovou skupinu jako *příklad 2*, ale s jednou výslovně vyloučenou databází. Akce kroku úlohy se ve vyloučené databázi *neprovede*.<br>
**Příklad 4** ukazuje cílovou skupinu, která jako cíl obsahuje elastický fond. Podobně jako v *příkladu 2* se fond automaticky zjistí za běhu úlohy a určí se seznam databází ve fondu.
<br><br>

![Příklady cílových skupin](media/elastic-jobs-overview/targetgroup-examples2.png)

**Příklad y 5** a **Příklad 6** ukazují pokročilé scénáře, kde lze kombinovat servery Azure SQL, elastické fondy a databáze pomocí pravidel zahrnutí a vyloučení.<br>
**Příklad 7** ukazuje, že za běhu úlohy je možné vyhodnotit také horizontální oddíly v mapě horizontálních oddílů.

> [!NOTE]
> Cílem úlohy může být samotná databáze úlohy. V tomto scénáři je databáze úloh zpracována stejně jako všechny ostatní cílové databáze. Uživatel úlohy musí být vytvořen a musí mu být udělena dostatečná oprávnění v databázi úloh a pověření s rozsahem databáze pro uživatele úlohy musí existovat také v databázi úloh, stejně jako pro jakoukoli jinou cílovou databázi.
>

#### <a name="job"></a>Úloha

*Úloha* je pracovní jednotka, která se spouští podle plánu nebo jako jednorázová úloha. Úloha se skládá z jednoho nebo několika *kroků úlohy*.

##### <a name="job-step"></a>Krok úlohy

Každý krok úlohy určuje skript T-SQL, který se má spustit, jednu nebo několik cílových skupin, pro které se má skript T-SQL spustit, a přihlašovací údaje, které agent úloh potřebuje k připojení k cílové databázi. Každý krok úlohy má přizpůsobitelný časový limit a zásady opakování a volitelně může určovat výstupní parametry.

#### <a name="job-output"></a>Výstup úlohy

Výstupy kroků úloh pro každou cílovou databázi se podrobně zaznamenávají a výstup skriptu je možné zachytávat do určené tabulky. Můžete určit databázi, do které se budou ukládat všechna data vrácená z úlohy.

#### <a name="job-history"></a>Historie úlohy

Historie spouštění úloh se ukládá do *databáze úloh*. Úloha vyčištění systému vyprázdní historii spouštění starší než 45 dnů. Pokud chcete odebrat historii mladší než 45 dnů, zavolejte v *databázi úloh* uloženou proceduru **sp_purge_history**.

### <a name="agent-performance-capacity-and-limitations"></a>Výkon, kapacita a omezení agenta

Elastické úlohy při čekání na dokončení dlouhotrvajících úloh využívají minimum výpočetních prostředků.

V závislosti na velikosti cílové skupiny databází a požadované době spuštění úlohy (počet souběžných pracovních procesů) vyžaduje agent pro *databázi úloh* různé úrovně výpočetních prostředků a výkonu (čím je více cílů a úloh, tím je potřeba více výpočetních prostředků).

V současné době je verze Preview omezená na 100 souběžných úloh.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zabránění snižování výkonu cílových databází úlohami

Pokud chcete zajistit, aby při spouštění úloh pro databáze v elastickém fondu SQL nedocházelo k přetížení prostředků, můžete pro úlohy nakonfigurovat omezení počtu databází, pro které se můžou najednou spouštět.

## <a name="next-steps"></a>Další kroky

- [Co je agent serveru SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)
- [Jak vytvářet a spravovat elastické úlohy](elastic-jobs-overview.md)
- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
