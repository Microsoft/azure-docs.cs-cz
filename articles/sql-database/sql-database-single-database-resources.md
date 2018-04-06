---
title: Izolované databáze Azure SQL Database | Microsoft Docs
description: Spravovat úrovně služby, úroveň výkonu a množství storagea pro jednu databázi Azure SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 87cd4c565a7429ffb7096034823a1f5ff49df195
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Spravovat prostředky pro jednu databázi v databázi SQL Azure

Jedné databáze můžete určit množství prostředků, které vyžaduje databázi pro zpracování zátěž na úroveň služby, úroveň výkonu a velikost úložiště, která vyžaduje. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Spravovat prostředky jedné databáze pomocí portálu Azure

Chcete-li nastavit nebo změnit úroveň služby, úroveň výkonu nebo množství úložiště pro nový nebo existující databázi Azure SQL pomocí portálu Azure, otevřete **konfigurace výkonu** okno pro vaši databázi kliknutím **cenová úroveň ( škálování Dtu)** – jak je znázorněno na následujícím snímku obrazovky. 

- Nastavení nebo změna vrstvy služby výběrem vrstvy služeb pro vaši úlohu. 
- Nastavit nebo změnit úroveň výkonu (**Dtu**) v rámci vrstvy služby pomocí **DTU** posuvníku.
- Nastavit nebo změnit velikost úložiště pro úrovně výkonu pomocí **úložiště** posuvníku. 

![Konfigurace úrovně služeb a výkonu](./media/sql-database-single-database-resources/change-service-tier.png)

Klikněte na tlačítko **přehled** monitorování nebo zrušte probíhající operaci.

![Operace zrušení](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Zkontrolujte [aktuální omezení P11 a P15 databází s maximální velikost 4 TB](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) při výběru P11 nebo P15 vrstvy služeb.
>

## <a name="manage-single-database-resources-using-powershell"></a>Spravovat prostředky jedné databáze pomocí prostředí PowerShell

Chcete-li nastavit nebo změnit úrovních služby databáze Azure SQL, úrovně výkonu a množství úložiště pomocí prostředí PowerShell, použijte tyto rutiny prostředí PowerShell. Pokud je potřeba nainstalovat nebo upgradovat prostředí PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Rutina | Popis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Vytvoří databázi |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Získá jednu nebo více databází|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databáze do pružného fondu. Například použít **MaxSizeBytes** vlastnost nastavení maximální velikosti pro databázi.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Získá stav databázových operací. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Zruší asynchronní aktualizace operaci v databázi.|


> [!TIP]
> Ukázkový skript prostředí PowerShell, který monitoruje metriky výkonu databáze, škálovatelná pro vyšší úroveň výkonu a vytvoří pravidlo výstrahy na jednom metrik výkonu, najdete v části [sledování a škálování jeden SQL databáze pomocí prostředí PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Správa prostředků jedné databáze pomocí rozhraní příkazového řádku Azure

Nastavit nebo změnit databáze Azure SQL úrovně služeb, úrovně výkonu a množství úložiště pomocí rozhraní příkazového řádku Azure používat tyto [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Vytváření a Správa SQL elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

| Rutina | Popis |
| --- | --- |
|[Vytvoření brány firewall pravidlo az sql serveru](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Vytvoří pravidlo brány firewall serveru|
|[seznam az sql serverů pravidlo brány firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Zobrazí seznam pravidel brány firewall na serveru|
|[Zobrazit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Zobrazí podrobnosti pravidla brány firewall|
|[aktualizace pravidla brány firewall az sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizace pravidla brány firewall|
|[Odstranit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Odstraní pravidlo brány firewall|
|[AZ sql db op seznamu](/cli/azure/sql/db/op?#az_sql_db_op_list)|Získá seznam operací provedených v databázi.|
|[AZ sql db op Storno](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Zruší asynchronní operaci v databázi.|

> [!TIP]
> Ukázkový skript příkazového řádku Azure CLI, která je škálovatelná jedné databáze Azure SQL na úroveň výkonu různých po dotaz na informace o velikosti databáze, najdete v části [použití rozhraní příkazového řádku pro sledování a škálování jedné databáze SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Spravovat prostředky jedné databáze pomocí jazyka Transact-SQL

Nastavení nebo změna úrovně služeb databáze Azure SQL, úrovně výkonu a množství úložiště pomocí jazyka Transact-SQL, pomocí těchto příkazů T-SQL. Můžete použít tyto příkazy pomocí portálu Azure [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který se může připojit k serveru Azure SQL Database a předat Transact-SQL příkazy. 

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi. Musíte být připojení k hlavní databázi a vytvořte novou databázi.|
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Upravuje Azure SQL database. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (vrstva služby), cíl služby (cenové úrovně) a název elastického fondu, pokud existuje, pro databázi Azure SQL nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi serveru Azure SQL Database, vrátí informace na všechny databáze. Pro Azure SQL Data Warehouse musí být připojen k hlavní databázi.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Uvádí počet, typ a doba trvání databází na serveru Azure SQL Database.|

Následující příklad ukazuje maximální velikost pro databázi se změnit pomocí příkazu ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Spravovat prostředky jedné databáze pomocí rozhraní REST API

Nastavit nebo změnit databáze Azure SQL úrovně služeb, úrovně výkonu a množství úložiště použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Databáze - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Get](/rest/api/sql/databases/get)|Získá databáze.|
|[Databáze - získat elastického fondu](/rest/api/sql/databases/getbyelasticpool)|Získá databáze v elastickém fondu.|
|[Získat doporučený fond Elastických databází –](/rest/api/sql/databases/getbyrecommendedelasticpool)|Získá databázi uvnitř recommented elastického fondu.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle doporučených elastického fondu](/rest/api/sql/databases/listbyrecommendedelasticpool)|Vrátí seznam databází uvnitř recommented elastického fondu.|
|[Databáze - seznamu serverem](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Operace – seznam](/rest/api/sql/Operations/List)|Zobrazí seznam všech dostupných operací SQL Rest API.|



## <a name="next-steps"></a>Další postup

- Další informace o úrovních služeb, úrovně výkonu a objemy úložiště, najdete v tématu [úrovních služeb](sql-database-service-tiers.md).
- Další informace o elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
- Další informace o [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md)
