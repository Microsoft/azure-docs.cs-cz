---
title: Správa elastických fondů
description: Vytvářejte a spravujte Azure SQL Database elastické fondy pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, jazyka Transact-SQL (T-SQL) a rozhraní REST API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: dc2bb24880b77eae24e9bb2ef0baf70ac0b92ac7
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588628"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Správa elastických fondů v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

U elastického fondu určíte množství prostředků, které elastický fond vyžaduje pro zpracování úloh svých databází, a množství prostředků pro každou databázi ve fondu.

## <a name="azure-portal"></a>portál Azure

Všechna nastavení fondu se dají najít na jednom místě: okno **Konfigurovat fond** . Pokud se chcete dostat sem, najděte v Azure Portal elastický fond a klikněte v horní části okna na možnost **Konfigurovat fond** nebo v nabídce prostředky na levé straně.

Tady můžete udělat libovolnou kombinaci následujících změn a všechny je Uložit do jedné dávky:

1. Změna úrovně služby fondu
2. Škálování výkonu (DTU nebo virtuální jádra) a úložiště směrem nahoru nebo dolů
3. Přidání nebo odebrání databází do/z fondu
4. Nastavení minimálního (zaručeného) a maximálního limitu výkonu pro databáze ve fondech
5. Pokud si chcete zobrazit všechny změny ve vyúčtování v důsledku nových výběrů, Projděte si souhrn nákladů.

![Okno Konfigurace elastického fondu](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvořit a spravovat SQL Database elastické fondy a databáze ve fondu Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Informace o vytváření a správě serverů pro elastický fond najdete v tématu [Vytvoření a Správa serverů](logical-servers.md). Pokud chcete vytvořit a spravovat pravidla brány firewall, přečtěte si téma [Vytvoření a Správa pravidel brány firewall pomocí PowerShellu](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Ukázkové skripty PowerShellu najdete v tématech [vytváření elastických fondů a přesun databází mezi fondy a ven z fondu pomocí prostředí PowerShell](scripts/move-database-between-elastic-pools-powershell.md) a [použití PowerShellu k monitorování a škálování elastického fondu SQL v Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Vytvoří elastický fond.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Získá elastické fondy a jejich hodnoty vlastností.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Upraví vlastnosti elastického fondu. můžete například použít vlastnost **StorageMB** pro úpravu maximálního úložiště elastického fondu.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Odstraní elastický fond.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Získá stav operací v elastickém fondu.|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří novou databázi v existujícím fondu nebo jako samostatnou databázi. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti pro databázi nebo přesune existující databázi do, z nebo mezi elastickými fondy.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|

> [!TIP]
> Vytváření mnoha databází v elastickém fondu může trvat čas i v případě, že se použije portál nebo rutiny PowerShellu, které vytvoří pouze izolovanou databázi najednou. Informace o automatizaci vytváření do elastického fondu najdete v tématu [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Pokud chcete vytvořit a spravovat SQL Database elastické fondy pomocí [Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Database](/cli/azure/sql/db) . Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](../../cloud-shell/overview.md) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Ukázkové skripty Azure CLI najdete v tématu [použití CLI k přesunutí databáze v SQL Database v elastickém fondu SQL](scripts/move-database-between-elastic-pools-cli.md) a [použití rozhraní příkazového řádku Azure pro škálování elastického fondu SQL v Azure SQL Database](scripts/scale-pool-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[AZ SQL elastický fond Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Vytvoří elastický fond.|
|[AZ SQL elastický seznam fondů](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Vrátí seznam elastických fondů na serveru.|
|[AZ SQL elastický fond list-databáze](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Vrátí seznam databází v elastickém fondu.|
|[AZ SQL elastický seznam fondů – edice](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Zahrnuje taky dostupná nastavení DTU fondu, omezení úložiště a nastavení databáze. Aby se snížila úroveň podrobností, jsou ve výchozím nastavení skryté další limity úložiště a nastavení databáze.|
|[AZ SQL elastický fond Update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje elastický fond.|
|[AZ SQL elastický fond DELETE](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Odstraní elastický fond.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Chcete-li vytvořit a přesunout databáze v rámci stávajících elastických fondů nebo vrátit informace o SQL Database elastickém fondu pomocí jazyka Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakéhokoli jiného programu, který se může připojit k serveru a předat příkazy jazyka Transact-SQL. Chcete-li vytvořit a spravovat pravidla brány firewall pomocí T-SQL, přečtěte si téma [Správa pravidel brány firewall pomocí jazyka Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Nemůžete vytvořit, aktualizovat nebo odstranit Azure SQL Database elastický fond pomocí jazyka Transact-SQL. Můžete přidat nebo odebrat databáze z elastického fondu a pomocí zobrazení dynamické správy vracet informace o existujících elastických fondech.
>

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT databázi (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi v existujícím fondu nebo jako samostatnou databázi. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přesun databáze do, z nebo mezi elastickými fondy.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Vrátí statistiku využití prostředků pro všechny elastické fondy na serveru. Pro každý elastický fond je k dispozici jeden řádek pro každé 15 sekundové okno pro vytváření sestav (čtyři řádky za minutu). To zahrnuje využití procesoru, vstupně-výstupních operací, protokolu, spotřeby úložiště a souběžného využívání požadavků a relací všemi databázemi ve fondu.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edici edice (Service úrovně), cíl služby (cenová úroveň) a název elastického fondu, pokud existuje, pro databázi v SQL Database nebo Azure synapse Analytics. Pokud se přihlásíte k hlavní databázi na serveru, vrátí informace na všech databázích. Pro Azure synapse Analytics musíte být připojeni k hlavní databázi.|

## <a name="rest-api"></a>Rozhraní REST API

Pokud chcete vytvořit a spravovat SQL Database elastické fondy a databáze ve fondu, použijte tyto žádosti o REST API.

| Příkaz | Popis |
| --- | --- |
|[Elastické fondy – vytvořit nebo aktualizovat](/rest/api/sql/elasticpools/createorupdate)|Vytvoří nový elastický fond nebo aktualizuje existující elastický fond.|
|[Elastické fondy – odstranění](/rest/api/sql/elasticpools/delete)|Odstraní elastický fond.|
|[Elastické fondy – získat](/rest/api/sql/elasticpools/get)|Získá elastický fond.|
|[Elastické fondy – seznam podle serveru](/rest/api/sql/elasticpools/listbyserver)|Vrátí seznam elastických fondů na serveru.|
|[Elastické fondy – aktualizace] (/rest/api/sql/2020-11-01-preview/elasticpools/update
)|Aktualizuje existující elastický fond.|
|[Aktivity elastického fondu](/rest/api/sql/elasticpoolactivities)|Vrátí aktivity elastického fondu.|
|[Aktivity databáze elastického fondu](/rest/api/sql/elasticpooldatabaseactivities)|Vrátí aktivitu v databázích uvnitř elastického fondu.|
|[Databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje stávající databázi.|
|[Databáze – získat](/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle serveru](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze – aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|

## <a name="next-steps"></a>Další kroky

* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](saas-tenancy-app-design-patterns.md).
* Kurz SaaS použití elastických fondů najdete v tématu [Úvod do aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).
