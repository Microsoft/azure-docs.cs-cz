---
title: Správa elastických fondů
description: Vytvářejte a spravujte elastické fondy Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: d8dde76753e58c713763c16230e5461fef43be88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067335"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Správa elastických fondů v Azure SQL Database

S elastické fondu určit množství prostředků, které vyžaduje elastické fondu ke zpracování zatížení jeho databází a množství prostředků pro každou sdruženou databázi.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: Správa elastických fondů a sdružených databází

Všechna nastavení fondu lze nalézt na jednom místě: **Konfigurovat fond** okno. Sem se dostanete, najděte elastický fond na portálu a klikněte na **Konfigurovat fond** buď z horní části okna nebo z nabídky prostředků vlevo.

Zde můžete provést libovolnou kombinaci následujících změn a uložit je všechny do jedné dávky:

1. Změna úrovně služeb fondu
2. Škálování výkonu (DTU nebo virtuálních jader) a ukládání nahoru nebo dolů
3. Přidání nebo odebrání databází do fondu nebo z něj
4. Nastavení min (zaručeného) a maximálního výkonnostního limitu pro databáze v fondech
5. Kontrola souhrnu nákladů zobrazíte změny na vyúčtování v důsledku nových výběrů.

![Elastická konfigurační čepel bazénu](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>Prostředí PowerShell: Správa elastických fondů a sdružených databází

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvářet a spravovat elastické fondy sql database a sdružené databáze pomocí Azure PowerShellu, použijte následující rutiny Prostředí PowerShell. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps). Informace o vytvoření a správě serverů SQL Database pro elastický fond naleznete v [tématu Vytvoření a správa databázových serverů SQL](sql-database-servers.md). Informace o vytváření a správě pravidel brány firewall najdete v [tématu Vytváření a správa pravidel brány firewall pomocí prostředí PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Ukázkové skripty Prostředí PowerShell najdete v [tématu Vytváření elastických fondů a přesouvání databází mezi fondy a mimo fond pomocí PowerShellu](scripts/sql-database-move-database-between-pools-powershell.md) a [pomocí PowerShellu ke sledování a škálování elastického fondu SQL v Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool)|Vytvoří elastický fond.|
|[Elastický fond Get-AzSql](/powershell/module/az.sql/get-azsqlelasticpool)|Získá elastické fondy a jejich hodnoty vlastností.|
|[Elastický fond Set-AzSql](/powershell/module/az.sql/set-azsqlelasticpool)|Upravuje vlastnosti elastického fondu: Například pomocí vlastnosti **StorageMB** upravte maximální úložiště elastického fondu.|
|[Odstranit azsqlelasticpool](/powershell/module/az.sql/remove-azsqlelasticpool)|Odstraní elastický fond.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Získá stav operací v elastickém fondu|
|[Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase)|Vytvoří novou databázi v existujícím fondu nebo jako jednu databázi. |
|[Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase)|Nastaví vlastnosti databáze nebo přesune existující databázi do, z nebo mezi elastické fondy.|
|[Odebrat databázi AzSql](/powershell/module/az.sql/remove-azsqldatabase)|Odebere databázi.|

> [!TIP]
> Vytvoření mnoha databází v elastickém fondu může nějakou dobu trvat, když se provádí pomocí rutin portálu nebo prostředí PowerShell, které vytvářejí pouze jednu databázi najednou. Chcete-li automatizovat vytváření do elastického fondu, naleznete v tématu [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Správa elastických fondů a sdružených databází

Chcete-li vytvořit a spravovat elastické fondy databáze SQL pomocí [příkazového příkazu k příkazu Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Database](/cli/azure/sql/db) Database. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Pro ukázkové skripty Azure CLI najdete v článku [Použití příkazového příkazového příkazu k přesunutí databáze Azure SQL v elastickém fondu SQL](scripts/sql-database-move-database-between-pools-cli.md) a použití azure [CLI k škálování elastického fondu SQL v Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[az sql elastický fond vytvořit](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Vytvoří elastický fond.|
|[az sql seznam elastického fondu](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Vrátí seznam elastických fondů na serveru.|
|[az sql elastické-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Vrátí seznam databází v elastickém fondu.|
|[az sql elastické-pool list-edice](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Zahrnuje také dostupná nastavení dtu fondu, omezení úložiště a nastavení databáze. Aby se snížila podrobnost, další omezení úložiště a na nastavení databáze jsou ve výchozím nastavení skryté.|
|[aktualizace elastického fondu az SQL](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje elastický fond.|
|[az sql elastické fondu odstranit](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Odstraní elastický fond.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Správa sdružených databází

Chcete-li vytvořit a přesunout databáze v rámci existujících elastických fondů nebo vrátit informace o elastickém fondu databáze SQL s Transact-SQL, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí portálu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), Visual Studio [Code](https://code.visualstudio.com/docs)nebo jakékoli jiné aplikace, která se může připojit k serveru Azure SQL Database a předat příkazy Transact-SQL. Informace o vytváření a správě pravidel brány firewall pomocí t-SQL naleznete v [tématu Správa pravidel brány firewall pomocí aplikace Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Nelze vytvořit, aktualizovat nebo odstranit elastického fondu Azure SQL Database pomocí Transact-SQL. Můžete přidat nebo odebrat databáze z elastického fondu a můžete použít dmv s vracet informace o existujících elastických fondů.
>

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT DATABÁZI (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi v existujícím fondu nebo jako jednu databázi. Chcete-li vytvořit novou databázi, musíte být připojeni k hlavní databázi.|
| [DATABÁZE ALTER (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přesunutí databáze do elastických fondů, z těchto fondů nebo mezi nimi.|
|[DROP DATABÁZE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Vrátí statistiky využití prostředků pro všechny elastické fondy na databázovém serveru SQL. Pro každý elastický fond je jeden řádek pro každé okno hlášení 15 sekund (čtyři řádky za minutu). To zahrnuje procesor, vstupně-výkon, protokol, využití úložiště a souběžné využití požadavku/relace všemi databázemi ve fondu.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edici (úroveň služby), cíl služby (cenovou úroveň) a případný název elastického fondu pro databázi Azure SQL nebo datový sklad Azure SQL. Pokud jste přihlášeni k hlavní databázi na serveru Azure SQL Database, vrátí informace o všech databázích. Pro Azure SQL Data Warehouse musíte být připojeni k hlavní databázi.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>ROZHRANÍ REST API: Správa elastických fondů a sdružených databází

Chcete-li vytvořit a spravovat elastické fondy databáze SQL a sdružené databáze, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Elastické fondy – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Vytvoří nový elastický fond nebo aktualizuje existující elastický fond.|
|[Elastické fondy - Odstranit](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Odstraní elastický fond.|
|[Elastické bazény - Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Získá elastický fond.|
|[Elastické fondy – seznam podle serveru](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Vrátí seznam elastických fondů na serveru.|
|[Elastické fondy – aktualizace](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Aktualizuje existující elastický fond.|
|[Aktivity elastického fondu](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Vrátí aktivity elastického fondu.|
|[Aktivity databáze elastického fondu](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Vrátí aktivitu v databázích uvnitř elastického fondu.|
|[Databáze – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Získat](https://docs.microsoft.com/rest/api/sql/databases/get)|Získá databázi.|
|[Databáze – seznam podle elastického fondu](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze - Seznam podle serveru](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - Aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje existující databázi.|

## <a name="next-steps"></a>Další kroky

* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Kurz SaaS pomocí elastických fondů najdete [v tématu Úvod do aplikace Wingtip SaaS](sql-database-wtp-overview.md).
