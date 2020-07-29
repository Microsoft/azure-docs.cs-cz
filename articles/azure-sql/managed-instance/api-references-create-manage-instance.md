---
title: Referenční informace k rozhraní API pro správu pro spravovanou instanci Azure SQL
description: Přečtěte si o vytváření a konfiguraci spravovaných instancí spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3f0eb2c1ecb26475b25cd5ca41c9c61d5e2b47a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695572"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Reference spravovaného rozhraní API pro spravovanou instanci Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravované instance spravované instance Azure SQL můžete vytvořit a nakonfigurovat pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, REST API a jazyka Transact-SQL. V tomto článku můžete najít přehled funkcí a rozhraní API, které můžete použít k vytvoření a konfiguraci spravovaných instancí.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: vytvoření spravované instance

Rychlý Start ukazuje, jak vytvořit spravovanou instanci, najdete v tématu [rychlý Start: vytvoření spravované instance](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: vytvoření a konfigurace spravovaných instancí

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRM jsou v podstatě identické.

Pokud chcete vytvořit a spravovat spravované instance pomocí Azure PowerShell, použijte následující rutiny PowerShellu. Pokud potřebujete nainstalovat nebo upgradovat PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Ukázkové skripty PowerShellu najdete v tématu [skript pro rychlý Start: vytvoření spravované instance pomocí knihovny prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Rutina | Description |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Vytvoří spravovanou instanci. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Vrátí informace o spravované instanci.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Nastaví vlastnosti pro spravovanou instanci.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Odebere spravovanou instanci.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Vytvoří databázi spravované instance SQL.|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Vrátí informace o databázi spravované instance SQL.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Odebere databázi spravované instance SQL.|
|[Obnovit – AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Obnoví databázi spravované instance SQL.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: vytvoření a konfigurace spravovaných instancí

Pokud chcete vytvořit a nakonfigurovat spravované instance pomocí [Azure CLI](/cli/azure), použijte následující [příkazy rozhraní příkazového řádku Azure pro spravovanou instanci SQL](/cli/azure/sql/mi). Pomocí [Azure Cloud Shell](/azure/cloud-shell/overview) spusťte rozhraní příkazového řádku v prohlížeči nebo ho [nainstalujte](/cli/azure/install-azure-cli) na MacOS, Linux nebo Windows.

> [!TIP]
> Rychlý Start Azure CLI najdete v tématu [práce se službou SQL Managed instance pomocí Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Rutina | Description |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Vytvoří spravovanou instanci.|
|[AZ SQL mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Zobrazí seznam dostupných spravovaných instancí.|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Získá podrobnosti o spravované instanci.|
|[AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje spravovanou instanci.|
|[AZ SQL mi DELETE](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Odebere spravovanou instanci.|
|[AZ SQL MIDB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Vytvoří spravovanou databázi.|
|[AZ SQL MIDB list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Zobrazí seznam dostupných spravovaných databází.|
|[AZ SQL MIDB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Obnoví spravovanou databázi.|
|[AZ SQL MIDB DELETE](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Odebere spravovanou databázi.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: vytvoření a konfigurace databází instancí

Chcete-li vytvořit a konfigurovat databáze instancí po vytvoření spravované instance, použijte následující příkazy T-SQL. Tyto příkazy můžete vydat pomocí Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)nebo jakéhokoli jiného programu, který se může připojit k serveru a předat příkazy jazyka Transact-SQL.

> [!TIP]
> V případě rychlých startů se dozvíte, jak nakonfigurovat a připojit se ke spravované instanci pomocí SQL Server Management Studio v Microsoft Windows najdete v tématu [rychlý Start: konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL](connect-vm-instance-configure.md) a [rychlé zprovoznění: Konfigurace připojení typu Point-to-site k spravované instanci Azure SQL z místního](point-to-site-p2s-configure.md)prostředí.

> [!IMPORTANT]
> Pomocí jazyka Transact-SQL nemůžete vytvořit nebo odstranit spravovanou instanci.

| Příkaz | Description |
| --- | --- |
|[VYTVOŘIT DATABÁZI](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Vytvoří novou instanci databáze ve spravované instanci SQL. Aby bylo možné vytvořit novou databázi, je nutné, abyste byli připojeni k hlavní databázi.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Upraví databázi instance ve spravované instanci SQL.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: vytváření a konfigurace spravovaných instancí

Pokud chcete vytvořit a nakonfigurovat spravované instance, použijte tyto požadavky REST API.

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
