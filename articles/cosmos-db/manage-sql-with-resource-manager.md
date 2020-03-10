---
title: Vytvoření a Správa Azure Cosmos DB s využitím šablon Správce prostředků
description: Použití šablon Azure Resource Manager k vytvoření a konfiguraci Azure Cosmos DB rozhraní API pro SQL (jádro)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 72a87c3b23e0eed6cfbf1614388702443f4e99d0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388034"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Správa Azure Cosmos DB prostředků rozhraní API SQL (Core) pomocí šablon Azure Resource Manager

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manageru automatizovat správu účtů, databází a kontejnerů Azure Cosmos DB.

Tento článek obsahuje jenom Azure Resource Manager příklady šablon pro účty rozhraní SQL API. Můžete také najít příklady šablon pro rozhraní API pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [Table](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Vytvoření účtu, databáze a kontejneru Azure Cosmos

Následující šablona Azure Resource Manager vytvoří účet Azure Cosmos s:

* Dva kontejnery sdílející propustnost 400 požadovaných jednotek za sekundu (RU/s) na úrovni databáze.
* Jeden kontejner s vyhrazenou propustností 400 RU/s.

Pokud chcete vytvořit prostředky Azure Cosmos DB, zkopírujte následující příklad šablony a nasaďte ji tak, jak je popsáno v [PowerShellu](#deploy-via-powershell) nebo rozhraní příkazového [řádku Azure CLI](#deploy-via-azure-cli).

* Volitelně můžete navštívit [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) a nasadit šablonu z Azure Portal.
* Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s parametrem `--template-file`.

> [!IMPORTANT]
>
> * Když přidáváte nebo odebíráte umístění účtu Azure Cosmos, nemůžete současně upravovat ostatní vlastnosti. Tyto operace se musí provádět samostatně.
> * Názvy účtů jsou omezené na 44 znaků, a to vše malými písmeny.
> * Chcete-li změnit hodnoty propustnosti, šablonu znovu odešlete s aktualizovaným RU/s.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Pokud chcete vytvořit kontejner s velkým klíčem oddílu, upravte předchozí šablonu tak, aby zahrnovala vlastnost `"version":2` v rámci objektu `partitionKey`.

### <a name="deploy-via-powershell"></a>Nasazení prostřednictvím PowerShellu

Použití PowerShellu k nasazení šablony Azure Resource Manager:

1. **Zkopírujte** skript.
2. Vyberte **zkusit** pro otevření Azure Cloud Shell.
3. V okně Azure Cloud Shell klikněte pravým tlačítkem myši a vyberte možnost **Vložit**.

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

Šablonu můžete nasadit s místně nainstalovanou verzí PowerShellu místo Azure Cloud Shell. Budete muset [nainstalovat modul Azure PowerShell](/powershell/azure/install-az-ps). Požadovanou verzi zjistíte spuštěním `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

Postup při nasazení Azure Resource Manager šablony pomocí Azure CLI:

1. **Zkopírujte** skript.
2. Vyberte **zkusit** pro otevření Azure Cloud Shell.
3. V okně Azure Cloud Shell klikněte pravým tlačítkem myši a vyberte možnost **Vložit**.

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

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Místo toho se můžete rozhodnout nasadit šablonu s místně nainstalovanou verzí Azure CLI Azure Cloud Shell. Další informace najdete v článku [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Vytvoření kontejneru Azure Cosmos DB pomocí funkce na straně serveru

Pomocí šablony Azure Resource Manager můžete vytvořit kontejner Azure Cosmos DB s uloženou procedurou, triggerem a uživatelsky definovanou funkcí.

Zkopírujte následující příklad šablony a nasaďte ji podle popisu v [PowerShellu](#deploy-with-powershell) nebo [Azure CLI](#deploy-with-azure-cli).

* Volitelně můžete navštívit [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) a nasadit šablonu z Azure Portal.
* Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s parametrem `--template-file`.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Použití PowerShellu k nasazení šablony Azure Resource Manager:

1. **Zkopírujte** skript.
1. Vyberte **zkusit** pro otevření Azure Cloud Shell.
1. Klikněte pravým tlačítkem na okno Azure Cloud Shell a pak vyberte **Vložit**.

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

Šablonu můžete nasadit s místně nainstalovanou verzí PowerShellu místo Azure Cloud Shell. Budete muset [nainstalovat modul Azure PowerShell](/powershell/azure/install-az-ps). Požadovanou verzi zjistíte spuštěním `Get-Module -ListAvailable Az`.

### <a name="deploy-with-azure-cli"></a>Nasazení s Azure CLI

Postup při nasazení Azure Resource Manager šablony pomocí Azure CLI:

1. **Zkopírujte** skript.
2. Vyberte **zkusit** pro otevření Azure Cloud Shell.
3. V okně Azure Cloud Shell klikněte pravým tlačítkem myši a vyberte možnost **Vložit**.

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

Tady je několik dalších prostředků:

* [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
