---
title: 'Azure Resource Manager: Vytvoření jedné databáze'
description: Vytvořte jednu databázi v Azure SQL Database pomocí šablony Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 7c42ff7f42dea049752f9f879abffffd0e7b3902
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531324"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Úvodní příručka: Vytvoření jedné databáze v Azure SQL Database pomocí šablony Azure Resource Manager

Vytvoření [jedné databáze](sql-database-single-database.md) je nejrychlejší a nejjednodušší možnost nasazení pro vytvoření databáze v Azure SQL Database. Tento rychlý start ukazuje, jak vytvořit jednu databázi pomocí šablony Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Žádný

## <a name="create-a-single-database"></a>Vytvoření jedné databáze

Jedna databáze má definovanou sadu výpočetních prostředků, paměti, voda a prostředků úložiště pomocí jednoho ze dvou [nákupních modelů](sql-database-purchase-models.md). Když vytvoříte jednu databázi, definujete také [databázový server SQL,](sql-database-servers.md) který ji spravuje a umístí do [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) v zadané oblasti.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Tyto prostředky jsou definovány v šabloně:

- [**Microsoft.SQL/servery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servery/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft.Sql/servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft.Sql/servery/hodnocení zranitelnosti**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft.Sql/servery/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments Microsoft.Storage/storageAccounts/providers/roleAssignments Microsoft.Storage/storageAccounts/providers/roleAssignments Microsoft.**](/azure/templates/microsoft.authorization/roleassignments)

Další ukázky šablon databáze Azure SQL najdete v [azure quickstart šablony](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Nasazení šablony

Vyberte **Vyzkoušet** z následujícího bloku kódu PowerShellu otevřete Azure Cloud Shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Ověření nasazení

Chcete-li zadat dotaz na databázi, [přečtěte si informace o dotazu na databázi](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li přejít na [další kroky](#next-steps), ponechejte tuto skupinu prostředků, databázový server a jednu databázi. Další kroky ukazují, jak připojit a dotazovat databázi pomocí různých metod.

Odstranění skupiny prostředků:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Další kroky

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k jedné databázi z místních nebo vzdálených nástrojů. Další informace naleznete [v tématu Vytvoření pravidla brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru připojte databázi [a zařaďte se tak pomocí](sql-database-connect-query.md) několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pokud chcete vytvořit jednu databázi pomocí Azure CLI, najdete [v tématu ukázky nastavení příkazového příkazu Kavi .](sql-database-cli-samples.md)
- Pokud chcete vytvořit jednu databázi pomocí Azure PowerShellu, přečtěte si [párky Azure PowerShellu](sql-database-powershell-samples.md).
- Informace o vytváření šablon Správce prostředků naleznete v tématu [Vytvoření první šablony](../azure-resource-manager/templates/template-tutorial-create-first-template.md).
