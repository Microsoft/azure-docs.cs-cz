---
title: Co je server v Azure SQL Database a Azure synapse Analytics?
titleSuffix: ''
description: Přečtěte si o logických serverech SQL používaných Azure SQL Database a Azure synapse Analytics a o tom, jak je spravovat.
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
ms.openlocfilehash: 38133a63d65e45a4d1c83e9752dcaa01a86da33e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782888"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Co je logický SQL Server v Azure SQL Database a Azure synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

V Azure SQL Database a Azure synapse Analytics je server logická konstrukce, která funguje jako centrální bod správy pro kolekci databází. Na úrovni serveru můžete spravovat [přihlašovací údaje](logins-create-manage.md), [pravidla brány firewall](firewall-configure.md), [pravidla auditování](../../azure-sql/database/auditing-overview.md), [zásady detekce hrozeb](threat-detection-configure.md)a [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md). Server může být v jiné oblasti než skupina prostředků. Aby bylo možné vytvořit databázi v Azure SQL Database nebo v databázi datového skladu ve službě Azure synapse Analytics, musí server existovat. Všechny databáze spravované jedním serverem se vytvářejí v rámci stejné oblasti jako server.

Tento server se liší od instance SQL Server, kterou můžete znát v místním světě. Konkrétně neexistují žádné záruky týkající se umístění databází nebo databáze datového skladu ve vztahu k serveru, který je spravuje. Kromě toho ani Azure SQL Database ani Azure synapse nevystavují žádné funkce nebo funkce přístupu na úrovni instance. Naproti tomu jsou databáze instancí ve spravované instanci fyzicky společně se stejným způsobem, jakým jste obeznámeni s SQL Server v místním prostředí nebo na světě virtuálních počítačů.

Když vytváříte server, zadáváte přihlašovací účet serveru a heslo, které má práva správce k hlavní databázi na daném serveru a všechny databáze vytvořené na tomto serveru. Tento počáteční účet je přihlašovací účet SQL. Azure SQL Database a synapse Analytics podporují ověřování SQL a Azure Active Directory ověřování pro ověřování. Informace o přihlašovacích údajích a ověřování najdete [v tématu Správa databází a přihlášení v Azure SQL Database](logins-create-manage.md). Ověřování systému Windows se nepodporuje.

Server v SQL Database a Azure synapse:

- Je vytvořen v rámci předplatného Azure, ale je možné ho i s obsaženými prostředky přenést do jiného předplatného.
- Je nadřazeným prostředkem pro databáze, elastické fondy a datové sklady.
- Poskytuje obor názvů pro databáze, elastické fondy a databázi datového skladu.
- Je logický kontejner se silnými sémantikou životního cyklu – odstraní Server a odstraní jeho databáze, elastické fondy a SQK fondy.
- Podílí se na [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) – databáze, elastické fondy a databáze datového skladu na serveru dědí přístupová práva ze serveru.
- Je vysokým prvkem identity databází, elastických fondů a databází datového skladu pro účely správy prostředků Azure (viz schéma adresy URL pro databáze a fondy).
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k databázi (`<serverName>`.database.windows.net).
- Poskytuje přístup k metadatům, která se vztahují k obsaženým prostředkům, přes zobrazení dynamických zpráv díky připojení k hlavní databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze – přihlášení, bránu firewall, audit, detekci hrozeb a jako
- Je omezená kvótou v rámci nadřazeného předplatného (šest serverů na předplatné ve výchozím nastavení – [tady se zobrazí omezení předplatného](../../azure-resource-manager/management/azure-subscription-service-limits.md)).
- Poskytuje rozsah pro kvótu databáze a DTU nebo vCore kvótu pro prostředky, které obsahuje (například 45 000 DTU).
- Je obor správy verzí pro funkce povolené u obsažených prostředků.
- Hlavní přihlášení na úrovni serveru můžou spravovat všechny databáze na serveru.
- Může obsahovat přihlašovací údaje podobné těm, které jsou v instancích SQL Server v místním prostředí, kterým je udělen přístup k jedné nebo více databázím na serveru a může jim být uděleno omezené oprávnění správce. Další informace najdete v tématu [Přihlašovací údaje](logins-create-manage.md).
- Výchozí kolace pro všechny databáze vytvořené na serveru je `SQL_LATIN1_GENERAL_CP1_CI_AS` , kde `LATIN1_GENERAL` English (USA) `CP1` je znaková stránka 1252, rozlišuje `CI` velká a malá písmena a `AS` je citlivá na znaky.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Správa serverů, databází a bran firewall pomocí Azure Portal

Skupinu prostředků pro server můžete vytvořit před časem nebo vytvořením samotného serveru. Existuje několik způsobů, jak získat nový formulář SQL serveru, a to buď vytvořením nového SQL serveru, nebo v rámci vytváření nové databáze.

### <a name="create-a-blank-server"></a>Vytvoření prázdného serveru

Pokud chcete vytvořit server (bez databáze, elastického fondu nebo databáze datového skladu) pomocí [Azure Portal](https://portal.azure.com), přejděte na prázdný formulář SQL serveru (logický SQL Server).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Vytvoření prázdné nebo ukázkové databáze v Azure SQL Database

Chcete-li vytvořit databázi v SQL Database pomocí [Azure Portal](https://portal.azure.com)přejděte do prázdného formuláře SQL Database a zadejte požadované informace. Můžete vytvořit skupinu prostředků a Server před časem nebo při vytváření samotné databáze. Můžete vytvořit prázdnou databázi nebo vytvořit ukázkovou databázi založenou na Adventure Works LT.

  ![create database-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Informace o výběru cenové úrovně pro vaši databázi najdete v tématu [nákupní model založený na DTU](service-tiers-dtu.md) a [model nakupování na základě Vcore](service-tiers-vcore.md).

Informace o vytvoření spravované instance najdete v tématu [Vytvoření spravované instance](../managed-instance/instance-create-quickstart.md) .

### <a name="manage-an-existing-server"></a>Správa existujícího serveru

Pokud chcete spravovat existující server, přejděte na server pomocí různých metod – například z konkrétní stránky databáze, ze stránky **servery SQL** nebo ze stránky **všechny prostředky** .

Pokud chcete spravovat existující databázi, přejděte na stránku **databáze SQL** a klikněte na databázi, kterou chcete spravovat. Následující snímek obrazovky ukazuje, jak začít s nastavením brány firewall na úrovni serveru pro databázi ze stránky **Přehled** pro databázi.

   ![pravidlo brány firewall serveru](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Chcete-li nakonfigurovat vlastnosti výkonu pro databázi, přečtěte si nákupní model [založený na DTU](service-tiers-dtu.md) a [nákupní model založený na Vcore](service-tiers-vcore.md).
> [!TIP]
> Azure Portal rychlého startu najdete v tématu [Vytvoření databáze v SQL Database Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Správa serverů, databází a bran firewall pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvářet a spravovat servery, databáze a brány firewall pomocí Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Informace o vytváření a správě elastických fondů najdete v tématu [elastické fondy](elastic-pool-overview.md).

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

> [!TIP]
> Informace o rychlém startu PowerShellu najdete [v tématu Vytvoření databáze v Azure SQL Database pomocí prostředí PowerShell](single-database-create-quickstart.md). Ukázkové skripty PowerShellu najdete [v tématu použití PowerShellu k vytvoření databáze v Azure SQL Database a konfiguraci pravidla brány firewall](scripts/create-and-configure-database-powershell.md) a [monitorování a škálování databáze v Azure SQL Database pomocí prostředí PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Správa serverů, databází a bran firewall pomocí Azure CLI

Pokud chcete vytvořit a spravovat servery, databáze a brány firewall pomocí [Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Database](/cli/azure/sql/db) . Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](../../cloud-shell/overview.md) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. Informace o vytváření a správě elastických fondů najdete v tématu [elastické fondy](elastic-pool-overview.md).

| Rutina | Popis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Vytvoří databázi.|
|[AZ SQL DB list](/cli/azure/sql/db#az-sql-db-list)|Zobrazí všechny databáze spravované serverem nebo všechny databáze v elastickém fondu.|
|[AZ SQL DB list-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Zobrazí seznam dostupných cílů služby a omezení úložiště.|
|[AZ SQL DB list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Vrátí využití databáze.|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Získá databázi.
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
> Rychlý Start Azure CLI najdete v tématu [Vytvoření databáze v Azure SQL Database pomocí Azure CLI](az-cli-script-samples-content-guide.md). Ukázkové skripty Azure CLI najdete v tématu [použití rozhraní příkazového řádku k vytvoření databáze v Azure SQL Database a konfiguraci pravidla brány firewall](scripts/create-and-configure-database-cli.md) a [použití rozhraní příkazového řádku k monitorování a škálování databáze v Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Správa serverů, databází a bran firewall pomocí jazyka Transact-SQL

Chcete-li vytvořit a spravovat servery, databáze a brány firewall pomocí jazyka Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakéhokoli jiného programu, který se může připojit k serveru a předat příkazy jazyka Transact-SQL. Informace o správě elastických fondů najdete v tématu [elastické fondy](elastic-pool-overview.md).

> [!IMPORTANT]
> Nemůžete vytvořit nebo odstranit server pomocí jazyka Transact-SQL.

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT databázi (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | Vytvoří novou databázi v Azure SQL Database. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
|[Vytvoření databáze (Azure synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | Vytvoří novou databázi datového skladu v Azure synapse. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Upraví databázi nebo elastický fond. |
|[ALTER DATABASE (Azure synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|Upraví databázi datového skladu v Azure synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí hodnotu edice (úroveň služby), cíl služby (cenová úroveň) a název elastického fondu, pokud existuje, pro databázi. Pokud se přihlásíte k hlavní databázi serveru, vrátí informace na všech databázích. V případě Azure synapse musíte být připojeni k hlavní databázi.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Vrátí CPU, vstupně-výstupní operace a spotřebu paměti pro databázi v Azure SQL Database. Jeden řádek existuje každých 15 sekund, a to i v případě, že databáze neobsahuje žádné aktivity.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Vrátí využití CPU a data úložiště pro databázi v Azure SQL Database. Data se shromažďují a agregují v intervalu pěti minut.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Obsahuje statistiku pro události připojení databáze pro Azure SQL Database a poskytuje přehled o úspěšných a neúspěšných připojeních k databázi. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Vrátí úspěšná Azure SQL Database databázová připojení, selhání připojení a zablokování pro Azure SQL Database. Tyto informace můžete použít ke sledování a řešení potíží s databázovou aktivitou.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje nastavení brány firewall na úrovni serveru pro váš server. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru. Pravidlo brány firewall na úrovni serveru se dá vytvořit jenom pomocí jazyka Transact-SQL, až se první pravidlo brány firewall na úrovni serveru vytvoří uživatelem s oprávněními na úrovni Azure.|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni serveru, které jsou přidruženy k serveru.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni serveru ze serveru. Tato uložená procedura je k dispozici pouze v hlavní databázi pro přihlášení hlavního objektu na úrovni serveru.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze pro databázi v Azure SQL Database. Pravidla brány firewall databáze je možné nakonfigurovat pro hlavní databázi a pro uživatelské databáze v SQL Database. Pravidla brány firewall databáze jsou užitečná při používání uživatelů databáze s omezením. Pravidla brány firewall databáze nejsou v Azure synapse podporovaná.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Vrátí informace o nastavení brány firewall na úrovni databáze pro databázi v Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Odebere nastavení brány firewall na úrovni databáze pro databázi, kterou máte v Azure SQL Database. |

> [!TIP]
> Rychlý Start s SQL Server Management Studio v systému Microsoft Windows najdete v tématu [Azure SQL Database: použití SQL Server Management Studio k připojení a dotazování dat](connect-query-ssms.md). Rychlý Start s Visual Studio Code v systému macOS, Linux nebo Windows najdete v článku [Azure SQL Database: použití Visual Studio Code k připojení a dotazování dat](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Správa serverů, databází a bran firewall pomocí REST API

Pokud chcete vytvářet a spravovat servery, databáze a brány firewall, použijte tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Servery – vytvořit nebo aktualizovat](/rest/api/sql/servers/createorupdate)|Vytvoří nebo aktualizuje nový server.|
|[Servery – odstranit](/rest/api/sql/servers/delete)|Odstraní server.|
|[Servery – získat](/rest/api/sql/servers/get)|Načte server.|
|[Servery – seznam](/rest/api/sql/servers/list)|Vrátí seznam serverů.|
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

- Další informace o migraci databáze SQL Server do Azure SQL Database najdete v tématu [migrace na Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](features-comparison.md).