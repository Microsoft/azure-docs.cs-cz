---
title: Úlohy Elastic Database (Preview)
description: Configure Elastic Database Jobs (preview) to run Transact-SQL (T-SQL) scripts across a set of one or more Azure SQL databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 283b4004f34372104eb083496400772884f5965e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420375"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Create, configure, and manage elastic jobs

In this article, you will learn how to create, configure, and manage elastic jobs.

If you have not used Elastic jobs, [learn more about the job automation concepts in Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Vytvoření a konfigurace agenta

1. Vytvořte nebo určete prázdnou databázi SQL S0 nebo vyšší. This database will be used as the *Job database* during Elastic Job agent creation.
2. Create an Elastic Job agent in the [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) or with [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Creating Elastic Job agent](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Vytváření, spouštění a správa úloh

1. Create a credential for job execution in the *Job database* using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Define the target group (the databases you want to run the job against) using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. V každé databázi, ve které se bude úloha spouštět, vytvořte přihlašovací údaje agenta úloh [(do každé databáze ve skupině přidejte příslušného uživatele nebo roli)](sql-database-control-access.md). Příklad najdete v [kurzu pro PowerShell](elastic-jobs-powershell.md).
4. Create a job using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Pomocí [PowerShellu](elastic-jobs-powershell.md) nebo [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) přidejte kroky úlohy.
6. Run a job using [PowerShell](elastic-jobs-powershell.md#run-the-job) or [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitor job execution status using the portal, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) or [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Přihlašovací údaje pro spouštění úloh

Úlohy se při spuštění připojují k databázím určeným cílovou skupinou pomocí [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Pokud cílová skupina obsahuje servery nebo fondy, použijí se tyto přihlašovací údaje v oboru databáze k připojení k hlavní databázi a výčtu dostupných databází.

Nastavení správných přihlašovacích údajů pro spuštění úlohy může být trochu matoucí, proto mějte na paměti následující body:

- Přihlašovací údaje v oboru databáze se musí vytvořit v *databázi úloh*.
- **All target databases must have a login with [sufficient permissions](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) for the job to complete successfully** (`jobuser` in the diagram below).
- Credentials can be reused across jobs, and the credential passwords are encrypted and secured from users who have read-only access to job objects.

Následující obrázek by vám měl pomoct porozumět přihlašovacím údajům úloh a správně je nastavit. **Nezapomeňte vytvořit příslušného uživatele v každé databázi (ve všech *cílových uživatelských databázích*), ve které se má úloha spouštět**.

![Přihlašovací údaje k elastickým úlohám](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Tady je několik osvědčených postupů, které byste při práci s elastickými úlohami měli brát v úvahu:

- Omezte možnost používat rozhraní API na důvěryhodné osoby.
- Přihlašovací údaje by měly mít nejnižší úroveň oprávnění nezbytnou k provedení daného kroku úlohy. For more information, see [Authorization and Permissions SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- When using a server and/or pool target group member, it is highly suggested to create a separate credential with rights on the master database to view/list databases that is used to expand the database lists of the server(s) and/or pool(s) prior to the job execution.

## <a name="agent-performance-capacity-and-limitations"></a>Výkon, kapacita a omezení agenta

Elastické úlohy při čekání na dokončení dlouhotrvajících úloh využívají minimum výpočetních prostředků.

V závislosti na velikosti cílové skupiny databází a požadované době spuštění úlohy (počet souběžných pracovních procesů) vyžaduje agent pro *databázi úloh* různé úrovně výpočetních prostředků a výkonu (čím je více cílů a úloh, tím je potřeba více výpočetních prostředků).

V současné době je verze Preview omezená na 100 souběžných úloh.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zabránění snižování výkonu cílových databází úlohami

Pokud chcete zajistit, aby při spouštění úloh pro databáze v elastickém fondu SQL nedocházelo k přetížení prostředků, můžete pro úlohy nakonfigurovat omezení počtu databází, pro které se můžou najednou spouštět.

Set the number of concurrent databases a job runs on by setting the `sp_add_jobstep` stored procedure's `@max_parallelism` parameter in T-SQL, or `Add-AzSqlElasticJobStep -MaxParallelism` in PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Osvědčené postupy pro vytváření úloh

### <a name="idempotent-scripts"></a>Idempotentní skripty
Skripty T-SQL úlohy musí být [idempotentní](https://en.wikipedia.org/wiki/Idempotence). **Idempotentní** znamená, že pokud se skript úspěšně provedete a spustí se znovu, výsledek bude stejný. Skript může selhat kvůli přechodným problémům se sítí. V takovém případě se úloha automaticky pokusí znovu skript spustit tolikrát, kolikrát je uvedeno v předvolbách, a pak přestane. Výsledek idempotentního skriptu je stejný, i když se úspěšně spustí dvakrát (nebo vícekrát).

Jednoduchou taktikou je před vytvořením objektu otestovat, jestli už neexistuje.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Skript se také musí být schopný úspěšně provést. Dosáhne toho tím, že logicky testuje podmínky a reaguje na případné podmínky, které najde.



## <a name="next-steps"></a>Další kroky

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
