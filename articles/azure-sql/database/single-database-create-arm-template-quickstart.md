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
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 03d598e0c0756cd304248ab19ab28a25836c57ab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054333"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Rychlý Start: vytvoření jedné databáze v Azure SQL Database pomocí šablony Azure Resource Manager
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Vytvoření [jedné databáze](single-database-overview.md) je nejrychlejší a nejjednodušší možnost pro vytvoření databáze v Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit jedinou databázi pomocí šablony Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Jedna databáze má definovanou sadu výpočetních, paměťových, vstupně-výstupních prostředků a prostředků úložiště pomocí jednoho ze dvou [nákupních modelů](purchasing-models.md). Když vytvoříte izolovanou databázi, nadefinujete taky [Server](logical-servers.md) pro správu a umístění v rámci [skupiny prostředků Azure](../../active-directory-b2c/overview.md) v zadané oblasti.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Tyto prostředky jsou definované v šabloně:

- [**Microsoft. SQL/servery**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/servery/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft. SQL/servery/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft. SQL/servery/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft. SQL/servery/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Storage/storageAccounts/Providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

Další ukázky šablon Azure SQL Database najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Nasazení šablony

Vyberte příkaz **vyzkoušet** z následujícího bloku kódu prostředí PowerShell a otevřete Azure Cloud Shell.

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

Dotaz na databázi najdete v tématu [dotazování databáze](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít k [dalším krokům](#next-steps), zachovejte tuto skupinu prostředků, server a samostatnou databázi. Následující kroky ukazují, jak se připojit k databázi a dotazovat se na ni pomocí různých metod.

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

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databázi z místních nebo vzdálených nástrojů. Další informace najdete v tématu [Vytvoření pravidla brány firewall na úrovni serveru](firewall-create-server-level-portal-quickstart.md).
- Po vytvoření pravidla brány firewall na úrovni serveru se [připojte a Dotazujte](connect-query-content-reference-guide.md) databázi pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pokud chcete vytvořit izolovanou databázi pomocí Azure CLI, přečtěte si [ukázky v Azure CLI](az-cli-script-samples-content-guide.md).
- Chcete-li vytvořit izolovanou databázi pomocí Azure PowerShell, přečtěte si téma [Azure PowerShell Samples](powershell-script-content-guide.md).
- Další informace o vytváření šablon Správce prostředků najdete v tématu [Vytvoření první šablony](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
