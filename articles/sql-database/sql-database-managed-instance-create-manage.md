---
title: Referenční dokumentace rozhraní API pro správu pro Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Další informace o vytváření a správa Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7fb6917e129c015536143a707fd2a89fc5423a99
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323076"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Spravovaná reference k rozhraní API pro Azure SQL Database Managed instance

Můžete vytvořit a spravovat Azure SQL Database Managed instance pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, rozhraní REST API a příkazů jazyka Transact-SQL. V tomto článku najdete přehled funkcí a rozhraní API, které můžete použít k vytvoření a konfigurace Managed Instance.

## <a name="azure-portal-create-a-managed-instance"></a>Azure portal: Vytvoření Managed Instance

Rychlý start ukazující vytvoření Azure SQL Database Managed Instance, naleznete v tématu [rychlý start: Vytvoření spravované Instance Azure SQL Database](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell: Vytvoření a správa Managed Instance

K vytváření a správě serveru Azure SQL, databáze a brány firewall pomocí Azure Powershellu, použijte následující rutiny Powershellu. Pokud potřebujete instalaci nebo upgrade prostředí PowerShell, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

> [!TIP]
> Příklady skriptů Powershellu, najdete v části [skript rychlý start: Vytvoření spravované Instance Azure SQL pomocí prostředí PowerShell knihovny](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Rutina | Popis |
| --- | --- |
|[Nové AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Vytvoří spravované Instance Azure SQL Database |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Vrátí informace o spravované Instance Azure SQL|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Nastaví vlastnosti pro Azure SQL Database Managed Instance|
|[Odebrat AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Odebere instanci spravované databáze Azure SQL|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: Správa logických serverů a databází

K vytváření a správě serveru Azure SQL, databáze a brány firewall s [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Rychlý start Azure CLI najdete v části [práce s SQL Managed Instance pomocí rozhraní příkazového řádku Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
>

| Rutina | Popis |
| --- | --- |
|[Vytvoření az sql mi](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |Vytvoří spravovanou instanci|
|[seznam mi az sql](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|Seznam dostupných spravovaných instancí|
|[Zobrazit mi az sql](/cli/azure/sql/db#az-sql-mi-show)|Získat podrobnosti pro Managed Instance|
|[aktualizace mi az sql](/cli/azure/sql/db#az-sql-mi-update)|Aktualizace spravované Instance|
|[Odstranit mi az sql](/cli/azure/sql/db#az-sql-mi-delete)|Odebere spravované Instance|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Příkaz Transact-SQL: Správa logických serverů a databází

Vytvoření a správa Azure SQL Database Managed Instance databáze po vytvoření Managed Instance, použijte následující příkazy T-SQL. Můžete použít tyto příkazy pomocí webu Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který můžete připojit k serveru Azure SQL Database a předat příkazů jazyka Transact-SQL.

> [!TIP]
> Rychlé starty budete muset nakonfigurovat a připojte se k Managed Instance pomocí SQL Server Management Studio na Microsoft Windows, naleznete v části [rychlý start: Konfigurace virtuálního počítače Azure pro připojení k Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) a [rychlý start: Konfigurace připojení typu point-to-site k Azure SQL Database Managed Instance z místní](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nejde vytvořit nebo odstranit Managed Instance pomocí příkazů jazyka Transact-SQL.

| Příkaz | Popis |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Vytvoří novou databázi Managed Instance. Musíte být připojení k hlavní databázi, a vytvořit novou databázi.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Upraví služby spravované Instance Azure SQL database.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>ROZHRANÍ REST API: Správa logických serverů a databází

Vytvoření a správa Azure SQL Database Managed Instance, použijte tyto požadavky rozhraní REST API.

| Příkaz | Popis |
| --- | --- |
|[Spravované instance - vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Vytvoří nebo aktualizuje Managed Instance.|
|[Spravované instance - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Odstraní spravované Instance.|
|[Spravované instance - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Načte spravované Instance.|
|[Spravované instance – seznam](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Vrátí seznam spravovaných instancí v rámci předplatného.|
|[Spravované instance – seznam podle skupin prostředků](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Vrátí seznam spravovaných instancí ve skupině prostředků.|
|[Spravovaná instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje Managed Instance.|

## <a name="next-steps"></a>Další postup

- Další informace o migraci databáze SQL serveru do Azure najdete v tématu [migrace do služby Azure SQL Database](sql-database-cloud-migrate.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](sql-database-features.md).
