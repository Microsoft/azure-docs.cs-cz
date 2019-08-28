---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: db7746bc91935c0385e97d494a45d34819665ced
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093384"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu služby Azure Cosmos DB

Tento článek popisuje, jak spravovat různé úlohy v účtu Azure Cosmos pomocí Azure Portal, Azure PowerShell, Azure CLI a šablon Azure Resource Manager.

## <a name="create-an-account"></a>Vytvořit účet

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and < 31 characters

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Šablona Azure Resource Manager

Tato šablona Azure Resource Manager vytvoří účet Azure Cosmos pro všechna podporovaná rozhraní API nakonfigurovaná se dvěma oblastmi a možnostmi výběru úrovně konzistence, automatického převzetí služeb při selhání a více hlavních serverů. Pokud chcete tuto šablonu nasadit, klikněte na stránce Readme na nasadit do Azure a [vytvořte účet Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account) .

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

1. Přejděte k účtu Azure Cosmos a otevřete nabídku replikovat **data globálně** .

1. Chcete-li přidat oblasti, vyberte šestiúhelníky na mapě pomocí **+** popisku, který odpovídá vašim požadovaným oblastem (y). Pokud chcete přidat oblast, vyberte možnost **+ Přidat oblast** a v rozevírací nabídce vyberte oblast.

1. Chcete-li odebrat oblasti, zrušte zaškrtnutí jedné nebo více oblastí z mapy tak, že vyberete modré šestiúhelníky se značkami zaškrtnutí. Nebo vyberte ikonu "wastebasket" (🗑) vedle oblasti na pravé straně.

1. Pokud chcete změny uložit, vyberte **OK**.

   ![Nabídka Přidat nebo odebrat oblasti](./media/how-to-manage-database-account/add-region.png)

V režimu zápisu s jednou oblastí nemůžete odebrat oblast zápisu. Než budete moct odstranit aktuální oblast pro zápis, musíte převzít služby při selhání do jiné oblasti.

V režimu zápisu ve více oblastech můžete přidat nebo odebrat libovolnou oblast, pokud máte aspoň jednu oblast.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and <31 characters

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and <31 characters

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Konfigurace více oblastí pro zápis

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otevřete kartu **replikace dat globálně** a výběrem **Povolit** povolte zápisy ve více oblastech. Po povolení zápisů ve více oblastech se všechny oblasti čtení, které v daném účtu aktuálně máte, stanou oblastmi pro čtení a zápis. 

> [!NOTE]
> Když povolíte zápis ve více oblastech, nemůžete ho zakázat. 

![Účet Azure Cosmos konfiguruje snímek s více hlavními servery.](./media/how-to-manage-database-account/single-to-multi-master.png)

Pokud chcete získat další otázky askcosmosdb@microsoft.com k této funkci, obraťte se na tento alias. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Šablona Resource Manageru

Účet lze migrovat z jednoho hlavního serveru do více hlavních serverů nasazením šablony Správce prostředků používané k vytvoření účtu a nastavení `enableMultipleWriteLocations: true`. Následující šablona Azure Resource Manager je minimální šablona, která nasadí účet Azure Cosmos pro rozhraní SQL API se dvěma oblastmi a více umístěními pro zápis povolených.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Povolení automatického převzetí služeb při selhání pro účet Azure Cosmos

Možnost automatického převzetí služeb při selhání umožňuje Azure Cosmos DB převzetí služeb při selhání do oblasti s nejvyšší prioritou převzetí služeb při selhání bez akce uživatele, pokud se oblast stane nedostupnou. Když je povolené automatické převzetí služeb při selhání, může se upravit priorita oblasti. Aby bylo možné povolit automatické převzetí služeb při selhání, musí mít účet dvě nebo více oblastí.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z účtu Azure Cosmos otevřete podokno **globálně replikovat data** .

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **automatické převzetí služeb při selhání** ověřte, že je **možnost povolit automatické převzetí služeb při** selhání nastavena na **zapnuto**. 

4. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavte priority převzetí služeb při selhání pro účet Azure Cosmos.

Po nakonfigurování účtu Cosmos pro automatické převzetí služeb při selhání je možné změnit prioritu převzetí služeb při selhání pro oblasti.

> [!IMPORTANT]
> Pokud je účet nakonfigurovaný na automatické převzetí služeb při selhání, nemůžete upravit oblast zápisu (priorita převzetí služeb při selhání nula). Chcete-li změnit oblast zápisu, je nutné zakázat automatické převzetí služeb při selhání a provést ruční převzetí služeb při selhání.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z účtu Azure Cosmos otevřete podokno **globálně replikovat data** .

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **automatické převzetí služeb při selhání** ověřte, že je **možnost povolit automatické převzetí služeb při** selhání nastavena na **zapnuto**.

4. Pokud chcete změnit prioritu převzetí služeb při selhání, přetáhněte oblasti čtení přes tři tečky na levé straně řádku, která se zobrazí, když na ně najedete myší.

5. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Provedení ručního převzetí služeb při selhání v účtu Azure Cosmos

> [!IMPORTANT]
> Aby tato operace proběhla úspěšně, musí být účet Azure Cosmos nakonfigurovaný na ruční převzetí služeb při selhání.

Proces ručního převzetí služeb při selhání zahrnuje změnu oblasti zápisu účtu (priorita převzetí služeb při selhání = 0) na jinou oblast nakonfigurovanou pro tento účet.

> [!NOTE]
> Pro účty s více hlavními servery nejde ručně převzít službu při selhání. V případě aplikací, které používají sadu Azure Cosmos SDK, bude sada SDK detekovat, kdy se oblast přestane používat, a pak automaticky přesměruje na nejbližší nejbližší oblast, pokud v sadě SDK používáte rozhraní API pro více koncových míst.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Přejděte k účtu Azure Cosmos a otevřete nabídku replikovat **data globálně** .

2. V horní části nabídky vyberte **ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V nabídce **ruční převzetí služeb při selhání** vyberte novou oblast pro zápis. Zaškrtnutím políčka označíte, že pochopení této možnosti mění vaši oblast zápisu.

4. Pokud chcete převzetí služeb při selhání aktivovat, vyberte **OK**.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Další postup

Další informace a příklady, jak spravovat účet Azure Cosmos i databáze a kontejnery, najdete v následujících článcích:

* [Správa Azure Cosmos DB pomocí Azure PowerShell](manage-with-powershell.md)
* [Správa služby Azure Cosmos DB pomocí Azure CLI](manage-with-cli.md)
