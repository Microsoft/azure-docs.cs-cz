---
title: 'Azure Resource Manager: vytvoření samostatné databáze'
description: Pomocí šablony Azure Resource Manager vytvořte v Azure SQL Database jednu databázi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: b7888215a2d463c8007168e215179ace898ca1cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821024"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Rychlý Start: vytvoření jedné databáze v Azure SQL Database pomocí šablony Azure Resource Manager

Vytvoření [jedné databáze](sql-database-single-database.md) je nejrychlejší a nejjednodušší možnost nasazení pro vytvoření databáze v Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit jedinou databázi pomocí šablony Azure Resource Manager. Další informace najdete v [dokumentaci Azure Resource Manager](/azure/azure-resource-manager/).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Jedna databáze má definovanou sadu výpočetních, paměťových, vstupně-výstupních prostředků a prostředků úložiště pomocí jednoho ze dvou [nákupních modelů](sql-database-purchase-models.md). Když vytvoříte izolovanou databázi, nadefinujete taky [SQL Database Server](sql-database-servers.md) , který budete spravovat a umístit do [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v zadané oblasti.

Následující soubor JSON je šablona použitá v tomto článku. Šablona je uložena na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Další ukázky šablon Azure SQL Database najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Vyberte příkaz **vyzkoušet** z následujícího bloku kódu prostředí PowerShell a otevřete Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Výběrem **Kopírovat** zkopírujte skript prostředí PowerShell do schránky.
1. Klikněte pravým tlačítkem na podokno prostředí a pak vyberte **Vložit**.

    Vytvoření databázového serveru a databáze chvíli trvá.

## <a name="query-the-database"></a>Dotazování databáze

Dotaz na databázi najdete v tématu [dotazování databáze](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), zachovejte tuto skupinu prostředků, databázový server a samostatnou databázi. Následující kroky ukazují, jak se připojit k databázi a dotazovat se na ni pomocí různých metod.

Odstranění skupiny prostředků pomocí Azure PowerShellu:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databázi z místních nebo vzdálených nástrojů. Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru se [připojte a Dotazujte](sql-database-connect-query.md) databázi pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pokud chcete vytvořit izolovanou databázi pomocí Azure CLI, přečtěte si [ukázky v Azure CLI](sql-database-cli-samples.md).
- Chcete-li vytvořit izolovanou databázi pomocí Azure PowerShell, přečtěte si téma [Azure PowerShell Samples](sql-database-powershell-samples.md).
