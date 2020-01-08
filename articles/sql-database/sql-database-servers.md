---
title: Servery
description: Přečtěte si o Azure SQL Databasech serverech a jejich správě.
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
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647148"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL Database servery a jejich správa

## <a name="what-is-an-azure-sql-database-server"></a>Co je server Azure SQL Database

SQL Database Server je logická konstrukce, která funguje jako centrální bod správy pro více databází v jednom nebo ve [fondu](sql-database-elastic-pool.md) , [přihlášení](sql-database-manage-logins.md), [pravidla brány firewall](sql-database-firewall-configure.md), [pravidla auditování](sql-database-auditing.md), [zásady detekce hrozeb](sql-database-threat-detection.md)a [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) . SQL Database Server může být v jiné oblasti, než je skupina prostředků. Než budete moct vytvořit databázi SQL Azure, musí existovat SQL Database Server. Všechny databáze spravované serverem SQL Database jsou vytvořeny v rámci stejné oblasti jako SQL Database Server.

SQL Database Server se liší od instance SQL Server, kterou můžete znát v místním světě. Konkrétně služba SQL Database neposkytuje žádné záruky týkající se umístění databází ve vztahu k serveru SQL Database, který je spravuje, a zpřístupňuje žádné funkce ani funkce pro přístup na úrovni instance. Naproti tomu jsou databáze instancí ve spravované instanci společně se stejným způsobem, jakým jste obeznámeni s SQL Server v místním světě.

Když vytváříte SQL Database Server, poskytnete přihlašovací účet serveru a heslo, které má práva správce k hlavní databázi na daném serveru a všechny databáze vytvořené na tomto serveru. Tento počáteční účet je přihlašovací účet SQL. Azure SQL Database podporuje ověřování SQL a ověřování Azure Active Directory pro ověřování. Informace o přihlašovacích údajích a ověřování najdete [v tématu Správa databází a přihlášení v Azure SQL Database](sql-database-manage-logins.md). Ověřování systému Windows se nepodporuje.

Server SQL Database:

- Je vytvořen v rámci předplatného Azure, ale je možné ho i s obsaženými prostředky přenést do jiného předplatného.
- Je nadřazeným prostředkem pro databáze, elastické fondy a datové sklady.
- Poskytuje obor názvů pro databáze, elastické fondy a datové sklady.
- Je logický kontejner se silnými sémantikou životního cyklu – odstraní Server a odstraní databáze, elastické fondy a datové sklady, které obsahuje.
- Podílí se na [řízení přístupu na základě role (RBAC) v Azure](/azure/role-based-access-control/overview) – databáze, elastické fondy a datové sklady v rámci serveru dědí přístupová práva ze serveru.
- Je vysokým prvkem identity databází, elastických fondů a datových skladů pro účely správy prostředků Azure (viz schéma adresy URL pro databáze a fondy).
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k databázi (`<serverName>`.database.windows.net).
- Poskytuje přístup k metadatům, která se vztahují k obsaženým prostředkům, přes zobrazení dynamických zpráv díky připojení k hlavní databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze – přihlášení, bránu firewall, audit, detekci hrozeb a jako
- Je omezená kvótou v rámci nadřazeného předplatného (šest serverů na předplatné ve výchozím nastavení – [tady se zobrazí omezení předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md)).
- Poskytuje rozsah pro kvótu databáze a DTU nebo vCore kvótu pro prostředky, které obsahuje (například 45 000 DTU).
- Je obor správy verzí pro funkce povolené u obsažených prostředků.
- Hlavní přihlášení na úrovni serveru můžou spravovat všechny databáze na serveru.
- Může obsahovat přihlašovací údaje podobné těm, která se místně používají v instancích SQL Serveru, a udělit jim přístup k jedné nebo několika databázím na serveru spolu s omezenými právy pro správu. Další informace najdete v tématu [Přihlašovací údaje](sql-database-manage-logins.md).
- Výchozí kolace pro všechny databáze vytvořené na serveru SQL Database je `SQL_LATIN1_GENERAL_CP1_CI_AS`, kde `LATIN1_GENERAL` je English (USA), `CP1` je znaková stránka 1252, `CI` rozlišuje velká a malá písmena a `AS` je citlivý na akcent.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Správa serverů, databází a bran firewall Azure SQL pomocí Azure Portal

Skupinu prostředků Azure SQL Database můžete vytvořit před časem nebo vytvořením samotného serveru. Existuje několik způsobů, jak získat nový formulář SQL serveru, a to buď vytvořením nového SQL serveru, nebo v rámci vytváření nové databáze.

### <a name="create-a-blank-sql-database-server"></a>Vytvoření prázdného serveru SQL Database

Pokud chcete vytvořit Azure SQL Database Server (bez databáze) pomocí [Azure Portal](https://portal.azure.com), přejděte na prázdný formulář SQL serveru (logický Server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Vytvoření prázdné nebo ukázkové databáze SQL

Pokud chcete vytvořit databázi SQL Azure pomocí [Azure Portal](https://portal.azure.com), přejděte do prázdného formuláře SQL Database a zadejte požadované informace. Můžete vytvořit skupinu prostředků Azure SQL Database a SQL Database Server před časem nebo při vytváření samotné databáze. Můžete vytvořit prázdnou databázi nebo vytvořit ukázkovou databázi založenou na Adventure Works LT.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenové úrovně pro vaši databázi najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [model nakupování na základě Vcore](sql-database-service-tiers-vcore.md).

Informace o vytvoření spravované instance najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) .

### <a name="manage-an-existing-sql-server"></a>Správa existujícího serveru SQL

Pokud chcete spravovat existující server, přejděte na server pomocí různých metod – například z konkrétní stránky SQL Database, na stránce **servery SQL** nebo na stránku **všechny prostředky** .

Pokud chcete spravovat existující databázi, přejděte na stránku **databáze SQL** a klikněte na databázi, kterou chcete spravovat. Následující snímek obrazovky ukazuje, jak začít s nastavením brány firewall na úrovni serveru pro databázi ze stránky **Přehled** pro databázi.

   ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Chcete-li nakonfigurovat vlastnosti výkonu pro databázi, přečtěte si nákupní model [založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).
> [!TIP]
> Azure Portal rychlého startu najdete v tématu [Vytvoření databáze SQL Azure v Azure Portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Správa serverů, databází a bran firewall Azure SQL pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvořit a spravovat Azure SQL Server, databáze a brány firewall pomocí Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Informace o vytváření a správě elastických fondů najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

| Rutina | Popis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří databázi. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databázi do elastického fondu.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Vytvoří skupinu prostředků.|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Vytvoří server.|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Vrátí informace o serverech.|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Upraví vlastnosti serveru.|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Odebere server.|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Vytvoří pravidlo brány firewall na úrovni serveru. |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Získá pravidla brány firewall pro server.|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Upraví pravidlo brány firewall na serveru.|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Odstraní pravidlo brány firewall ze serveru.|
| New-AzSqlServerVirtualNetworkRule | Vytvoří [*pravidlo virtuální sítě*](sql-database-vnet-service-endpoint-rule-overview.md)na základě podsítě, která je Virtual Networkm koncovým bodem služby. |

> [!TIP]
> Informace o rychlém startu PowerShellu najdete v tématu [Vytvoření izolované databáze SQL Azure pomocí PowerShellu](sql-database-single-database-get-started.md). Ukázkové skripty PowerShellu najdete v tématu [použití PowerShellu k vytvoření jednoduché databáze SQL Azure a konfiguraci pravidla brány firewall](scripts/sql-database-create-and-configure-database-powershell.md) a [monitorování a ŠKÁLOVÁNÍ izolované databáze SQL Azure pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Správa serverů, databází a bran firewall Azure SQL pomocí rozhraní příkazového řádku Azure

Pokud chcete vytvořit a spravovat Azure SQL Server, databáze a brány firewall pomocí [Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Database](/cli/azure/sql/db) . Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Informace o vytváření a správě elastických fondů najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

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

> [!TIP]
> Rychlý Start Azure CLI najdete v tématu [Vytvoření izolované databáze SQL Azure pomocí Azure CLI](sql-database-cli-samples.md). Ukázkové skripty Azure CLI najdete v tématu [použití rozhraní příkazového řádku k vytvoření izolované databáze SQL Azure a konfiguraci pravidla brány firewall](scripts/sql-database-create-and-configure-database-cli.md) a použití rozhraní příkazového [řádku pro monitorování a škálování databáze SQL Azure s jedinou databází](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Správa serverů, databází a bran firewall Azure SQL pomocí jazyka Transact-SQL

Pokud chcete vytvořit a spravovat Azure SQL Server, databáze a brány firewall pomocí jazyka Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakéhokoli jiného programu, který se může připojit k serveru Azure SQL Database a předat příkazy jazyka Transact-SQL. Informace o správě elastických fondů najdete v tématu [elastické fondy](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nemůžete vytvořit nebo odstranit server pomocí jazyka Transact-SQL.
>

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT databázi (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Upraví databázi SQL Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Upraví Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edici (úroveň služby), cíl služby (cenovou úroveň) a název elastického fondu, pokud existuje, pro databázi SQL Azure nebo Azure SQL Data Warehouse. Pokud se přihlásíte k hlavní databázi na serveru Azure SQL Database, vrátí informace na všech databázích. Pro Azure SQL Data Warehouse musíte být připojeni k hlavní databázi.|
|[sys. dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí CPU, vstupně-výstupní operace a spotřebu paměti pro databázi Azure SQL Database. Jeden řádek existuje každých 15 sekund, a to i v případě, že databáze neobsahuje žádné aktivity.|
|[sys. resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí využití CPU a data úložiště pro Azure SQL Database. Data se shromažďují a agregují v intervalu pěti minut.|
|[sys. database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiku pro SQL Database události připojení databáze a poskytuje přehled o úspěšných a neúspěšných připojeních k databázi. |
|[sys. event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšná Azure SQL Database databázová připojení, selhání připojení a zablokování. Tyto informace můžete použít ke sledování a řešení potíží s databázovou aktivitou pomocí SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro server SQL Database. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru. Pravidlo brány firewall na úrovni serveru se dá vytvořit jenom pomocí jazyka Transact-SQL, až se první pravidlo brány firewall na úrovni serveru vytvoří uživatelem s oprávněními na úrovni Azure.|
|[sys. firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni serveru přidružených k vašemu Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni serveru ze serveru SQL Database. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze pro Azure SQL Database nebo SQL Data Warehouse. Pravidla brány firewall databáze je možné nakonfigurovat pro hlavní databázi a pro uživatelské databáze na SQL Database. Pravidla brány firewall databáze jsou užitečná při používání uživatelů databáze s omezením. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni databáze přidružených k vašemu Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni databáze z Azure SQL Database nebo SQL Data Warehouse. |

> [!TIP]
> Rychlý Start s SQL Server Management Studio v systému Microsoft Windows najdete v tématu [Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](sql-database-connect-query-ssms.md). Rychlý Start s Visual Studio Code v systému macOS, Linux nebo Windows najdete v článku [Azure SQL Database: použití Visual Studio Code k připojení a dotazování dat](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Správa serverů, databází a bran firewall Azure SQL pomocí REST API

Pokud chcete vytvořit a spravovat Azure SQL Server, databáze a brány firewall, použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje nový server.|
|[Servery – odstranit](https://docs.microsoft.com/rest/api/sql/servers/delete)|Odstraní SQL Server.|
|[Servery – získat](https://docs.microsoft.com/rest/api/sql/servers/get)|Načte server.|
|[Servery – seznam](https://docs.microsoft.com/rest/api/sql/servers/list)|Vrátí seznam serverů.|
|[Servery – seznam podle skupiny prostředků](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Vrátí seznam serverů ve skupině prostředků.|
|[Servery – aktualizace](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje existující server.|
|[Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje stávající databázi.|
|[Databáze – odstranění](https://docs.microsoft.com/rest/api/sql/databases/delete)|Odstraní databázi.|
|[Databáze – získat](https://docs.microsoft.com/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle serveru](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje existující databázi.|
|[Pravidla brány firewall – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Vytvoří nebo aktualizuje pravidlo brány firewall.|
|[Pravidla brány firewall – odstranit](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Odstraní pravidlo brány firewall.|
|[Pravidla brány firewall – získat](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Získá pravidlo brány firewall.|
|[Pravidla brány firewall – seznam podle serveru](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Vrátí seznam pravidel brány firewall.|

## <a name="next-steps"></a>Další kroky

- Další informace o migraci databáze SQL Server do Azure najdete v tématu [migrace na Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
