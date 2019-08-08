---
title: Automatizace úloh Azure SQL | Microsoft Docs
description: Použití automatizace úloh ke spouštění skriptů jazyka Transact-SQL (T-SQL) v rámci jedné nebo více databází SQL Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/25/2019
ms.openlocfilehash: 1b8d4965edb446235e28f47a0226c82b89c73e0b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845056"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatizace úloh správy pomocí databázových úloh

Azure SQL Database vám umožní vytvářet a plánovat úlohy, které se můžou pravidelně provádět v jedné nebo mnoha databázích, aby se daly spouštět dotazy T-SQL a provádět úlohy údržby. Každá úloha zaznamená stav spuštění a také automaticky opakuje operace, pokud dojde k nějaké chybě.
Můžete definovat cílovou databázi nebo skupiny databází Azure SQL, kde se úloha spustí, a také definovat plány pro spuštění úlohy.
Úloha zpracovává úlohu přihlášení do cílové databáze. Můžete také definovat, udržovat a zachovat skripty jazyka Transact-SQL, které se mají spouštět napříč skupinou databází SQL Azure.

## <a name="when-to-use-automated-jobs"></a>Kdy použít automatizované úlohy

K dispozici je několik scénářů, kdy můžete použít automatizaci úloh:

- Automatizujte úlohy správy a naplánujte, aby se spouštěly každý den v týdnu, a to po hodinách atd.
  - Nasazování změn schématu, správa přihlašovacích údajů, shromažďování dat o výkonu nebo shromažďování telemetrických dat tenantů (zákazníků).
  - Aktualizujte referenční data (společné informace napříč všemi databázemi), načtěte data z úložiště objektů BLOB v Azure.
  - Vylepšení výkonu dotazů díky opětovnému sestavení indexů. Konfigurace spouštění úloh pro kolekci databází nebo jejich opakovaného spouštění, například v době mimo špičku.
  - Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Shromažďování dat pro účely generování sestav
  - Agregace dat z kolekce databází Azure SQL do jedné cílové tabulky.
  - Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.
- Přesuny dat
  - Vytvářejte úlohy, které replikují změny provedené ve vašich databázích do jiných databází nebo shromažďují aktualizace provedené ve vzdálených databázích a aplikují změny v databázi.
  - Vytvářejte úlohy, které načítají data z databáze nebo do databází pomocí služba SSIS (SQL Server Integration Services) (SSIS).

## <a name="overview"></a>Přehled

V Azure SQL Database jsou k dispozici následující technologie plánování úloh:

- **Úlohy agenta SQL** jsou klasické a s prošlou dostupností SQL Server komponenty plánování úloh, která je k dispozici ve spravované instanci. Úlohy agenta SQL nejsou k dispozici v izolovaných databázích.
- **Úlohy elastic Database** jsou služby plánování úloh, které spouštějí vlastní úlohy v jedné nebo mnoha databázích SQL Azure.

Je potřeba si vymezit několik rozdílů mezi agentem SQL (dostupnými místně a jako součást SQL Database spravované instance) a Agent elastické úlohy databáze (k dispozici pro izolované databáze ve službě Azure SQL Database a databáze v SQL Data Warehouse).

|  |Elastické úlohy  |Agent SQL |
|---------|---------|---------|
|Scope     |  Libovolný počet databází Azure SQL nebo datových skladů ve stejném cloudu Azure jako agent úloh. Cíle můžou být v různých SQL Databasech serverech, předplatných a/nebo oblastech. <br><br>Cílové skupiny se můžou skládat z jednotlivých databází nebo datových skladů nebo ze všech databází na serveru, ve fondu nebo v mapě horizontálních oddílů (dynamicky se zjišťují za běhu úlohy). | Všechny jednotlivé databáze ve stejné instanci SQL Server jako Agent SQL. |
|Podporovaná rozhraní API a nástroje     |  Portál, PowerShell, T-SQL, Azure Resource Manager      |   T-SQL, SQL Server Management Studio (SSMS)     |

## <a name="sql-agent-jobs"></a>SQL Agent Jobs

Úlohy agenta SQL jsou zadané řady skriptů T-SQL pro vaši databázi. Použijte úlohy k definování úlohy správy, kterou je možné spustit jednou nebo vícekrát a monitorovat pro úspěch nebo neúspěch.
Úlohu můžete spustit na jednom místním serveru nebo na několika vzdálených serverech. Úlohy agenta SQL jsou interní součástí databázového stroje, který je spuštěn v rámci služby Managed instance.
V úlohách agenta SQL je několik klíčových konceptů:

- **Kroky úlohy** v jednom nebo několika krocích, které by měly být provedeny v rámci úlohy. Pro každý krok úlohy můžete definovat strategii opakování a akci, která se má provést, když se krok úlohy zdaří nebo selže.
- **Plány** definují, kdy se má úloha spustit.
- **Oznámení** umožňují definovat pravidla, která budou sloužit k oznamování operátorů prostřednictvím e-mailu po dokončení úlohy.

### <a name="job-steps"></a>Kroky úlohy

Kroky úlohy agenta SQL jsou sekvence akcí, které by měl Agent SQL spustit. Každý krok má následující krok, který by měl být proveden, pokud je krok úspěšný nebo neúspěšný, počet opakovaných pokusů v případě selhání.
SQL Agent vám umožňuje vytvořit různé typy kroků úlohy, jako je například krok úlohy Transact-SQL, který spouští jednu dávku Transact-SQL v databázi, nebo příkazy příkazového řádku nebo PowerShellový postup, který může spustit vlastní skript operačního systému, a SSIS úlohy umožňují načíst data pomocí SSIS modul runtime nebo kroky [replikace](sql-database-managed-instance-transactional-replication.md) , které mohou publikovat změny z databáze do jiných databází.

[Transakční replikace](sql-database-managed-instance-transactional-replication.md) je funkce databázového stroje, která umožňuje publikovat změny provedené v jedné nebo několika tabulkách v jedné databázi a publikovat je nebo distribuovat do sady databází předplatitelů. Publikování změn je implementováno pomocí následujících typů kroků úlohy agenta SQL:

- Čtečka protokolu transakcí.
- Snímek.
- Rozdělovač.

Jiné typy kroků úlohy se aktuálně nepodporují, včetně:

- Krok úlohy sloučení replikace se nepodporuje.
- Čtečka fronty není podporována.
- Analysis Services se nepodporují.

### <a name="job-schedules"></a>Plány úlohy

Plán určuje, kdy úloha běží. Ve stejném plánu může běžet víc než jedna úloha a u stejné úlohy se může použít víc než jeden plán.
Plán může definovat následující podmínky pro čas spuštění úlohy:

- Pokaždé, když je instance restartována (nebo když se spustí Agent SQL Server). Úloha se aktivuje po každém převzetí služeb při selhání.
- Jednou, v konkrétní datum a čas, který je vhodný pro opožděné provádění některých úloh.
- Podle plánu opakování.

> [!Note]
> Spravovaná instance v současné době neumožňuje spustit úlohu, když je instance nečinný.

### <a name="job-notifications"></a>Oznámení úloh

Úlohy agenta SQL vám umožňují dostávat oznámení, když se úloha úspěšně dokončí nebo dojde k chybě. Oznámení můžete přijímat prostřednictvím e-mailu.

Nejdřív je potřeba nastavit e-mailový účet, který se použije k odeslání e-mailových oznámení a přiřadit účet k e-mailovému profilu s `AzureManagedInstance_dbmail_profile`názvem, jak je znázorněno v následující ukázce:

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

Budete taky muset povolit Databázová pošta na spravované instanci:

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

Můžete upozornit operátora, že se něco stalo s vašimi úlohami agenta SQL. Operátor definuje kontaktní informace pro jednotlivce zodpovědného za údržbu jedné nebo více spravovaných instancí. V některých případech se odpovědnosti operátorů přiřazují jednomu jednotlivci.
V systémech, které mají více spravovaných instancí nebo serverů SQL, mnoho jednotlivců může sdílet zodpovědnost za operátora. Operátor neobsahuje informace o zabezpečení a nedefinuje objekt zabezpečení.

Můžete vytvořit operátory pomocí SSMS nebo skriptu Transact-SQL, který je znázorněn v následujícím příkladu:

```sql
EXEC msdb.dbo.sp_add_operator 
    @name=N'Mihajlo Pupun', 
        @enabled=1, 
        @email_address=N'mihajlo.pupin@contoso.com'
```

Můžete upravit libovolnou úlohu a přiřadit operátory, které budou upozorňovány prostřednictvím e-mailu, pokud se úloha dokončí, selže nebo bude úspěšná pomocí SSMS nebo následujícího skriptu Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS', 
        @notify_level_email=3,                        -- Options are: 1 on succeed, 2 on failure, 3 on complete
        @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Omezení úloh agenta SQL

Některé funkce agenta SQL, které jsou k dispozici v SQL Server, nejsou ve spravované instanci podporovány:
- Nastavení agenta SQL jsou jen pro čtení. Procedura `sp_set_agent_properties` není ve spravované instanci podporována.
- Povolení nebo zakázání agenta SQL se v tuto chvíli nepodporuje ve spravované instanci. Agent SQL je vždycky spuštěný.
- Oznámení jsou částečně podporovaná.
  - Pager není podporován.
  - NetSend se nepodporuje.
  - Výstrahy nejsou podporovány.
- Proxy servery nejsou podporovány.
- Protokol událostí není podporován.

Informace o agentovi SQL Server najdete v tématu [agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

## <a name="elastic-database-jobs"></a>Úlohy elastické databáze

**Úlohy elastické databáze** poskytují možnost paralelně spouštět jeden nebo několik skriptů T-SQL nad velkým počtem databází, a to podle plánu nebo na vyžádání.

**Spouštění úloh pro jakoukoli kombinací databází:** jedna nebo několik samostatných databází, všechny databáze na serveru, všechny databáze v elastickém fondu nebo mapa horizontálních oddílů s dodatečnou možností zahrnout nebo vyloučit jakoukoli konkrétní databázi. **Úlohy se můžou spouštět na více serverech, ve více fondech a dokonce i pro databáze v různých předplatných.** Servery a fondy se dynamicky zjišťují za běhu, takže se úlohy spouští pro všechny databáze, které existují v cílové skupině v době spuštění.

Následující obrázek ukazuje agenta úloh, který spouští úlohy napříč různými typy cílových skupin:

![Konceptuální model agenta elastických úloh](media/elastic-jobs-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Komponenty elastických úloh

|Komponenta  | Popis (další podrobnosti jsou uvedené pod tabulkou) |
|---------|---------|
|[**Agent elastických úloh**](#elastic-job-agent) |  Prostředek Azure, který vytvoříte pro spouštění a správu úloh.   |
|[**Databáze úloh**](#job-database)    |    Databáze Azure SQL, do které agent úloh ukládá data související s úlohami, definice úloh atd.      |
|[**Cílová skupina**](#target-group)      |  Sada serverů, fondu, databází a map horizontálních oddílů, pro které se má úloha spustit.       |
|[**Úloha**](#job)  |  Úloha je jednotka práce, která se skládá z jednoho nebo více [kroků úlohy](#job-step). Kroky úlohy určují skript T-SQL, který se má spustit, a také další podrobnosti potřebné ke spuštění skriptu.  |


#### <a name="elastic-job-agent"></a>Agent elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent elastických úloh je prostředek Azure, který vytvoříte na portálu (podporuje se také [PowerShell](elastic-jobs-powershell.md) a REST). 

K vytvoření **agenta elastických úloh** je potřeba existující databáze SQL. Agent nakonfiguruje tuto existující databázi jako [*databázi úloh*](#job-database).

Agent elastických úloh je bezplatný. Databáze úloh se účtuje stejnou sazbou jako jakákoli databáze SQL.

#### <a name="job-database"></a>Databáze úloh

*Databáze úloh* slouží k definování úloh a sledování stavu a historie spouštění úloh. *Databáze úloh* se také používá k ukládání metadat agenta, protokolů, výsledků, definic úloh a také obsahuje mnoho užitečných uložených procedur a dalších databázových objektů pro vytváření, spouštění a správu úloh pomocí T-SQL.

V aktuální verzi Preview je k vytvoření agenta elastických úloh potřeba existující databáze Azure SQL (S0 nebo vyšší).

*Databáze úloh* nemusí být zcela nová, ale měla by být čistá, prázdná a s úrovní služby S0 nebo vyšší. Doporučená úroveň služby pro *databázi úloh* je S1 nebo vyšší, ale opravdu záleží na požadavcích vašich úloh na výkon: na počtu kroků úloh a na počtu a frekvenci spouštění úloh. Databáze S0 například může být dostatečná pro agenta úloh, který spouští několik úloh za hodinu. Při spouštění úlohy každou minutu by však výkon nemusel být dostačující a byla by lepší vyšší úroveň služby.


##### <a name="job-database-permissions"></a>Oprávnění k databázi úloh

Při vytváření agenta úloh se v *databázi úloh* vytvoří schéma, tabulky a role *jobs_reader*. Tato role se vytvoří s následujícími oprávněními a je určená k tomu, aby správcům poskytovala podrobnější řízení přístupu pro účely monitorování úloh:


|Název role  |Oprávnění ke schématu jobs  |Oprávnění ke schématu jobs_internal  |
|---------|---------|---------|
|**jobs_reader**     |    SELECT     |    Žádný     |

> [!IMPORTANT]
> Jako správce databáze zvažte před udělením přístupu k *databázi úloh* všechny bezpečnostní důsledky. Uživatel se zlými úmysly s oprávněními k vytváření nebo úpravě úloh by mohl vytvořit nebo upravit úlohu, která se pomocí uložených přihlašovacích údajů připojí k databázi pod jeho kontrolou a uživatel se zlými úmysly by tak mohl zjistit přihlašovací heslo.



#### <a name="target-group"></a>Cílová skupina

*Cílová skupina* definuje sadu databází, pro které se provede určitý krok úlohy. Cílová skupina může obsahovat libovolný počet a kombinaci následujících položek:

- **SQL Database Server** – Pokud je zadaný server, všechny databáze, které existují na serveru v době provádění úlohy, jsou součástí skupiny. Je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina určit a aktualizovat před spuštěním úlohy.
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

**Příklad 5** a **Příklad 6** : zobrazení rozšířených scénářů, ve kterých se Azure SQL Server, elastické fondy a databáze dají kombinovat pomocí pravidel zahrnutí a vyloučení.<br>
**Příklad 7** ukazuje, že za běhu úlohy je možné vyhodnotit také horizontální oddíly v mapě horizontálních oddílů.

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

## <a name="next-steps"></a>Další postup

- [Co je Agent SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) 
- [Jak vytvářet a spravovat elastické úlohy](elastic-jobs-overview.md) 
- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md) 
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md) 
