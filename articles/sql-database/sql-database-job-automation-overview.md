---
title: Azure automation úlohy SQL | Dokumentace Microsoftu
description: Použití úlohy automatizace ke spouštění skriptů Transact-SQL (T-SQL) mezi sadu jednu nebo více databází Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: database-features
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/22/2019
ms.openlocfilehash: 63a6daa7c409aeb77b07e98cc0108b727f263d4c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453261"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatizace úloh správy pomocí úlohy databáze

Azure SQL Database vám umožňuje vytvářet a plánovat úlohy, které může pravidelně provést jeden nebo více databází, které ke spuštění dotazů T-SQL a provádění úloh údržby. Každá úloha zaznamená stav spuštění a také automaticky opakuje operace, pokud dojde k žádné chybě.
Můžete definovat cílovou databázi nebo skupiny databází Azure SQL, ve kterém se spustí úlohy a také nastavit plány, které ke spuštění úlohy.
Úloha zpracuje úloh přihlášení do cílové databáze. Můžete také definujte, udržovat a zachovat skriptů Transact-SQL, který se spustí v rámci skupiny databází Azure SQL.

Existuje několik scénářů, když můžete použít automatizaci úloh:
- Automatizace úloh správy a pak naplánovat spuštění každý den v týdnu po hodiny atd.
  - Nasazování změn schématu, správa přihlašovacích údajů, shromažďování dat o výkonu nebo shromažďování telemetrických dat tenantů (zákazníků).
  - Aktualizace referenčních dat (informace o běžných napříč všemi databázemi), načítání dat z úložiště objektů Blob v Azure.
  - Vylepšení výkonu dotazů díky opětovnému sestavení indexů. Konfigurace spouštění úloh pro kolekci databází nebo jejich opakovaného spouštění, například v době mimo špičku.
  - Průběžné shromažďování výsledků dotazů ze sady databází do centrální tabulky. Výkonové dotazy je možné spouštět průběžně a nakonfigurovat tak, aby aktivovaly další úlohy, které se mají provést.
- Shromažďování dat pro účely generování sestav
  - Agregace dat z kolekce databází Azure SQL do jedné cílové tabulky.
  - Spouštění dlouhotrvajících dotazů na zpracování dat pro velkou sadu databází, například shromažďování telemetrických dat uživatelů. Výsledky se pro účely další analýzy shromažďují do jedné cílové tabulky.
- Přesuny dat
 - Vytvoření úlohy, které replikovat změny provedené ve vašich databázích jiných databází nebo shromažďovat aktualizace provedené v vzdálené databáze a použít změny v databázi.
 - Vytvoření úlohy, které načítají data z nebo do vaší databáze pomocí SQL Server Integration Services (SSIS).

Následující technologie plánování úlohy jsou k dispozici ve službě Azure SQL Database:
- **Úlohy agenta serveru SQL** jsou plánování classic a testovány v těžkém provozu úloh SQL serveru součásti, která je k dispozici ve spravované instanci. Úlohy agenta serveru SQL nejsou k dispozici v databáze typu Singleton.
- **Úlohy elastic Database** jsou plánování úloh služby, které provádí vlastní úlohy na jeden nebo více databází Azure SQL Database.

Stojí za povšimnutí několik rozdílů mezi Agent serveru SQL (k dispozici místně a v rámci SQL Database Managed Instance) a agenta Elastických úloh databáze (k dispozici pro jednotlivý prvek SQL Database a SQL Data Warehouse).

|  |Elastické úlohy  |Agent serveru SQL |
|---------|---------|---------|
|Rozsah     |  Libovolný počet databází Azure SQL nebo datových skladů ve stejném cloudu Azure jako agent úloh. Cíle můžou být na různých logických serverech a v různých oblastech nebo umístěních. <br><br>Cílové skupiny se můžou skládat z jednotlivých databází nebo datových skladů nebo ze všech databází na serveru, ve fondu nebo v mapě horizontálních oddílů (dynamicky se zjišťují za běhu úlohy). | Libovolná izolovaná databáze ve stejné instanci SQL Serveru jako agent SQL. |
|Podporovaná rozhraní API a nástroje     |  Portál, PowerShell, T-SQL, Azure Resource Manager      |   T-SQL, SQL Server Management Studio (SSMS)     |

## <a name="sql-agent-jobs"></a>SQL Agent Jobs

Úlohy agenta serveru SQL se zadaným řadu skriptů T-SQL na vaší databázi. Použití úloh k definování úlohou správy, můžete spouštět jednou nebo vícekrát a monitorovat úspěch nebo neúspěch.
Úlohu můžete spustit na místním serveru jeden nebo víc vzdálených serverů. Úloha agenta SQL je vnitřní komponenta databázový stroj, který se spouští v rámci Managed Instance služby.
Existuje několik klíčových konceptů v úlohy agenta serveru SQL:
- **Kroky úlohy** sadu jeden nebo více kroků, které budou spuštěny v rámci úlohy. Pro každý krok úlohy můžete definovat strategii opakování a akce, která se stane při splnění krok úlohy úspěšné nebo neúspěšné.
- **Plány** definovat provedení úlohy.
- **Oznámení** vám umožňují definovat pravidla, která se použije k oznámení operátory prostřednictvím e-mailů po dokončení úlohy.

### <a name="job-steps"></a>Kroky úlohy

Úloha agenta SQL kroky jsou pořadí akcí, které by se měl spustit agenta systému SQL. Každý krok obsahuje následující krok, který má být provedena, pokud v kroku úspěšné nebo neúspěšné, počet opakovaných pokusů v případě selhání.
Agent SQL Server vám umožní vytvářet různé typy kroky úlohy, jako je například krok úlohy příkazů jazyka Transact-SQL, který se spustí v jedné dávce Transact-SQL na databázi nebo kroky příkaz/PowerShell operačního systému, které můžete spustit vlastní skript operačního systému, kroky úlohy služby SSIS umožňují načtení dat pomocí modulu runtime služby SSIS, nebo [replikace](sql-database-managed-instance-transactional-replication.md) kroky, které můžete publikovat změny z databáze do jiné databáze.

[Transakční replikace](sql-database-managed-instance-transactional-replication.md) je funkce databázového stroje, který vám umožní publikovat změny provedené na jeden nebo více tabulek v jedné databázi a publikovat/distribuovat na sadu předplatitelskými databázemi. Publikování změn je implementováno pomocí následující typy krok úlohy agenta SQL:
- Čtečky protokolů transakcí.
- Pořízení snímku.
- Distributor.

Jiné druhy kroky úlohy se aktuálně nepodporují, včetně:
- Krok úlohy slučovací replikace nepodporuje.
- Čtečky fronty se nepodporuje.
- Analysis Services nejsou podporovány.

### <a name="job-schedules"></a>Plány úlohy

Plán Určuje, kdy se spouští úloha. Více než jednu úlohu můžete spustit ve stejném plánu a více než jeden plán můžou použít stejné úloze.
Plán můžete definovat dobu, kdy se spouští úloha následující podmínky:
- Při každém restartování Instance (nebo při spuštění agenta systému SQL Server). Úloha se aktivuje po každé převzetí služeb při selhání.
- Jednou, v určité datum a čas, což je užitečné pro provádění zpožděných některé úlohy.
- Podle opakovaného plánu.

> [!Note]
> Managed Instance aktuálně není umožňuje spuštění úlohy, jakmile bude instance "nečinnosti".

### <a name="job-notifications"></a>Oznámení úlohy

Úlohy agenta SQL serveru vám umožní dostávat oznámení, když se úloha úspěšně dokončí nebo se nezdařilo. Můžete dostávat e-mailové oznámení e-mailem.

Nejprve je třeba nastavit e-mailový účet, který se použije k odesílání e-mailová oznámení a přiřadit účet e-mailový profil, který volá `AzureManagedInstance_dbmail_profile`, jak je znázorněno v následujícím příkladu:

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

Bude také potřeba povolit na spravované instanci databázového e-mailu:

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

Operátor může upozornit, že se něco stalo s úlohy agenta serveru SQL. Operátor definuje kontaktní informace pro osoba zodpovědná za údržbu jeden nebo více spravovaných instancí. Odpovědnosti operátora nějakou dobu, jsou přiřazeny k jedna osoba.
V systémech s více mi nebo SQL serverů můžete sdílet mnoho jednotlivce odpovědnosti operátora. Operátor neobsahuje informace o zabezpečení a nedefinuje objektu zabezpečení.

Můžete vytvořit operátory pomocí aplikace SSMS nebo příkazů jazyka Transact-SQL skriptu je znázorněno v následujícím příkladu:

```sql
EXEC msdb.dbo.sp_add_operator 
    @name=N'Mihajlo Pupun', 
        @enabled=1, 
        @email_address=N'mihajlo.pupin@contoso.com'
```

Můžete upravit libovolnou úlohu a přiřadit operátor, který budete upozorněni prostřednictvím e-mailu, pokud se úloha dokončí, selže nebo úspěšné pomocí aplikace SSMS nebo následující skript jazyka Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS', 
        @notify_level_email=3,                        -- Options are: 1 on succeed, 2 on failure, 3 on complete
        @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Omezení úlohu agenta SQL

Některé funkce agenta SQL, které jsou k dispozici v systému SQL Server nejsou podporovány ve spravované instanci:
- Nastavení agenta SQL jsou jen pro čtení. Postup `sp_set_agent_properties` není ve spravované instanci podporováno.
- Povolit/zakázat agenta není aktuálně podporován ve spravované instanci. Vždy je spuštěn Agent serveru SQL.
- Jsou podporovány jen částečně oznámení
 - Operátor není podporován.
 - Příkazu není podporován.
 - Výstrahy se ještě nepodporuje.
- Nepodporuje proxy servery.
- Protokol událostí se nepodporuje.

Informace o agenta systému SQL Server najdete v tématu [agenta systému SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

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
|[**Úloha**](#job)  |  Úloha je jednotka práce, která se skládá z jedné nebo více [úlohy kroky](#job-step). Kroky úlohy určují skript T-SQL, který se má spustit, a také další podrobnosti potřebné ke spuštění skriptu.  |


#### <a name="elastic-job-agent"></a>Agent elastických úloh

Agent elastických úloh je prostředek Azure určený k vytváření, spouštění a správě úloh. Agent elastických úloh je prostředek Azure, který vytvoříte na portálu (podporuje se také [PowerShell](elastic-jobs-powershell.md) a REST). 

K vytvoření **agenta elastických úloh** je potřeba existující databáze SQL. Agent nakonfiguruje tuto existující databázi jako [*databázi úloh*](#job-database).

Agent elastických úloh je bezplatný. Databáze úloh se účtuje stejnou sazbou jako jakákoli jiná databáze SQL.

#### <a name="job-database"></a>Databáze úloh

*Databáze úloh* slouží k definování úloh a sledování stavu a historie spouštění úloh. *Databáze úloh* se používá také k ukládání metadat agenta, protokolů, výsledků a definic úloh. Navíc obsahuje řadu užitečných uložených procedur a dalších databázových objektů určených k vytváření, spouštění a správě úloh pomocí T-SQL.

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

- **Server SQL Azure** – pokud je zadaný server, součástí skupiny jsou všechny databáze existující na serveru v době spuštění úlohy. Je potřeba zadat přihlašovací údaje k hlavní databázi, aby se mohla skupina určit a aktualizovat před spuštěním úlohy.
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

**Příklad 5** a **příklad 6** ukazují pokročilé scénáře, kdy je možné pomocí pravidel zahrnutí a vyloučení kombinovat Azure SQL Servery, elastické fondy a databáze.<br>
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

- [Co je Agent systému SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) 
- [Vytvoření a správa elastických úloh](elastic-jobs-overview.md) 
- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md) 
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md) 
