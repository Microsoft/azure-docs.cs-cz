---
title: Vytváření, správa serverů a jednotlivých databází
description: Přečtěte si o vytváření a správě serverů SQL Database a jednotlivých databází.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268545"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Vytváření a správa databázových serverů SQL a jednotlivých databází v Azure SQL Database

Servery sql database a jednotlivé databáze můžete vytvářet a spravovat pomocí portálu Azure, PowerShellu, rozhraní API Azure, rozhraní REST API a Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Portál Azure: Správa databázových serverů SQL a jednotlivých databází

Skupinu prostředků databáze Azure SQL můžete vytvořit předem nebo při vytváření samotného serveru. Existuje několik metod pro získání nového formuláře serveru SQL, buď vytvořením nového serveru SQL, nebo jako součást vytvoření nové databáze.

### <a name="create-a-blank-sql-database-server"></a>Vytvoření prázdného databázového serveru SQL

Chcete-li vytvořit databázový server SQL pomocí [portálu Azure](https://portal.azure.com), přejděte na prázdný formulář serveru SQL (logický server).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Vytvoření prázdné nebo ukázkové jediné databáze SQL

Pokud chcete pomocí [portálu Azure portal](https://portal.azure.com)vytvořit jednu databázi Azure , přejděte na prázdný formulář databáze SQL a poskytněte požadované informace. Skupinu prostředků databáze Azure SQL a server SQL Database můžete vytvořit předem nebo při vytváření samotné jedné databáze. Můžete vytvořit prázdnou databázi nebo vytvořit ukázkovou databázi založenou na řešení Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenové úrovně pro vaši databázi naleznete v [tématu Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Pokud chcete vytvořit spravovanou instanci, [přečtěte si tématu Vytvoření spravované instance.](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Správa existujícího databázového serveru SQL

Chcete-li spravovat existující databázový server SQL, přejděte na server pomocí několika metod – například z konkrétní stránky databáze SQL, stránky **serverů SQL** nebo stránky **Všechny prostředky.**

Chcete-li spravovat existující databázi, přejděte na stránku **databáze SQL** a klepněte na databázi, kterou chcete spravovat. Následující snímek obrazovky ukazuje, jak začít nastavovat bránu firewall na úrovni serveru pro databázi ze stránky **Přehled** pro databázi.

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Informace o konfiguraci vlastností výkonu databáze naleznete v [tématu Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
> [!TIP]
> Rychlý start na webu Azure portal najdete [v tématu Vytvoření databáze Azure SQL na webu Azure Portal](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: Správa databázových serverů SQL a jednotlivých databází

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvářet a spravovat servery Azure SQL Database, jednoasložkované databáze a brány firewall serveru SQL Database server s Azure PowerShellem, použijte následující rutiny Prostředí PowerShell. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Pro ukázkové skripty PowerShellu najdete [v tématu Použití PowerShellu k vytvoření jediné databáze Azure SQL a konfiguraci pravidla brány firewall serveru SQL Database a](scripts/sql-database-create-and-configure-database-powershell.md) monitorování a [škálování jediné databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Rutina | Popis |
| --- | --- |
|[Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří databázi |
|[Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti databáze nebo přesune existující databázi do elastického fondu.|
|[Odebrat databázi AzSql](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Vytvoří skupinu prostředků.|
|[Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Vytvoří server|
|[Získat-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Vrátí informace o serverech.|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Upravuje vlastnosti serveru.|
|[Odebrat-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Odebere server.|
|[Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru. |
|[Pravidlo brány Get-AzSqlServerFirewallFirewall](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Získá pravidla brány firewall pro server|
|[Pravidlo programu Set-AzSqlServerFirewallFirewall](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Upraví pravidlo brány firewall na serveru.|
|[Odebrat-AzSqlServerFirewallFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| Nové pravidlo virtuální sítě AzSqlServer | Vytvoří [*pravidlo virtuální sítě*](sql-database-vnet-service-endpoint-rule-overview.md)založené na podsíti, která je koncovým bodem služby virtuální sítě. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: Správa serverů SQL Database a jednotlivých databází

Pokud chcete vytvářet a spravovat Azure SQL server, databáze a brány firewall pomocí [Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Database.](/cli/azure/sql/db) Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Vytváření a správa elastických fondů najdete [v tématu Elastické fondy](sql-database-elastic-pool.md).

> [!TIP]
> Rychlý start příkazového příkazového odložšte od Azure najdete [v tématu Vytvoření jediné databáze Azure SQL pomocí azure CLI](sql-database-cli-samples.md). Pro ukázkové skripty Azure CLI najdete v článku [Použití rozhraní příkazového příkazu k vytvoření jediné databáze Azure SQL a konfiguraci pravidla brány firewall databáze SQL](scripts/sql-database-create-and-configure-database-cli.md) database a použití rozhraní [PŘÍKAZOVÉHO příkazu ke sledování a škálování jediné databáze Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Vytvoří databázi|
|[az sql db seznam](/cli/azure/sql/db#az-sql-db-list)|Zobrazí seznam všech databází a datových skladů na serveru nebo všech databází v elastickém fondu.|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Seznam dostupných cílů služeb a omezení úložiště|
|[az sql db seznam-použití](/cli/azure/sql/db#az-sql-db-list-usages)|Vrátí využití databáze.|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Získá databázi nebo datový sklad.|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje databázi.|
|[az sql db odstranit](/cli/azure/sql/db#az-sql-db-delete)|Odebere databázi.|
|[az group create](/cli/azure/group#az-group-create)|Vytvoří skupinu prostředků.|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Vytvoří server|
|[seznam serveru az SQL](/cli/azure/sql/server#az-sql-server-list)|Seznam serverů|
|[Az sql server seznam-použití](/cli/azure/sql/server#az-sql-server-list-usages)|Vrátí použití serveru.|
|[az sql server zobrazit](/cli/azure/sql/server#az-sql-server-show)|Získá server|
|[az sql server aktualizace](/cli/azure/sql/server#az-sql-server-update)|Aktualizuje server|
|[az sql server odstranit](/cli/azure/sql/server#az-sql-server-delete)|Odstraní server.|
|[Vytvoření pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Vytvoří pravidlo brány firewall serveru.|
|[seznam pravidel brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Zobrazí seznam pravidel brány firewall na serveru.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Zobrazuje podrobnosti pravidla brány firewall.|
|[Aktualizace pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje pravidlo brány firewall.|
|[az sql server firewall-rule odstranit](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Odstraní pravidlo brány firewall.|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: Správa databázových serverů SQL a jednotlivých databází

Chcete-li vytvořit a spravovat Azure SQL server, databáze a brány firewall s Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí portálu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), Visual Studio [Code](https://code.visualstudio.com/docs)nebo jakékoli jiné aplikace, která se může připojit k serveru Azure SQL Database a předat příkazy Transact-SQL. Správa elastických fondů najdete [v tématu Elastické fondy](sql-database-elastic-pool.md).

> [!TIP]
> Úvodní příručka pomocí sql server management studia v microsoft windows najdete v článku [Azure SQL Database: Use SQL Server Management Studio pro připojení a dotazování na data](sql-database-connect-query-ssms.md). Úvodní příručka k použití kódu Visual Studia v systémech macOS, Linux nebo Windows najdete v [tématu Azure SQL Database: Use Visual Studio Code pro připojení a dotazování na data](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Server pomocí transact-SQL nelze vytvořit ani odstranit.

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Vytvoří novou jednu databázi. Chcete-li vytvořit novou databázi, musíte být připojeni k hlavní databázi.|
| [DATABÁZE ALTER (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Upravuje databázi Azure SQL. |
|[DROP DATABÁZE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edici (úroveň služby), cíl služby (cenovou úroveň) a případný název elastického fondu pro databázi Azure SQL nebo datový sklad Azure SQL. Pokud jste přihlášeni k hlavní databázi na serveru Azure SQL Database, vrátí informace o všech databázích. Pro Azure SQL Data Warehouse musíte být připojeni k hlavní databázi.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí využití procesoru, vi a paměti pro databázi Azure SQL Database. Jeden řádek existuje každých 15 sekund, i v případě, že neexistuje žádná aktivita v databázi.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí využití procesoru a data úložiště pro azure sql database. Data jsou shromažďována a agregována v pětiminutových intervalech.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiky pro události připojení databáze SQL Database, poskytující přehled o úspěchy připojení k databázi a selhání. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšná připojení databáze Azure SQL Database, selhání připojení a zablokování. Tyto informace můžete použít ke sledování nebo řešení potíží s databázovou aktivitou pomocí databáze SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro databázový server SQL. Tato uložená procedura je k dispozici pouze v hlavní databázi pro hlavní přihlášení na úrovni serveru. Pravidlo brány firewall na úrovni serveru lze vytvořit pomocí technologie Transact-SQL až po vytvoření prvního pravidla brány firewall na úrovni serveru uživatelem s oprávněními na úrovni Azure.|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni serveru přidružené k databázi Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni serveru z databázového serveru SQL. Tato uložená procedura je k dispozici pouze v hlavní databázi pro hlavní přihlášení na úrovni serveru.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze pro azure sql database nebo sql datový sklad. Pravidla brány firewall databáze lze nakonfigurovat pro hlavní databázi a pro uživatelské databáze v databázi SQL. Pravidla brány firewall databáze jsou užitečná při použití obsahujících uživatelů databáze. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni databáze přidružené k databázi Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni databáze z databáze Azure SQL Database nebo datového skladu SQL. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>ROZHRANÍ REST API: Správa databázových serverů SQL a jednotlivých databází

Chcete-li vytvořit a spravovat Server Azure SQL, databáze a brány firewall, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje nový server.|
|[Servery - Odstranit](https://docs.microsoft.com/rest/api/sql/servers/delete)|Odstraní server SQL.|
|[Servery - Získat](https://docs.microsoft.com/rest/api/sql/servers/get)|Získá server.|
|[Servery - seznam](https://docs.microsoft.com/rest/api/sql/servers/list)|Vrátí seznam serverů v předplatném.|
|[Servery – seznam podle skupin prostředků](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Vrátí seznam serverů ve skupině prostředků.|
|[Servery - Aktualizace](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje existující server.|
|[Databáze – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Odstranit](https://docs.microsoft.com/rest/api/sql/databases/delete)|Odstraní databázi.|
|[Databáze - Získat](https://docs.microsoft.com/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze - Seznam podle serveru](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - Aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Pravidla brány firewall – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Vytvoří nebo aktualizuje pravidlo brány firewall.|
|[Pravidla brány firewall - Odstranit](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Odstraní pravidlo brány firewall.|
|[Pravidla brány firewall - Získat](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Získá pravidlo brány firewall.|
|[Pravidla brány firewall – seznam podle serveru](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Vrátí seznam pravidel brány firewall.|

## <a name="next-steps"></a>Další kroky

- Další informace o migraci databáze SQL Serveru do Azure najdete v tématu [Migrace do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
