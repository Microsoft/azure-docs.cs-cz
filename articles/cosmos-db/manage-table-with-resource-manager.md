---
title: Šablony Správce prostředků pro rozhraní API tabulky DB Azure Cosmos
description: K vytvoření a konfiguraci rozhraní API tabulky Azure Cosmos DB table API použijte šablony Azure Resource Manageru.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246705"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API tabulky Azure Cosmos DB pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace k automatizaci správy účtů Azure Cosmos DB, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady pouze pro účty rozhraní TABLE API, chcete-li najít příklady pro jiné účty typu rozhraní API, viz: použití šablon Azure Resource Manager s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md) články.

## <a name="create-azure-cosmos-account-and-table"></a>Vytvoření účtu a tabulky Azure Cosmos<a id="create-resource"></a>

Vytvořte prostředky Azure Cosmos DB pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní TABLE API s jednou tabulkou s propustností 400 RU/s. Zkopírujte šablonu a nasaďte, jak je znázorněno níže, nebo navštivte [Azure QuickStart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) a nasaďte je z webu Azure Portal. Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

> [!NOTE]
> Názvy účtů musí být malá a 44 nebo méně znaků.
> Chcete-li aktualizovat ru/s, odešlete šablonu s aktualizovanými hodnotami vlastností propustnosti.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Nasazení přes PowerShell

Pokud chcete nasadit šablonu Správce prostředků pomocí PowerShellu, **zkopírujte** skript a vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Pokud se rozhodnete použít místně nainstalovanou verzi PowerShellu místo z prostředí Azure Cloud, budete muset [nainstalovat](/powershell/azure/install-az-ps) modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`.

### <a name="deploy-via-the-azure-cli"></a>Nasazení pomocí azure cli

Pokud chcete nasadit šablonu Azure Resource Manager pomocí azure CLI, **zkopírujte** skript a vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi azure cli namísto použití cloudového prostředí, najdete v článku [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

- [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/)
- [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony rychlých startů Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Poradce při potížích s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/templates/common-deployment-errors.md)