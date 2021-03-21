---
title: 'Azure Resource Manager: vytvoření samostatné databáze'
description: Pomocí šablony Azure Resource Manager vytvořte v Azure SQL Database jednu databázi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96187081"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Rychlý Start: vytvoření jedné databáze v Azure SQL Database pomocí šablony ARM

Vytvoření [jedné databáze](single-database-overview.md) je nejrychlejší a nejjednodušší možnost pro vytvoření databáze v Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit samostatnou databázi pomocí šablony Azure Resource Manager (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Jedna databáze má definovanou sadu výpočetních, paměťových, vstupně-výstupních prostředků a prostředků úložiště pomocí jednoho ze dvou [nákupních modelů](purchasing-models.md). Když vytvoříte izolovanou databázi, nadefinujete taky [Server](logical-servers.md) pro správu a umístění v rámci [skupiny prostředků Azure](../../active-directory-b2c/overview.md) v zadané oblasti.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-sql-database/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Tyto prostředky jsou definované v šabloně:

- [**Microsoft. SQL/servery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/servery/databáze**](/azure/templates/microsoft.sql/servers/databases)

Další ukázky šablon Azure SQL Database najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

Vyberte příkaz **vyzkoušet** z následujícího bloku kódu prostředí PowerShell a otevřete Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Ověření nasazení

Dotaz na databázi najdete v tématu [dotazování databáze](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), zachovejte tuto skupinu prostředků, server a samostatnou databázi. Následující kroky ukazují, jak se připojit k databázi a dotazovat se na ni pomocí různých metod.

Odstranění skupiny prostředků:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databázi z místních nebo vzdálených nástrojů. Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](firewall-create-server-level-portal-quickstart.md).
- Po vytvoření pravidla brány firewall na úrovni serveru se [připojte a Dotazujte](connect-query-content-reference-guide.md) databázi pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- Pokud chcete vytvořit izolovanou databázi pomocí Azure CLI, přečtěte si [ukázky v Azure CLI](az-cli-script-samples-content-guide.md).
- Chcete-li vytvořit izolovanou databázi pomocí Azure PowerShell, přečtěte si téma [Azure PowerShell Samples](powershell-script-content-guide.md).
- Další informace o tom, jak vytvořit šablony ARM, najdete v tématu [Vytvoření první šablony](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).