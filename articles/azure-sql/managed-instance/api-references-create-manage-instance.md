---
title: Referenční dokumentace rozhraní API pro správu pro spravovanou instanci
description: Seznamte se s vytvářením a správou spravovaných instancí Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ca7e123a3a0a1707eb979f25cabfc5913c5bfcb8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046317"
---
# <a name="managed-api-reference-for-azure-sql-managed-instances"></a>Reference spravovaného rozhraní API pro spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravované instance Azure SQL můžete vytvořit a spravovat pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, REST API a jazyka Transact-SQL. V tomto článku můžete najít přehled funkcí a rozhraní API, které můžete použít k vytvoření a konfiguraci spravované instance.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: vytvoření spravované instance

Rychlý Start ukazuje, jak vytvořit spravovanou instanci SQL Database, najdete v tématu [rychlý Start: vytvoření spravované instance SQL](instance-create-quickstart.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: vytváření a Správa spravovaných instancí

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

Pokud chcete vytvořit a spravovat spravované instance pomocí Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Ukázkové skripty PowerShellu najdete v tématu [rychlý Start skriptu: vytvoření spravované instance SQL pomocí knihovny prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Rutina | Description |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Vytvoří spravovanou instanci Azure SQL. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Vrátí informace o spravované instanci Azure SQL.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Nastaví vlastnosti pro spravovanou instanci SQL Azure.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Odebere instanci spravované databáze SQL Azure.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Vytvoří databázi spravované instance Azure SQL.|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Vrátí informace o databázi Azure SQL Managed instance.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Odebere databázi instance spravované databáze SQL Azure.|
|[Obnovit – AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Obnoví databázi instance spravované databáze SQL Azure.|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: vytvoření a Správa spravovaných instancí

Pokud chcete vytvořit a spravovat spravované instance pomocí [Azure CLI](/cli/azure), použijte následující příkazy [Azure CLI SQL Managed instance](/cli/azure/sql/mi) . Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows.

> [!TIP]
> Rychlý Start Azure CLI najdete v tématu [práce se službou SQL Managed instance pomocí Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Rutina | Description |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Vytvoří spravovanou instanci.|
|[AZ SQL mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Zobrazí seznam dostupných spravovaných instancí.|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Získat podrobnosti o spravované instanci|
|[AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje spravovanou instanci.|
|[AZ SQL mi DELETE](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Odebere spravovanou instanci.|
|[AZ SQL MIDB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Vytvoří spravovanou databázi.|
|[AZ SQL MIDB list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Vypíše dostupné spravované databáze.|
|[AZ SQL MIDB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Obnovení spravované databáze|
|[AZ SQL MIDB DELETE](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Odebere spravovanou databázi.|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: vytváření a Správa databází instancí

Chcete-li vytvořit a spravovat databázi instancí po vytvoření spravované instance, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakýkoli jiný program, který se může připojit k serveru a předat příkazy jazyka Transact-SQL.

> [!TIP]
> Pro rychlé starty, které ukazují, že musíte nakonfigurovat a připojit se ke spravované instanci SQL pomocí SQL Server Management Studio v systému Microsoft Windows, přečtěte si téma [rychlý Start: konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL](connect-vm-instance-configure.md) a [rychlé zprovoznění: Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního](point-to-site-p2s-configure.md)prostředí.
> [!IMPORTANT]
> Pomocí jazyka Transact-SQL nemůžete vytvořit nebo odstranit spravovanou instanci.

| Příkaz | Description |
| --- | --- |
|[VYTVOŘIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Vytvoří novou databázi spravované instance. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Upraví databázi spravované instance Azure SQL.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: vytváření a Správa spravovaných instancí

Chcete-li vytvořit a spravovat spravované instance, použijte tyto požadavky REST API.

| Příkaz | Description |
| --- | --- |
|[Spravované instance – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Vytvoří nebo aktualizuje spravovanou instanci.|
|[Spravované instance – odstranění](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Odstraní spravovanou instanci.|
|[Spravované instance – získání](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Načte spravovanou instanci.|
|[Spravované instance – seznam](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Vrátí seznam spravovaných instancí v rámci předplatného.|
|[Spravované instance – seznam podle skupiny prostředků](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Vrátí seznam spravovaných instancí ve skupině prostředků.|
|[Spravované instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje spravovanou instanci.|

## <a name="next-steps"></a>Další kroky

- Další informace o migraci databáze SQL Server do Azure najdete v tématu [migrace na Azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Informace o podporovaných funkcích najdete v tématu [Funkce](../database/features-comparison.md).
