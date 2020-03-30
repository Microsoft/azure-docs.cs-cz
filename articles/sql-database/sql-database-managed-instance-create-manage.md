---
title: Odkaz na rozhraní API pro správu
description: Přečtěte si o vytváření a správě spravovaných instancí Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268857"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Odkaz spravovaného rozhraní API pro spravované instance Azure SQL Database

Spravované instance Azure SQL Database můžete vytvářet a spravovat pomocí portálu Azure Portal, PowerShellu, rozhraní API Azure, rozhraní REST API a Transact-SQL. V tomto článku najdete přehled funkcí a rozhraní API, které můžete použít k vytvoření a konfiguraci spravované instance.

## <a name="azure-portal-create-a-managed-instance"></a>Portál Azure: Vytvoření spravované instance

Úvodní příručka, která vám ukáže, jak vytvořit spravovanou instanci Azure SQL Database, najdete v [tématu Úvodní příručka: Vytvoření spravované instance azure SQL database .](sql-database-managed-instance-get-started.md)

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Vytváření a správa spravovaných instancí

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvářet a spravovat spravované instance pomocí Azure PowerShellu, použijte následující rutiny Prostředí PowerShell. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Ukázkové skripty PowerShellu najdete [v tématu Skript rychlého spuštění: Vytvoření azure sql spravované instance pomocí knihovny PowerShellu](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Rutina | Popis |
| --- | --- |
|[Nová instance AzSql](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Vytvoří spravovanou instanci Azure SQL Database |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Vrátí informace o spravované instanci Azure SQL.|
|[Instance Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Nastaví vlastnosti spravované instance Azure SQL Database|
|[Odebrat-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Odebere instanci spravované databáze Azure SQL.|
|[Nová databáze azráži](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Vytvoří databázi spravovaných instancí Azure SQL Database.|
|[Databáze get-azsqlinstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Vrátí informace o databázi spravované instance Azure SQL.|
|[Odebrat databázi instance AzSql](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Odebere databázi instance spravované databáze Azure SQL.|
|[Obnovit databázi instance AzSql](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Obnoví databázi instance spravované databáze Azure SQL.|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Vytváření a správa spravovaných instancí

Chcete-li vytvářet a spravovat spravované instance pomocí [příkazového příkazu Azure CLI](/cli/azure), použijte následující příkazy [spravované instance Azure CLI SQL.](/cli/azure/sql/mi) Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Rychlý start příkazového příkazového odložitého jsme si poúvodním základě azure cli najdete [v tématu Práce se spravovanou instancí SQL pomocí azure cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Rutina | Popis |
| --- | --- |
|[az sql mi vytvořit](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Vytvoří spravovanou instanci.|
|[az sql mi seznam](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Seznamy dostupných spravovaných instancí|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Získání podrobností o spravované instanci|
|[az sql mi aktualizace](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje spravovanou instanci|
|[az sql mi odstranit](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Odebere spravovanou instanci.|
|[az sql midb vytvořit](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Vytvoří spravovanou databázi.|
|[az sql midb seznam](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Seznam dostupných spravovaných databází|
|[az sql midb obnovení](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Obnovení spravované databáze|
|[az sql midb odstranit](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Odebere spravovanou databázi.|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Vytváření a správa databází instancí

Chcete-li vytvořit a spravovat databázi instancí po vytvoření spravované instance, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí portálu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), Azure Data [Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakýkoli jiný program, který se může připojit k serveru Azure SQL Database a předat příkazy Transact-SQL.

> [!TIP]
> Rychlé starty, které ukazují, že je třeba nakonfigurovat a připojit se ke spravované instanci pomocí sql serveru Management Studio v Microsoft Windows, najdete v [tématu Úvodní příručka: Konfigurace virtuálního počítače Azure pro připojení k spravované instanci Azure SQL Database a](sql-database-managed-instance-configure-vm.md) Úvodní [příručka: Konfigurace připojení z bodu na místo ke spravované instanci Azure SQL Database Managed Instance z místního prostředí](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Spravovanou instanci nelze vytvořit ani odstranit pomocí aplikace Transact-SQL.

| Příkaz | Popis |
| --- | --- |
|[VYTVOŘIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Vytvoří novou databázi spravovaných instancí. Chcete-li vytvořit novou databázi, musíte být připojeni k hlavní databázi.|
| [ZMĚNIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Upraví databázi spravované instance Azure SQL.|

## <a name="rest-api-create-and-manage-managed-instances"></a>ROZHRANÍ REST API: Vytváření a správa spravovaných instancí

Chcete-li vytvářet a spravovat spravované instance, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Spravované instance – vytvoření nebo aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Vytvoří nebo aktualizuje spravovanou instanci.|
|[Spravované instance – odstranit](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Odstraní spravovanou instanci.|
|[Spravované instance – získat](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Získá spravovanou instanci.|
|[Spravované instance – seznam](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Vrátí seznam spravovaných instancí v předplatném.|
|[Spravované instance – seznam podle skupiny prostředků](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Vrátí seznam spravovaných instancí ve skupině prostředků.|
|[Spravované instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje spravovanou instanci.|

## <a name="next-steps"></a>Další kroky

- Další informace o migraci databáze SQL Serveru do Azure najdete v tématu [Migrace do Azure SQL Database](sql-database-single-database-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
