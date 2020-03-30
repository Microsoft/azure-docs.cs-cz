---
title: Úlohy elastické databáze (náhled)
description: Konfigurace úloh elastické databáze (preview) pro spouštění skriptů Transact-SQL (T-SQL) v sadě jedné nebo více databází Azure SQL
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
ms.openlocfilehash: e5b07ac0e9421cbca034b17c573cab16641f49f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214481"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Vytváření, konfigurace a správa elastických úloh

V tomto článku se dozvíte, jak vytvořit, nakonfigurovat a spravovat elastické úlohy.

Pokud jste nepoužili elastické [úlohy, další informace o konceptech automatizace úloh v Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Vytvoření a konfigurace agenta

1. Vytvořte nebo určete prázdnou databázi SQL S0 nebo vyšší. Tato databáze bude použita jako *databáze úloh* během vytváření agenta elastické úlohy.
2. Vytvořte agenta elastické úlohy na [portálu](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) nebo pomocí [prostředí PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Vytváření agenta elastické úlohy](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Vytváření, spouštění a správa úloh

1. Vytvořte pověření pro spuštění úlohy v *databázi úloh* pomocí [prostředí PowerShell](elastic-jobs-powershell.md) nebo [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Definujte cílovou skupinu (databáze, proti kterým chcete úlohu spustit) pomocí [prostředí PowerShell](elastic-jobs-powershell.md) nebo [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. V každé databázi, ve které se bude úloha spouštět, vytvořte přihlašovací údaje agenta úloh [(do každé databáze ve skupině přidejte příslušného uživatele nebo roli)](sql-database-manage-logins.md). Příklad najdete v [kurzu pro PowerShell](elastic-jobs-powershell.md).
4. Vytvořte úlohu pomocí [Prostředí PowerShell](elastic-jobs-powershell.md) nebo [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Pomocí [PowerShellu](elastic-jobs-powershell.md) nebo [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) přidejte kroky úlohy.
6. Spusťte úlohu pomocí [prostředí PowerShell](elastic-jobs-powershell.md#run-the-job) nebo [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Sledování stavu spuštění úlohy pomocí portálu, [prostředí PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) nebo [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Přihlašovací údaje pro spouštění úloh

Úlohy se při spuštění připojují k databázím určeným cílovou skupinou pomocí [přihlašovacích údajů v oboru databáze](/sql/t-sql/statements/create-database-scoped-credential-transact-sql). Pokud cílová skupina obsahuje servery nebo fondy, použijí se tyto přihlašovací údaje v oboru databáze k připojení k hlavní databázi a výčtu dostupných databází.

Nastavení správných přihlašovacích údajů pro spuštění úlohy může být trochu matoucí, proto mějte na paměti následující body:

- Pověření s rozsahem databáze musí být vytvořena v *databázi úloh*.
- **Všechny cílové databáze musí mít přihlášení s [dostatečnými oprávněními](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) pro úspěšné dokončení úlohy** (v`jobuser` diagramu níže).
- Pověření lze znovu použít napříč úlohami a hesla pověření jsou šifrována a zabezpečena od uživatelů, kteří mají přístup jen pro čtení k objektům úloh.

Následující obrázek by vám měl pomoct porozumět přihlašovacím údajům úloh a správně je nastavit. **Nezapomeňte vytvořit příslušného uživatele v každé databázi (ve všech *cílových uživatelských databázích*), ve které se má úloha spouštět**.

![Přihlašovací údaje k elastickým úlohám](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Osvědčené postupy zabezpečení

Tady je několik osvědčených postupů, které byste při práci s elastickými úlohami měli brát v úvahu:

- Omezte možnost používat rozhraní API na důvěryhodné osoby.
- Přihlašovací údaje by měly mít nejnižší úroveň oprávnění nezbytnou k provedení daného kroku úlohy. Další informace naleznete v [tématu Autorizace a oprávnění SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Při použití člena cílové skupiny serveru nebo fondu se důrazně doporučuje vytvořit samostatné pověření s právy v hlavní databázi k zobrazení nebo seznamu databází, které se používají k rozšíření databázových seznamů serverů nebo fondu před spuštěním úlohy.

## <a name="agent-performance-capacity-and-limitations"></a>Výkon, kapacita a omezení agenta

Elastické úlohy při čekání na dokončení dlouhotrvajících úloh využívají minimum výpočetních prostředků.

V závislosti na velikosti cílové skupiny databází a požadované době spuštění úlohy (počet souběžných pracovních procesů) vyžaduje agent pro *databázi úloh* různé úrovně výpočetních prostředků a výkonu (čím je více cílů a úloh, tím je potřeba více výpočetních prostředků).

V současné době je verze Preview omezená na 100 souběžných úloh.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zabránění snižování výkonu cílových databází úlohami

Pokud chcete zajistit, aby při spouštění úloh pro databáze v elastickém fondu SQL nedocházelo k přetížení prostředků, můžete pro úlohy nakonfigurovat omezení počtu databází, pro které se můžou najednou spouštět.

Nastavte počet souběžných databází, na kterých `sp_add_jobstep` úloha běží, nastavením parametru uložené procedury `@max_parallelism` v T-SQL nebo `Add-AzSqlElasticJobStep -MaxParallelism` v prostředí PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Osvědčené postupy pro vytváření úloh

### <a name="idempotent-scripts"></a>Idempotentní skripty
Skripty T-SQL úlohy musí být [idempotentní](https://en.wikipedia.org/wiki/Idempotence). **Idempotentní** znamená, že pokud se skript úspěšně provedete a spustí se znovu, výsledek bude stejný. Skript může selhat kvůli přechodným problémům se sítí. V takovém případě se úloha automaticky pokusí znovu skript spustit tolikrát, kolikrát je uvedeno v předvolbách, a pak přestane. Výsledek idempotentního skriptu je stejný, i když se úspěšně spustí dvakrát (nebo vícekrát).

Jednoduchou taktikou je před vytvořením objektu otestovat, jestli už neexistuje.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Skript se také musí být schopný úspěšně provést. Dosáhne toho tím, že logicky testuje podmínky a reaguje na případné podmínky, které najde.



## <a name="next-steps"></a>Další kroky

- [Vytváření a správa elastických úloh s využitím PowerShellu](elastic-jobs-powershell.md)
- [Vytváření a správa elastických úloh pomocí Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
