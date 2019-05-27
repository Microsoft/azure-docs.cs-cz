---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 07d177987db1dea261520e8ee2543d871d552acb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240893"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu Azure Cosmos

Tento článek popisuje, jak spravovat různé úlohy na účtu služby Azure Cosmos pomocí webu Azure portal, prostředí Azure PowerShell, Azure CLI a šablon Azure Resource Manageru.

## <a name="create-an-account"></a>Vytvořit účet

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

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

### <a id="create-database-account-via-arm-template"></a>Šablona Azure Resource Manageru

Tuto šablonu Azure Resource Manageru vytvoří účet služby Azure Cosmos DB pro všechny podporované rozhraní API nakonfigurovat dvě oblasti a v možnostech úrovně konzistence, automatické převzetí služeb při selhání a více hlavních databází. Chcete-li nasadit tuto šablonu, klikněte na nasadit do Azure na stránce readme [účet vytvořit službu Azure Cosmos DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

1. Přejděte ke svému účtu Azure Cosmos a otevřete **globální replikace dat** nabídky.

1. Přidat oblasti, vyberte Šestiúhelníky na mapě s **+** popisek, který odpovídá požadované oblasti. Můžete také přidat oblast, vyberte **+ přidat oblast** a z rozevírací nabídky vyberte oblast.

1. Pokud chcete odebrat oblasti, zrušte tak, že vyberete modré Šestiúhelníky se zaškrtnutím jedné nebo několika oblastech z mapy. Nebo vyberte "Koš" (🗑) ikonu vedle oblast na pravé straně.

1. Chcete-li uložit změny, vyberte **OK**.

   ![Přidání nebo odebrání oblastí nabídky](./media/how-to-manage-database-account/add-region.png)

V jedné oblasti zápisu režimu, že nelze odebrat oblasti pro zápis. Musíte převzetí služeb při selhání do jiné oblasti než budete moct odstranit aktuální oblasti pro zápis.

Ve více oblastech režimu, můžete přidat nebo odebrat libovolnou oblast, pokud máte alespoň jedné oblasti zápisu.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

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

## <a id="configure-multiple-write-regions"></a>Konfigurace více oblastí zápisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otevřít **replikovat Data globálně** kartě a vyberte **povolit** povolit více oblastí zápisu. Po povolení více oblastí zápisu všech oblastí čtení, které máte aktuálně na účtu se stanou čtení a zápis oblastech. 

> [!NOTE]
> Po povolení více oblastí zápisu se nedá vypnout. 

![Účet služby Azure Cosmos nakonfiguruje snímek obrazovky s několika hlavními databázemi](./media/how-to-manage-database-account/single-to-multi-master.png)

Kontaktujte prosím askcosmosdb@microsoft.com alias další dotazy týkající se této funkce. 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos Account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Šablona Resource Manageru

Účet můžete migrovat z jednoho hlavní větve do více hlavních databází nasazením šablona Resource Manageru použitá k vytvoření účtu a nastavení `enableMultipleWriteLocations: true`. Následující šablony Azure Resource Manageru je úplné minimální šablonu, která nasadí účet služby Azure Cosmos DB pro rozhraní SQL API s jedné oblasti a více hlavních databází povolena.

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
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Povolit automatické převzetí služeb při selhání pro váš účet Azure Cosmos DB

Možnost automatického převzetí služeb při selhání umožňuje službě Azure Cosmos DB převzetí služeb při selhání do oblasti s nejvyšší prioritou převzetí služeb při selhání se žádné akce uživatele by měla oblast k dispozici. Pokud je povoleno automatické převzetí služeb při selhání, může být upraveno priority oblasti. Účet musí mít dva nebo více oblastí, které chcete povolit automatické převzetí služeb při selhání.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z vašeho účtu služby Azure Cosmos DB, otevřete **globální replikace dat** podokně.

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **automatické převzetí služeb při selhání** podokno, ujistěte se, že **povolit automatické převzetí služeb při selhání** je nastavena na **ON**. 

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

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavení priorit převzetí služeb při selhání pro váš účet Azure Cosmos

Po Cosmos účet je nakonfigurovaný pro automatické převzetí služeb při selhání, můžete změnit prioritu převzetí služeb při selhání pro oblasti.

> [!IMPORTANT]
> Nelze změnit oblast zápisu (priority převzetí služeb při selhání nula) Pokud je účet nakonfigurovaný pro automatické převzetí služeb při selhání. Chcete-li změnit oblast zápisu, musíte zakázat automatické převzetí služeb při selhání a proveďte ruční převzetí služeb při selhání.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Ze svého účtu Azure Cosmos, otevřete **globální replikace dat** podokně.

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **automatické převzetí služeb při selhání** podokno, ujistěte se, že **povolit automatické převzetí služeb při selhání** je nastavena na **ON**.

4. Změna priority převzetí služeb při selhání, přetáhněte oblastí pro čtení přes na tři tečky na levé straně, který se zobrazí při najetí myši na řádek.

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

## <a id="manual-failover"></a>Proveďte ruční převzetí služeb při selhání v rámci účtu Azure Cosmos

> [!IMPORTANT]
> Účet Azure Cosmos musí být nakonfigurovaný pro ruční převzetí služeb při selhání pro tato operace proběhla úspěšně.

Součástí procesu pro ruční převzetí služeb při selhání je změna oblasti zápisu na účet (priority převzetí služeb při selhání = 0) do jiné oblasti, které jsou nakonfigurované pro účet.

> [!NOTE]
> Účty s několika hlavními databázemi nelze převzít ručně. Pro aplikace pomocí sady SDK služby Azure Cosmos DB bude sada SDK rozpoznat, kdy se oblast stane nedostupnou, potom automaticky přesměrovat na další nejbližší oblasti, pokud používáte vícenásobné navádění rozhraní API v sadě SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Přejděte ke svému účtu Azure Cosmos a otevřete **globální replikace dat** nabídky.

2. V horní nabídce vyberte **ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **ruční převzetí služeb při selhání** nabídku, vyberte novou oblast zápisu. Zaškrtněte políčko označující, že chápete, že se že tato možnost změní vaše oblast pro zápis.

4. Chcete-li aktivovat převzetí služeb při selhání, vyberte **OK**.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
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

Další informace a příklady o tom, jak spravovat účet služby Azure Cosmos stejně jako databáze a kontejnerů najdete v následujících článcích:

* [Správa služby Azure Cosmos DB pomocí Azure Powershellu](manage-with-powershell.md)
* [Správa služby Azure Cosmos DB pomocí rozhraní příkazového řádku Azure](manage-with-cli.md)
