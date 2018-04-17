---
title: Vytvořit a spravovat servery Azure SQL & databáze | Microsoft Docs
description: Informace o serveru Azure SQL Database a databázových koncepcí a o vytváření a správě serverů a databází.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: carlrab
ms.openlocfilehash: 0466b0e911736d2e1e7fc50649feda932c3163e5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Vytvářet a spravovat servery Azure SQL Database a databáze

SQL Database nabízí tři typy databází:

- Vytvořit v rámci jedné databáze [skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) s definovanou sadu [výpočetní a úložnou kapacitu pro různé úlohy](sql-database-service-tiers.md). Azure SQL database je přidružen logického serveru Azure SQL Database, která je vytvořena v rámci konkrétní oblasti Azure.
- Databáze vytvořené jako součást [fond databází](sql-database-elastic-pool.md) v rámci [skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) s definovanou sadu [výpočetní a úložnou kapacitu pro různé úlohy](sql-database-service-tiers.md) , které jsou sdíleno mezi všechny databáze ve fondu. Azure SQL database je přidružen logického serveru Azure SQL Database, která je vytvořena v rámci konkrétní oblasti Azure.
- [Instance systému SQL server](sql-database-managed-instance.md) (spravované instanci) vytvořen v rámci [skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) s definovanou sadu výpočetní a úložnou kapacitu pro všechny databáze v instanci serveru. Spravované instance obsahuje systém a uživatele databáze. Spravované Instance vytvořené za účelem povolení databáze navýšení a posunu plně spravovaná PaaS, bez přepracování aplikace. Spravované Instance poskytuje vysokou kompatibilitu s programovací model místní systém SQL Server a podporuje velká většina funkcí systému SQL Server a doprovodné nástroje a služby.  

Microsoft Azure SQL Database podporuje tabular data stream (TDS) protokol klienta verze 7.3 nebo novější a umožňuje šifrované připojení TCP/IP.

> [!IMPORTANT]
> Instance databáze serveru SQL spravované, aktuálně ve verzi public preview, nabízí jedné vrstvy služby obecné účely. Další informace najdete v tématu [SQL Database Managed Instance](sql-database-managed-instance.md). Zbývající část tohoto článku se nevztahuje na spravované Instance.

## <a name="what-is-an-azure-sql-logical-server"></a>Co je logickému serveru Azure SQL?

Logický server funguje jako centrální administrativní bod pro více jedním nebo [ve fondu](sql-database-elastic-pool.md) databází, [přihlášení](sql-database-manage-logins.md), [pravidla brány firewall](sql-database-firewall-configure.md), [auditování pravidla](sql-database-auditing.md), [hrozby zásady detekce](sql-database-threat-detection.md), a [převzetí služeb při selhání skupiny](sql-database-geo-replication-overview.md). Logický server může být v jiné oblasti než jeho skupin prostředků. Logický server musí existovat, abyste mohli vytvořit databázi Azure SQL. Všechny databáze na serveru jsou vytvořeny v rámci stejné oblasti jako logického serveru.

Logický server je logická konstrukce, která se liší od instance systému SQL Server, který může na světě místní být obeznámeni s. Služba SQL Database zejména neposkytuje žádnou záruku ohledně umístění databází ve vztahu k jejich logickým serverům a nezveřejňuje žádné funkce ani možnosti přístupu na úrovni instance. Na server v instanci SQL databáze spravované spočívá v tom, podobně jako instanci systému SQL Server, který může na světě místní být obeznámeni s.

Když vytvoříte logického serveru, zadat server přihlašovací účet a heslo, které má oprávnění správce pro hlavní databázi na tomto serveru a všechny databáze na tomto serveru vytvořit. Tento počáteční účet je účet SQL přihlášení. Azure SQL Database podporuje ověřování SQL a ověřování Azure Active Directory pro ověřování. Informace o ověřování a přihlášení najdete v tématu [Správa databází a přihlašovacích údajů ve službě Azure SQL Database](sql-database-manage-logins.md). Ověřování systému Windows se nepodporuje. 

> [!TIP]
> Názvy platný prostředků skupiny a serveru, najdete v části [pojmenování pravidla a omezení](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Logický server Azure Database:

- Je vytvořen v rámci předplatného Azure, ale je možné ho i s obsaženými prostředky přenést do jiného předplatného.
- Je nadřazeným prostředkem pro databáze, elastické fondy a datové sklady.
- Poskytuje obor názvů pro databáze elastické fondy a datových skladů
- Je logický kontejner s silné životnost sémantiku - odstranění serveru a odstraní databázích s omezením, elastické fondy a datových skladů
- Účastní se [řízení přístupu Azure na základě rolí (RBAC)](/azure/role-based-access-control/overview) -databází, elastické fondy a datových skladů v rámci serveru zdědí oprávnění ze serveru
- Je element horní identity databází, elastické fondy a datových skladů pro prostředků Azure pro účely správy (viz schéma adresy URL pro databáze a fondy)
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k databázi (<serverName>.database.windows.net).
- Poskytuje přístup k metadatům, která se vztahují k obsaženým prostředkům, přes zobrazení dynamických zpráv díky připojení k hlavní databázi. 
- Poskytuje oboru pro zásady správy, které platí pro její databáze - přihlášení, brány firewall, audit, hrozby detekce atd. 
- Je omezené na základě kvótu v rámci nadřazeného předplatného (šesti serverů na jedno předplatné, ve výchozím nastavení - [najdete v části předplatné omezuje zde](../azure-subscription-service-limits.md))
- Poskytuje oboru pro DTU nebo vCore kvót a kvóty databáze pro prostředky, které obsahuje (jako je například 45 000 DTU)
- Je v rozsahu správy verzí pro možnosti zapnuta obsažených prostředků 
- Hlavní přihlášení na úrovni serveru můžou spravovat všechny databáze na serveru.
- Může obsahovat přihlašovací údaje podobné těm, která se místně používají v instancích SQL Serveru, a udělit jim přístup k jedné nebo několika databázím na serveru spolu s omezenými právy pro správu. Další informace najdete v tématu [Přihlašovací údaje](sql-database-manage-logins.md).
- Výchozí kolaci pro všechny uživatele databáze vytvořené na logickém serveru je `SQL_LATIN1_GENERAL_CP1_CI_AS`, kde `LATIN1_GENERAL` je angličtina (Spojené státy), `CP1` je znaková stránka 1252, `CI` nerozlišuje, a `AS` je diakritikou.

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Chráněné bránou firewall databáze SQL Azure databáze SQL

K ochraně vašich dat [brány firewall SQL Database](sql-database-firewall-configure.md) zabraňuje veškerý přístup k databázovému serveru ani žádnou její databáze z mimo připojení k serveru přímo prostřednictvím připojení k předplatnému Azure. Chcete-li povolit další připojení, je potřeba [vytvořit jeden nebo více pravidel brány firewall](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Vytváření a správa elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí portálu Azure

Můžete vytvořit skupinu prostředků Azure SQL database předem nebo při vytváření samotný server. Existuje více metod pro získání nového formuláře SQL serveru tak, že vytvoříte nový server SQL nebo jako součást vytvoření nové databáze. 

### <a name="create-a-blank-sql-server-logical-server"></a>Vytvořit prázdný SQL server (logický server)

K vytvoření serveru (bez databáze) Azure SQL Database pomocí [portál Azure](https://portal.azure.com), přejděte do prázdného formuláře SQL server (logický server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Vytvoření databáze SQL prázdné nebo ukázku

Chcete-li vytvořit databázi Azure SQL pomocí [portál Azure](https://portal.azure.com), přejděte do prázdného formuláře databáze SQL a zadejte požadované informace. Můžete vytvořit skupinu prostředků a logický server předem nebo při vytváření samotná databáze Azure SQL database. Můžete vytvořit prázdnou databázi nebo vytvoření ukázkové databáze založené na společnosti Adventure Works LT. 

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenovou úroveň pro vaši databázi najdete v tématu [úrovních služeb](sql-database-service-tiers.md).

K vytvoření Instance spravované, najdete v části [vytvořit instanci spravované](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>Spravovat existující server SQL

Chcete-li spravovat existující server, přejděte na server s počtem metody – například od konkrétní stránky databáze SQL, **servery SQL** stránky, nebo **všechny prostředky** stránky. 

Chcete-li spravovat stávající databázi, přejděte na **databází SQL** a klikněte na tlačítko databáze, které chcete spravovat. Následující snímek obrazovky ukazuje, jak začít, nastavení brány firewall úrovni serveru pro databázi z **přehled** stránky pro databázi. 

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Konfigurace vlastnosti výkonu pro databázi najdete v tématu [úrovních služeb](sql-database-service-tiers.md).
>

> [!TIP]
> Azure portálu rychlý úvodní kurz, najdete v části [vytvoření Azure SQL database na portálu Azure](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí prostředí PowerShell

Chcete-li vytvořit a spravovat server Azure SQL, databáze a brány firewall pomocí prostředí Azure PowerShell, použijte následující rutiny prostředí PowerShell. Pokud je potřeba nainstalovat nebo upgradovat prostředí PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). Vytváření a správa elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

| Rutina | Popis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Vytvoří databázi |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Získá jednu nebo více databází|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databáze do pružného fondu|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Odebere databáze|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Vytvoří skupinu prostředků.]
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Vytvoří serveru|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Vrátí informace o serverech|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Upraví vlastnosti serveru|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Odebere server|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Získá pravidla brány firewall pro server|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Upraví pravidlo brány firewall na serveru|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| New-AzureRmSqlServerVirtualNetworkRule | Vytvoří [ *pravidlo virtuální sítě*](sql-database-vnet-service-endpoint-rule-overview.md)založená na podsíť, která je koncový bod služby virtuální sítě. |

> [!TIP]
> Rychlý úvodní kurz prostředí PowerShell, najdete v části [vytvářet izolované databáze Azure SQL pomocí prostředí PowerShell](sql-database-get-started-portal.md). Příklad skriptů prostředí PowerShell, najdete v části [použít PowerShell k vytvoření jedné databáze Azure SQL a nakonfigurujte pravidlo brány firewall](scripts/sql-database-create-and-configure-database-powershell.md) a [sledování a škálování jeden SQL databáze pomocí prostředí PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí rozhraní příkazového řádku Azure

Vytvoření a Správa serveru Azure SQL, databáze a brány firewall se [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Vytváření a správa elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

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

> [!TIP]
> Rychlý úvodní kurz Azure CLI, najdete v části [vytvářet izolované databáze Azure SQL pomocí rozhraní příkazového řádku Azure](sql-database-get-started-cli.md). Příklad skriptů příkazového řádku Azure CLI, najdete v části [použití rozhraní příkazového řádku k vytvoření jedné databáze Azure SQL a nakonfigurujte pravidlo brány firewall](scripts/sql-database-create-and-configure-database-cli.md) a [použití rozhraní příkazového řádku pro sledování a škálování jedné databáze SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí jazyka Transact-SQL

Vytvoření a Správa serveru Azure SQL, databáze a brány firewall pomocí jazyka Transact-SQL, pomocí následujících příkazů T-SQL. Můžete použít tyto příkazy pomocí portálu Azure [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který se může připojit k serveru Azure SQL Database a předat Transact-SQL příkazy. Správa elastické fondy, najdete v části [elastické fondy](sql-database-elastic-pool.md).

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


> [!TIP]
> Rychlý úvodní kurz pomocí SQL Server Management Studio v systému Windows, najdete v části [Azure SQL Database: pomocí SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md). Rychlý úvodní kurz pomocí kódu v jazyce Visual Studio v systému macOS, Linux nebo Windows, najdete v části [Azure SQL Database: použití Visual Studio Code k připojení a dotazování dat](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Spravovat servery Azure SQL, databáze a brány firewall pomocí rozhraní REST API

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
