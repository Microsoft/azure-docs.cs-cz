---
title: Vytvoření a správa elastické fondy – Azure SQL database | Microsoft Docs
description: Vytvořit a spravovat elastické fondy Azure SQL.
keywords: více databází, databáze prostředků, výkon databáze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313322"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Vytváření a správě fondů elastické databáze SQL Azure

Pomocí fondu elastické databáze určit množství prostředků, které elastického fondu vyžaduje pro zpracování úloh její databáze a objem prostředků pro jednotlivé databáze ve fondu. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portál Azure: Správa elastické fondy a databází ve fondu

Všechna nastavení fondu najdete v jednom místě: **konfigurace fondu** okno. Sem získáte v portálu a klikněte na tlačítko Najít fondu elastické databáze **konfigurace fondu** z horní části okna, nebo z prostředků nabídky na levé straně.

Odsud můžete nastavit libovolnou kombinaci z následujících změn a uložte je všechny v jedné dávce:
1. Služba úroveň fondu změnit.
2. Škálování výkonu (DTU nebo vCores) a úložiště nahoru nebo dolů
3. Přidat nebo odebrat databáze z fondu
4. Nastavte minimum (zaručit) a maximálního počtu omezení výkonu pro databáze ve fondech
5. Zkontrolujte souhrn náklady zobrazíte všechny změny na vašem vyúčtování v důsledku nové vybrané položky

![Okno Konfigurace elastického fondu](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>Prostředí PowerShell: Správa elastické fondy a databází ve fondu 

K vytváření a správě fondů elastické databáze SQL a databáze ve fondu pomocí prostředí Azure PowerShell, použijte následující rutiny prostředí PowerShell. Pokud je potřeba nainstalovat nebo upgradovat prostředí PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). Chcete-li vytvořit a spravovat databáze, servery a pravidla brány firewall, přečtěte si téma [vytvořit a spravovat servery Azure SQL Database a databáze pomocí prostředí PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> Příklad skriptů prostředí PowerShell, najdete v části [vytvořit elastické fondy a přesunutí databází mezi fondy a z fondu pomocí prostředí PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) a [použijte PowerShell ke sledování a škálování elastický fond SQL v Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Vytvoří fond elastické databáze na logický SQL server.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Získá elastické fondy a jejich hodnoty vlastností na logický SQL server.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Upraví vlastnosti fondu elastické databáze na logický SQL server. Například použít **StorageMB** vlastnost změnit maximální úložiště elastického fondu.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Odstraní fond elastické databáze na logický SQL server.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Získá stav operace v elastickém fondu na logický SQL server.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Vytvoří novou databázi v existující fond nebo jedné databáze. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Nastaví vlastnosti pro databázi nebo existující databázi se přesune do, mimo nebo mezi elastické fondy.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Odebere databáze.|


> [!TIP]
> Vytváření mnoho databází v elastickém fondu může trvat dobu, pokud se provádí pomocí portálu nebo rutiny prostředí PowerShell, který současně vytvořit pouze jednu databázi. K automatizaci vytváření fondu elastické databáze, najdete v části [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Rozhraní příkazového řádku Azure: Správa elastické fondy a databází ve fondu

K vytváření a správě fondů elastické databáze SQL se [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Příklad skriptů příkazového řádku Azure CLI, najdete v části [použití rozhraní příkazového řádku přesouvat Azure SQL database v elastický fond SQL](scripts/sql-database-move-database-between-pools-cli.md) a [použití Azure CLI škálování elastický fond SQL v Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[Vytvoření az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Vytvoří fondu elastické databáze.|
|[seznam elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Vrátí seznam hodnot elastické fondy na serveru.|
|[seznam – databáze az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Vrátí seznam databází v elastickém fondu.|
|[seznam edicí az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Také zahrnuje nastavení DTU fondu k dispozici, limity úložiště a pro nastavení databáze. Chcete-li snížit podrobností, limity další úložiště a na databázi nastavení jsou skryté. ve výchozím nastavení.|
|[aktualizace elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Aktualizace fondu elastické databáze.|
|[odstranění elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Odstraní elastický fond.|

## <a name="transact-sql-manage-pooled-databases"></a>Příkaz Transact-SQL: Spravovat databáze ve fondu

Vytvoření a přesunutí databází v rámci existující elastické fondy nebo k vrácení informací o elastického fondu SQL Database pomocí jazyka Transact-SQL, pomocí následujících příkazů T-SQL. Můžete použít tyto příkazy pomocí portálu Azure [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který se může připojit k serveru Azure SQL Database a předat Transact-SQL příkazy. Chcete-li vytvořit a spravovat databáze, servery a pravidla brány firewall, přečtěte si téma [vytvořit a spravovat servery Azure SQL Database a databází pomocí jazyka Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Nelze vytvořit, aktualizovat nebo odstranit fondu elastické databáze Azure SQL Database pomocí jazyka Transact-SQL. Můžete přidat nebo odebrat z fondu elastické databáze a zobrazení dynamické správy můžete použít k vrácení informací o existující elastické fondy.
>

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi v existující fond nebo jedné databáze. Musíte být připojení k hlavní databázi a vytvořte novou databázi.|
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přesun databáze do, mimo nebo mezi elastické fondy.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[Sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Vrátí statistiku využití prostředků pro všechny fondy elastické databáze v logický server. Pro každý fond elastické databáze je jeden řádek pro každou 15 sekundu reporting okno (čtyři řádky za minutu). To zahrnuje využití procesoru, vstupně-výstupní operace, protokolu, spotřebu úložiště a souběžných požadavku nebo relace využití všech databází ve fondu.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (vrstva služby), cíl služby (cenové úrovně) a název elastického fondu, pokud existuje, pro databázi Azure SQL nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi serveru Azure SQL Database, vrátí informace na všechny databáze. Pro Azure SQL Data Warehouse musí být připojen k hlavní databázi.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>Rozhraní API REST: Správa elastické fondy a databází ve fondu

K vytváření a správě fondů elastické databáze SQL a databáze ve fondu, použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Elastické fondy - vytvořit nebo aktualizovat](/rest/api/sql/elasticpools/createorupdate)|Vytvoří nový elastický fond nebo aktualizuje existující elastickém fondu.|
|[Elastické fondy - odstranění](/rest/api/sql/elasticpools/delete)|Odstraní elastický fond.|
|[Elastické fondy - Get](/rest/api/sql/elasticpools/get)|Získá fondu elastické databáze.|
|[Elastické fondy - seznamu serverem](/rest/api/sql/elasticpools/listbyserver)|Vrátí seznam hodnot elastické fondy na serveru.|
|[Elastické fondy - aktualizace](/rest/api/sql/elasticpools/update)|Aktualizace existujícího elastického fondu.|
|[Počet doporučených fondů elastické - Get](/rest/api/sql/recommendedelasticpools/get)|Získá doporučené elastického fondu.|
|[Počet doporučených fondů elastické - seznamu serverem](/rest/api/sql/recommendedelasticpools/listbyserver)|Vrátí počet doporučených fondů elastické.|
|[Počet doporučených fondů elastické - seznamu metriky](/rest/api/sql/recommendedelasticpools/listmetrics)|Vrátí doporučuje metriky elastického fondu.|
|[Elastický fond aktivity](/rest/api/sql/elasticpoolactivities)|Vrátí aktivity elastického fondu.|
|[Fond elastické databáze aktivity](/rest/api/sql/elasticpooldatabaseactivities)|Vrátí aktivita u databází v elastickém fondu.|
|[Databáze - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Get](/rest/api/sql/databases/get)|Získá databáze.|
|[Databáze - získat elastického fondu](/rest/api/sql/databases/getbyelasticpool)|Získá databáze v elastickém fondu.|
|[Získat doporučený fond Elastických databází –](/rest/api/sql/databases/getbyrecommendedelasticpool)|Získá uvnitř doporučený fond elastické databáze.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle doporučených elastického fondu](/rest/api/sql/databases/listbyrecommendedelasticpool)|Vrátí seznam uvnitř doporučený fond elastických databází.|
|[Databáze - seznamu serverem](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|

## <a name="next-steps"></a>Další postup

* Video najdete v tématu [Microsoft Virtual Academy video kurzu na možnostech elastické databáze SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* SaaS pomocí elastické fondy, na adrese [Úvod k aplikaci Wingtip SaaS](sql-database-wtp-overview.md).
