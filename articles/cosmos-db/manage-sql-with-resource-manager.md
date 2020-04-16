---
title: Vytváření a správa Azure Cosmos DB pomocí šablon Správce prostředků
description: Vytvoření a konfigurace rozhraní API Azure Cosmos DB for SQL (Core) pomocí šablon Azure Resource Manageru
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390891"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Správa prostředků rozhraní API Azure Cosmos DB SQL (Core) pomocí šablon Azure Resource Manager

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manageru automatizovat správu účtů, databází a kontejnerů Azure Cosmos DB.

Tento článek zobrazuje jenom příklady šablon Azure Resource Manager pro účty ROZHRANÍ SQL API. Můžete také najít příklady šablon pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [Table](manage-table-with-resource-manager.md) API.

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Vytvoření účtu, databáze a kontejneru Azure Cosmos

Následující šablona Azure Resource Manager vytvoří účet Azure Cosmos s:

* Dva kontejnery, které sdílejí 400 požadovaných jednotek za sekundu (RU/s) propustnost na úrovni databáze.
* Jeden kontejner s vyhrazenou propustností 400 RU/s.

Chcete-li vytvořit prostředky Azure Cosmos DB, zkopírujte následující ukázkovou šablonu a nasaďte ji tak, jak je popsáno, buď prostřednictvím [PowerShellu](#deploy-via-powershell) nebo [Azure CLI](#deploy-via-azure-cli).

* Volitelně můžete navštívit [Galerii rychlých startů Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) a nasadit šablonu z webu Azure Portal.
* Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

> [!IMPORTANT]
>
> * Když přidáte nebo odeberete umístění do účtu Azure Cosmos, nemůžete současně upravovat další vlastnosti. Tyto operace musí být provedeny samostatně.
> * Názvy účtů jsou omezeny na 44 znaků, všechna malá písmena.
> * Chcete-li změnit hodnoty propustnosti, odešlete šablonu s aktualizovanými ru/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Chcete-li vytvořit kontejner s velkým klíčem oddílu, upravte předchozí šablonu tak, aby zahrnovala `"version":2` vlastnost do objektu. `partitionKey`

### <a name="deploy-via-powershell"></a>Nasazení přes PowerShell

Použití PowerShellu k nasazení šablony Azure Resource Manageru:

1. **Zkopírujte** skript.
2. Vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell.
3. Klikněte pravým tlačítkem myši v okně Azure Cloud Shell a pak vyberte **Vložit**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Šablonu můžete nasadit s místně nainstalovanou verzí PowerShellu místo Azure Cloud Shellu. Budete muset [nainstalovat modul Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním `Get-Module -ListAvailable Az` vyhledejte požadovanou verzi.

### <a name="deploy-via-azure-cli"></a>Nasazení pomocí azure cli

Použití azure cli k nasazení šablony Azure Resource Manager:

1. **Zkopírujte** skript.
2. Vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell.
3. Klikněte pravým tlačítkem myši v okně Azure Cloud Shell a pak vyberte **Vložit**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Šablonu můžete nasadit s místně nainstalovanou verzí Azure CLI místo Azure Cloud Shell. Další informace naleznete v článku [rozhraní CLI (Azure Command-Line Interface).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Vytvoření kontejneru Azure Cosmos DB s funkcemi na straně serveru

Šablonu Azure Resource Manager uděláte k vytvoření kontejneru Azure Cosmos DB s uloženou procedurou, aktivační událostí a uživatelem definovanou funkcí.

Zkopírujte následující ukázkovou šablonu a nasaďte ji podle popisu, buď pomocí [PowerShellu](#deploy-with-powershell) nebo [Azure CLI](#deploy-with-azure-cli).

* Volitelně můžete navštívit [Azure QuickStart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) a nasadit šablonu z webu Azure Portal.
* Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Použití PowerShellu k nasazení šablony Azure Resource Manageru:

1. **Zkopírujte** skript.
1. Vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell.
1. Klikněte pravým tlačítkem myši na okno Azure Cloud Shell a pak vyberte **Vložit**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Šablonu můžete nasadit s místně nainstalovanou verzí PowerShellu místo Azure Cloud Shellu. Budete muset [nainstalovat modul Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním `Get-Module -ListAvailable Az` vyhledejte požadovanou verzi.

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Použití azure cli k nasazení šablony Azure Resource Manager:

1. **Zkopírujte** skript.
2. Vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell.
3. Klikněte pravým tlačítkem myši v okně Azure Cloud Shell a pak vyberte **Vložit**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/)
* [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Šablony rychlých startů Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Poradce při potížích s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/templates/common-deployment-errors.md)
