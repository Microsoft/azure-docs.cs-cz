---
title: Vytvoření & Správa serverů a samostatných databází
description: Přečtěte si, jak vytvářet a spravovat servery a izolované databáze v Azure SQL Database pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, jazyka Transact-SQL (T-SQL) a rozhraní REST API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 2c487b5bc5c8d5fa01388b2942a70defa0001253
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791524"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Vytváření a Správa serverů a samostatných databází v Azure SQL Database

Můžete vytvářet a spravovat servery a izolované databáze v Azure SQL Database pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, REST API a jazyka Transact-SQL.

## <a name="the-azure-portal"></a>Azure Portal

Můžete vytvořit skupinu prostředků pro Azure SQL Database před časem nebo vytvořením samotného serveru.

### <a name="create-a-server"></a>Vytvoření serveru

Pokud chcete vytvořit server pomocí [Azure Portal](https://portal.azure.com), vytvořte nový prostředek [serveru](logical-servers.md) z Azure Marketplace. Případně můžete vytvořit server při nasazení Azure SQL Database.

  ![vytvořit server](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Vytvoření prázdné nebo ukázkové databáze

Pokud chcete vytvořit jednu Azure SQL Database pomocí [Azure Portal](https://portal.azure.com), vyberte v Azure Marketplace prostředek Azure SQL Database. Můžete vytvořit skupinu prostředků a Server před časem nebo při vytváření samostatné databáze. Můžete vytvořit prázdnou databázi nebo vytvořit ukázkovou databázi založenou na Adventure Works LT.

  ![create database-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenové úrovně pro vaši databázi najdete v tématu [nákupní model založený na DTU](service-tiers-dtu.md) a [model nakupování na základě Vcore](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Správa existujícího serveru

Pokud chcete spravovat existující server, přejděte na server pomocí různých metod – například na stránce konkrétní databáze, na stránce **servery SQL** nebo na stránce **všechny prostředky** .

Pokud chcete spravovat existující databázi, přejděte na stránku **databáze SQL** a vyberte databázi, kterou chcete spravovat. Následující snímek obrazovky ukazuje, jak začít s nastavením brány firewall na úrovni serveru pro databázi ze stránky **Přehled** pro databázi.

   ![pravidlo brány firewall serveru](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Chcete-li nakonfigurovat vlastnosti výkonu pro databázi, přečtěte si nákupní model [založený na DTU](service-tiers-dtu.md) a [nákupní model založený na Vcore](service-tiers-vcore.md).
> [!TIP]
> Azure Portal rychlého startu najdete v tématu [Vytvoření databáze v SQL Database Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvářet a spravovat servery, databáze v jedné a sdružené službě a brány firewall na úrovni serveru s Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Ukázkové skripty PowerShellu najdete [v tématu použití PowerShellu k vytvoření databáze v SQL Database a konfiguraci pravidla brány firewall na úrovni serveru](scripts/create-and-configure-database-powershell.md) a [monitorování a škálování databáze v SQL Database pomocí PowerShellu](scripts/monitor-and-scale-database-powershell.md).

| Rutina | Popis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří databázi. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databázi do elastického fondu.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Vytvoří skupinu prostředků.|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Vytvoří server.|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Vrátí informace o serverech.|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Upraví vlastnosti serveru.|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Odebere server.|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru. |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Získá pravidla brány firewall pro server.|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Upraví pravidlo brány firewall na serveru.|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| New-AzSqlServerVirtualNetworkRule | Vytvoří [*pravidlo virtuální sítě*](vnet-service-endpoint-rule-overview.md)na základě podsítě, která je Virtual Networkm koncovým bodem služby. |

## <a name="the-azure-cli"></a>Azure CLI

Pokud chcete vytvořit a spravovat servery, databáze a brány firewall pomocí [Azure CLI](/cli/azure), použijte následující příkazy rozhraní příkazového [řádku Azure](/cli/azure/sql/db) . Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](../../cloud-shell/overview.md) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Informace o vytváření a správě elastických fondů najdete v tématu [elastické fondy](elastic-pool-overview.md).

> [!TIP]
> Rychlý Start Azure CLI najdete v tématu [vytvoření jednoho Azure SQL Database pomocí Azure CLI](az-cli-script-samples-content-guide.md). Ukázkové skripty Azure CLI najdete v tématu použití rozhraní příkazového [řádku k vytvoření databáze v Azure SQL Database a konfiguraci SQL Database pravidla brány firewall](scripts/create-and-configure-database-cli.md) a použití rozhraní příkazového řádku [k monitorování a škálování databáze v Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Vytvoří databázi.|
|[AZ SQL DB list](/cli/azure/sql/db#az-sql-db-list)|Zobrazí všechny databáze a datové sklady na serveru nebo všechny databáze v elastickém fondu.|
|[AZ SQL DB list-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Zobrazí seznam dostupných cílů služby a omezení úložiště.|
|[AZ SQL DB list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Vrátí využití databáze.|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Načte databázi nebo datový sklad.|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje databázi.|
|[AZ SQL DB DELETE](/cli/azure/sql/db#az-sql-db-delete)|Odebere databázi.|
|[az group create](/cli/azure/group#az-group-create)|Vytvoří skupinu prostředků.|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Vytvoří server.|
|[AZ SQL Server list](/cli/azure/sql/server#az-sql-server-list)|Seznam serverů|
|[AZ SQL Server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Vrátí využití serveru.|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Získá Server.|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Aktualizuje server.|
|[AZ SQL Server DELETE](/cli/azure/sql/server#az-sql-server-delete)|Odstraní server.|
|[AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Vytvoří pravidlo brány firewall serveru.|
|[AZ SQL Server Firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Vypíše pravidla brány firewall na serveru.|
|[AZ SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Zobrazuje podrobnosti pravidla brány firewall.|
|[AZ SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje pravidlo brány firewall.|
|[AZ SQL Server Firewall-Rule DELETE](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Odstraní pravidlo brány firewall.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Chcete-li vytvořit a spravovat servery, databáze a brány firewall pomocí jazyka Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakéhokoli jiného programu, který se může připojit k serveru v SQL Database a předat příkazy jazyka Transact-SQL. Informace o správě elastických fondů najdete v tématu [elastické fondy](elastic-pool-overview.md).

> [!TIP]
> Rychlý Start s SQL Server Management Studio v systému Microsoft Windows najdete v tématu [Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](connect-query-ssms.md). Rychlý Start s Visual Studio Code v systému macOS, Linux nebo Windows najdete v článku [Azure SQL Database: použití Visual Studio Code k připojení a dotazování dat](connect-query-vscode.md).
> [!IMPORTANT]
> Nemůžete vytvořit nebo odstranit server pomocí jazyka Transact-SQL.

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT DATABÁZI](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Vytvoří novou izolovanou databázi. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Upraví databázi nebo elastický fond. |
|[ZRUŠIT DATABÁZI](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edici edice (Service úrovně), cíl služby (cenová úroveň) a název elastického fondu, pokud existuje, pro Azure SQL Database nebo fond SQL Azure synapse Analytics. Pokud se přihlásíte k hlavní databázi na serveru v SQL Database, vrátí informace na všech databázích. Pro Azure synapse Analytics musíte být připojeni k hlavní databázi.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí CPU, vstupně-výstupní operace a spotřebu paměti pro databázi v Azure SQL Database. Jeden řádek existuje každých 15 sekund, a to i v případě, že v databázi neexistuje žádná aktivita.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí využití CPU a data úložiště pro databázi v Azure SQL Database. Data se shromažďují a agregují v intervalu pěti minut.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiku pro události připojení SQL Database a poskytuje přehled o úspěšnostech a selháních připojení databáze. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšná připojení Azure SQL Database, selhání připojení a zablokování. Tyto informace můžete použít ke sledování a řešení potíží s databázovou aktivitou pomocí SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro váš server. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru. Pravidlo brány firewall na úrovni serveru se dá vytvořit jenom pomocí jazyka Transact-SQL, až se první pravidlo brány firewall na úrovni serveru vytvoří uživatelem s oprávněními na úrovni Azure.|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni serveru přidružené k vaší databázi v Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Odebere z vašeho serveru nastavení brány firewall na úrovni serveru. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze pro vaši databázi v Azure SQL Database. Pravidla brány firewall databáze je možné nakonfigurovat pro hlavní databázi a pro uživatelské databáze na SQL Database. Pravidla brány firewall databáze jsou užitečná při používání uživatelů databáze s omezením. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni databáze přidružené k vaší databázi v Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni databáze z databáze. |

## <a name="rest-api"></a>REST API

Chcete-li vytvořit a spravovat servery, databáze a brány firewall, použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvořit nebo aktualizovat](/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje nový server.|
|[Servery – odstranit](/rest/api/sql/servers/delete)|Odstraní SQL Server.|
|[Servery – získat](/rest/api/sql/servers/get)|Načte server.|
|[Servery – seznam](/rest/api/sql/servers/list)|Vrátí seznam serverů v rámci předplatného.|
|[Servery – seznam podle skupiny prostředků](/rest/api/sql/servers/listbyresourcegroup)|Vrátí seznam serverů ve skupině prostředků.|
|[Servery – aktualizace](/rest/api/sql/servers/update)|Aktualizuje existující server.|
|[Databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje stávající databázi.|
|[Databáze – odstranění](/rest/api/sql/databases/delete)|Odstraní databázi.|
|[Databáze – získat](/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle serveru](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze – aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Pravidla brány firewall – vytvořit nebo aktualizovat](/rest/api/sql/firewallrules/createorupdate)|Vytvoří nebo aktualizuje pravidlo brány firewall.|
|[Pravidla brány firewall – odstranit](/rest/api/sql/firewallrules/delete)|Odstraní pravidlo brány firewall.|
|[Pravidla brány firewall – získat](/rest/api/sql/firewallrules/get)|Získá pravidlo brány firewall.|
|[Pravidla brány firewall – seznam podle serveru](/rest/api/sql/firewallrules/listbyserver)|Vrátí seznam pravidel brány firewall.|

## <a name="next-steps"></a>Další kroky

- Další informace o migraci databáze SQL Server do Azure najdete v tématu [migrace na Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](features-comparison.md).
 
