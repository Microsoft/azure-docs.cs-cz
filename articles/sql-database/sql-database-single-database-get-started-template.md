---
title: 'Azure Resource Manageru: Vytvoření izolované databáze – Azure SQL Database | Dokumentace Microsoftu'
description: Vytvoření izolované databáze ve službě Azure SQL Database pomocí šablony Azure Resource Manageru.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
manager: craigg
ms.date: 06/28/2019
ms.openlocfilehash: 4ef0f9ff6f8620109f2ef6f6bd5f549281b4de54
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472143"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí šablony Azure Resource Manageru

Vytváření [izolované databáze](sql-database-single-database.md) je možnost nejrychlejší a nejjednodušší nasazení pro vytvoření databáze ve službě Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit izolované databáze pomocí šablony Azure Resource Manageru. Další informace najdete v tématu [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Izolované databáze má definovanou sadu výpočetních, paměťových, vstupně-výstupní operace a úložiště prostředků pomocí jednoho ze dvou [zakoupení modely](sql-database-purchase-models.md). Při vytvoření izolované databáze také definovat [serveru služby SQL Database](sql-database-servers.md) ho spravovat a umístěte ji v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v určité oblasti.

Následující soubor JSON je šablonu, která se používá v tomto článku. Šablony jsou uloženy v [Githubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Další ukázkové šablony Azure SQL database najdete v [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Vyberte **vyzkoušet** z následující blok kódu Powershellu a otevřete Azure Cloud Shell.

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

1. Vyberte **kopírování** zkopírujte skript prostředí PowerShell do schránky.
1. Klikněte pravým tlačítkem na podokno prostředí a pak vyberte **vložit**.

    Trvá několik okamžiků vytvoření databázového serveru a databáze.

## <a name="query-the-database"></a>Dotaz na databázi

Dotaz na databázi, naleznete v tématu [dotaz na databázi](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít na zachovat tuto skupinu prostředků, serveru databáze a izolované databáze [další kroky](#next-steps). Následující kroky ukazují, jak k připojení a dotazování databáze pomocí různých metod.

Pokud chcete odstranit skupinu prostředků pomocí Azure Powershellu:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další postup

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databáze z místní nebo vzdálené nástroje. Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru, [připojení a dotazování](sql-database-connect-query.md) vaší databáze pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Vytvoření izolované databáze pomocí Azure CLI najdete v tématu [ukázky v Azure CLI](sql-database-cli-samples.md).
- Vytvoření izolované databáze pomocí Azure Powershellu najdete v tématu [ukázky Azure Powershellu](sql-database-powershell-samples.md).
