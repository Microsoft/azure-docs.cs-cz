---
title: Vytvořit a spravovat servery Azure SQL a izolované databáze | Microsoft Docs
description: Další informace o vytváření a správě logické servery a izolované databáze.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 918cfd0257c82e84451e07ef904dbda331f47b95
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313308"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Vytvářet a spravovat logické servery a izolované databáze v databázi SQL Azure 

Můžete vytvořit a spravovat logické servery databáze SQL Azure a jedné databáze pomocí portálu Azure, prostředí PowerShell, rozhraní příkazového řádku Azure, rozhraní API REST a Transact-SQL.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Portál Azure: Správa logické servery a databáze

Můžete vytvořit skupinu prostředků Azure SQL database předem nebo při vytváření samotný server. Existuje více metod pro získání nového formuláře SQL serveru tak, že vytvoříte nový server SQL nebo jako součást vytvoření nové databáze. 

### <a name="create-a-blank-sql-server-logical-server"></a>Vytvořit prázdný SQL server (logický server)

K vytvoření serveru (bez databáze) Azure SQL Database pomocí [portál Azure](https://portal.azure.com), přejděte do prázdného formuláře SQL server (logický server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Vytvoření databáze SQL prázdné nebo ukázku

Chcete-li vytvořit databázi Azure SQL pomocí [portál Azure](https://portal.azure.com), přejděte do prázdného formuláře databáze SQL a zadejte požadované informace. Můžete vytvořit skupinu prostředků a logický server předem nebo při vytváření samotná databáze Azure SQL database. Můžete vytvořit prázdnou databázi nebo vytvoření ukázkové databáze založené na společnosti Adventure Works LT. 

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenovou úroveň pro vaši databázi najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).

K vytvoření Instance spravované, najdete v části [vytvořit instanci spravované](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>Spravovat existující server SQL

Chcete-li spravovat existující server, přejděte na server s počtem metody – například od konkrétní stránky databáze SQL, **servery SQL** stránky, nebo **všechny prostředky** stránky. 

Chcete-li spravovat stávající databázi, přejděte na **databází SQL** a klikněte na tlačítko databáze, které chcete spravovat. Následující snímek obrazovky ukazuje, jak začít, nastavení brány firewall úrovni serveru pro databázi z **přehled** stránky pro databázi. 

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Konfigurace vlastnosti výkonu pro databázi najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).
>

> [!TIP]
> Azure quickstart portálu, najdete v části [vytvoření Azure SQL database na portálu Azure](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>Prostředí PowerShell: Správa logické servery a databáze

Chcete-li vytvořit a spravovat server Azure SQL, databáze a brány firewall pomocí prostředí Azure PowerShell, použijte následující rutiny prostředí PowerShell. Pokud je potřeba nainstalovat nebo upgradovat prostředí PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!TIP]
> Rychlé spuštění prostředí PowerShell najdete v části [vytvářet izolované databáze Azure SQL pomocí prostředí PowerShell](sql-database-get-started-portal.md). Příklad skriptů prostředí PowerShell, najdete v části [použít PowerShell k vytvoření jedné databáze Azure SQL a nakonfigurujte pravidlo brány firewall](scripts/sql-database-create-and-configure-database-powershell.md) a [sledování a škálování jeden SQL databáze pomocí prostředí PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Vytvoří databázi |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Získá jednu nebo více databází|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databáze do pružného fondu|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Odebere databáze|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Vytvoří skupinu prostředků.|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Vytvoří serveru|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Vrátí informace o serverech|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Upraví vlastnosti serveru|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Odebere server|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Získá pravidla brány firewall pro server|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Upraví pravidlo brány firewall na serveru|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| New-AzureRmSqlServerVirtualNetworkRule | Vytvoří [ *pravidlo virtuální sítě*](sql-database-vnet-service-endpoint-rule-overview.md)založená na podsíť, která je koncový bod služby virtuální sítě. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Rozhraní příkazového řádku Azure: Správa logické servery a databáze

Vytvoření a Správa serveru Azure SQL, databáze a brány firewall s [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Vytváření a správa elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

> [!TIP]
> Rychlé spuštění příkazového řádku Azure CLI, najdete v části [vytvářet izolované databáze Azure SQL pomocí rozhraní příkazového řádku Azure](sql-database-get-started-cli.md). Příklad skriptů příkazového řádku Azure CLI, najdete v části [použití rozhraní příkazového řádku k vytvoření jedné databáze Azure SQL a nakonfigurujte pravidlo brány firewall](scripts/sql-database-create-and-configure-database-cli.md) a [použití rozhraní příkazového řádku pro sledování a škálování jedné databáze SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Vytvoří databázi|
|[AZ sql db seznamu](/cli/azure/sql/db#az_sql_db_list)|Zobrazí všechny databáze a datových skladů na serveru, nebo všechny databáze v elastickém fondu|
|[seznam edicí az sql db](/cli/azure/sql/db#az_sql_db_list_editions)|Seznamy, které jsou k dispozici služby cíle a limity úložiště|
|[db sql az seznamu – použití](/cli/azure/sql/db#az_sql_db_list_usages)|Vrátí databáze použití|
|[AZ sql db zobrazit](/cli/azure/sql/db#az_sql_db_show)|Získá databáze nebo datového skladu|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Aktualizuje databázi|
|[Odstranění databáze sql az](/cli/azure/sql/db#az_sql_db_delete)|Odebere databáze|
|[az group create](/cli/azure/group#az_group_create)|Vytvoří skupinu prostředků.|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Vytvoří serveru|
|[seznam serverů sql az](/cli/azure/sql/server#az_sql_server_list)|Vytvoří seznam serverů|
|[server sql az seznamu – použití](/cli/azure/sql/server#az_sql_server_list_usages)|Vrátí použití serveru|
|[AZ sql serveru zobrazit](/cli/azure/sql/server#az_sql_server_show)|Získá serveru|
|[aktualizace az sql server](/cli/azure/sql/server#az_sql_server_update)|Aktualizace serveru|
|[Odstranění serveru sql az](/cli/azure/sql/server#az_sql_server_delete)|Odstraní server|
|[Vytvoření brány firewall pravidlo az sql serveru](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Vytvoří pravidlo brány firewall serveru|
|[seznam az sql serverů pravidlo brány firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Zobrazí seznam pravidel brány firewall na serveru|
|[Zobrazit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Zobrazí podrobnosti pravidla brány firewall|
|[aktualizace pravidla brány firewall az sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizace pravidla brány firewall|
|[Odstranit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Odstraní pravidlo brány firewall|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Příkaz Transact-SQL: Správa logické servery a databáze

Vytvoření a Správa serveru Azure SQL, databáze a brány firewall pomocí jazyka Transact-SQL, pomocí následujících příkazů T-SQL. Můžete použít tyto příkazy pomocí portálu Azure [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který se může připojit k serveru Azure SQL Database a předat Transact-SQL příkazy. Správa elastické fondy, najdete v části [elastické fondy](sql-database-elastic-pool.md).


> [!TIP]
> Rychlý start pomocí SQL Server Management Studio v systému Windows, najdete v části [Azure SQL Database: pomocí SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md). Rychlý start pomocí kódu v jazyce Visual Studio v systému macOS, Linux nebo Windows, najdete v části [Azure SQL Database: použití Visual Studio Code k připojení a dotazování dat](sql-database-connect-query-vscode.md).

> [!IMPORTANT]
> Nelze vytvořit nebo odstranit serveru pomocí jazyka Transact-SQL.
>

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi. Musíte být připojení k hlavní databázi a vytvořte novou databázi.|
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Upravuje Azure SQL database. |
|[Příkaz ALTER databáze (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Upravuje datovým skladem Azure SQL.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (vrstva služby), cíl služby (cenové úrovně) a název elastického fondu, pokud existuje, pro databázi Azure SQL nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi serveru Azure SQL Database, vrátí informace na všechny databáze. Pro Azure SQL Data Warehouse musí být připojen k hlavní databázi.|
|[Sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí spotřeby procesoru, vstupně-výstupní operace a paměti pro databázi Azure SQL Database. Jeden řádek existuje pro každých 15 sekund, i když je v databázi žádná aktivita.|
|[Sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí data o využití a úložiště procesoru pro Azure SQL Database. Data jsou nasbírána a shrnuta v pěti minutách.|
|[Sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiku pro události připojení databáze SQL Database, umožní získat přehled o databáze připojení úspěchy a selhání. |
|[Sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšné připojení databáze Azure SQL Database, připojení selhání a zablokování. Tyto informace můžete sledovat a řešit potíže aktivitu vaší databáze SQL Database.|
|[příkaz sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro server služby SQL Database. Tuto uloženou proceduru lze pouze v hlavní databázi, a hlavní přihlášení úrovni serveru. Pravidlo brány firewall na úrovni serveru lze vytvořit pouze po vytvoření první pravidlo brány firewall na úrovni serveru uživatel oprávnění na úrovni Azure pomocí jazyka Transact-SQL|
|[Sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrací informace o nastavení brány firewall na úrovni serveru přidružené k vaší databázi SQL Azure Microsoft.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Nastavení brány firewall na úrovni serveru odebere server služby SQL Database. Tuto uloženou proceduru lze pouze v hlavní databázi, a hlavní přihlášení úrovni serveru.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze Azure SQL Database nebo SQL Data Warehouse. Pravidla firewallu databáze lze konfigurovat pro hlavní databázi a pro uživatele databáze pro službu SQL Database. Pravidla firewallu databáze jsou užitečné, pokud pomocí obsažené uživatele databáze. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrací informace o nastavení brány firewall na úrovni databáze přidružené k vaší databázi SQL Azure Microsoft. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Nastavení brány firewall na úrovni databáze odebere z Azure SQL Database nebo SQL Data Warehouse. |



## <a name="rest-api-manage-logical-servers-and-databases"></a>Rozhraní API REST: Správa logické servery a databáze

Vytvoření a Správa serveru Azure SQL, databáze a brány firewall, použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvořit nebo aktualizovat](/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje na nový server.|
|[Servery – odstranit](/rest/api/sql/servers/delete)|Odstraní systému SQL server.|
|[Servery – Get](/rest/api/sql/servers/get)|Získá serveru.|
|[Servery – seznam](/rest/api/sql/servers/list)|Vrátí seznam serverů.|
|[Servery – seznam podle skupiny prostředků](/rest/api/sql/servers/listbyresourcegroup)|Vrátí seznam serverů ve skupině prostředků.|
|[Servery – aktualizace](/rest/api/sql/servers/update)|Aktualizuje existující server.|
|[Databáze - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Get](/rest/api/sql/databases/get)|Získá databáze.|
|[Databáze - získat elastického fondu](/rest/api/sql/databases/getbyelasticpool)|Získá databáze v elastickém fondu.|
|[Získat doporučený fond Elastických databází –](/rest/api/sql/databases/getbyrecommendedelasticpool)|Získá databázi uvnitř recommented elastického fondu.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle doporučených elastického fondu](/rest/api/sql/databases/listbyrecommendedelasticpool)|Vrátí seznam uvnitř doporučený fond elastických databází.|
|[Databáze - seznamu serverem](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Brány firewall pravidla - vytvořit nebo aktualizovat](/rest/api/sql/firewallrules/createorupdate)|Vytvoří nebo aktualizuje pravidlo brány firewall.|
|[Pravidla brány firewall - odstranění](/rest/api/sql/firewallrules/delete)|Odstraní pravidlo brány firewall.|
|[Pravidla brány firewall - Get](/rest/api/sql/firewallrules/get)|Získá pravidla brány firewall.|
|[Pravidla brány firewall - seznamu serverem](/rest/api/sql/firewallrules/listbyserver)|Vrátí seznam pravidel brány firewall.|

## <a name="next-steps"></a>Další postup

- Další informace o migraci databáze SQL serveru do Azure najdete v tématu [migrací do Azure SQL Database](sql-database-cloud-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
