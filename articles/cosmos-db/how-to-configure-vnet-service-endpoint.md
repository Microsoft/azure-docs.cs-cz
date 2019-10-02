---
title: Konfigurace přístupu na základě virtuální sítě a podsítě pro účet Azure Cosmos DB
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 1c81045408a948820c8b9fef56e2c7d69cd39e08
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811916"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurace přístupu z virtuálních sítí (VNet)

Účty Azure Cosmos DB můžete nakonfigurovat tak, aby povolovaly přístup jenom z konkrétní podsítě virtuální sítě Azure. Chcete-li omezit přístup k účtu Azure Cosmos DB s připojením z podsítě ve virtuální síti:

1. Umožněte, aby podsíť odesílala podsíť a identitu virtuální sítě do Azure Cosmos DB. To můžete dosáhnout povolením koncového bodu služby pro Azure Cosmos DB v konkrétní podsíti.

1. Přidejte do účtu Azure Cosmos DB pravidlo, abyste mohli určit podsíť jako zdroj, ze kterého je účet k účtu k dispozici.

> [!NOTE]
> Pokud je v podsíti povolen koncový bod služby pro váš Azure Cosmos DB účet, zdroj provozu, který dosáhne Azure Cosmos DB přepínačů z veřejné IP adresy do virtuální sítě a podsítě. Přepínání přenosů se vztahuje na libovolný účet Azure Cosmos DB, který je z této podsítě k dispozici. Pokud vaše účty Azure Cosmos DB mají bránu firewall založenou na protokolu IP, která by umožňovala tuto podsíť, požadavky z podsítě s povolenými službami už nebudou odpovídat pravidlům brány firewall protokolu IP a budou odmítnuty.
>
> Další informace najdete v části Postup [migrace z pravidla brány firewall protokolu IP na virtuální síť](#migrate-from-firewall-to-vnet) v tomto článku.

Následující části popisují, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.

1. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** a zvolte možnost povolení přístupu z **vybraných sítí**.

1. Pokud chcete udělit přístup k podsíti existující virtuální sítě, vyberte v části **virtuální sítě**možnost **Přidat existující virtuální síť Azure**.

1. Vyberte **předplatné** , ze kterého chcete přidat virtuální síť Azure. Vyberte **virtuální sítě** a **podsítě** Azure, které chcete poskytnout přístup k vašemu Azure Cosmos DB účtu. V dalším kroku vyberte **Povolit** a povolte vybrané sítě s koncovými body služby pro Microsoft. AzureCosmosDB. Až se dokončí, vyberte **Přidat**.

   ![Vybrat virtuální síť a podsíť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Až bude účet Azure Cosmos DB povolený pro přístup z virtuální sítě, povolí provoz jenom z této zvolené podsítě. Virtuální síť a podsíť, kterou jste přidali, by se měla zobrazit, jak je znázorněno na následujícím snímku obrazovky:

   ![Virtuální síť a podsíť se úspěšně nakonfigurovaly.](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> K povolení koncových bodů služby virtuální sítě budete potřebovat následující oprávnění předplatného:
>   * Předplatné s virtuální sítí: Přispěvatel sítě
>   * Předplatné s Azure Cosmos DBm účtem: Přispěvatel účtu DocumentDB
>   * Pokud je vaše virtuální síť a účet Azure Cosmos DB v různých předplatných, ujistěte se, že odběr, který má virtuální síť, má taky zaregistrovaný poskytovatel prostředků `Microsoft.DocumentDB`. Pokud chcete zaregistrovat poskytovatele prostředků, přečtěte si článek [poskytovatelé a typy prostředků Azure](../azure-resource-manager/resource-manager-supported-services.md) .

Tady jsou pokyny pro registraci předplatného u poskytovatele prostředků.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.  

1. V nabídce nastavení vyberte **brány firewall a Azure Virtual Networks** a zvolte možnost povolení přístupu z **vybraných sítí**.  

1. Pokud chcete udělit přístup k nové službě Azure Virtual Network, vyberte v části **virtuální sítě**možnost **Přidat novou virtuální síť**.  

1. Zadejte podrobnosti požadované k vytvoření nové virtuální sítě a pak vyberte **vytvořit**. Podsíť se vytvoří s koncovým bodem služby pro Microsoft. AzureCosmosDB, který je povolený.

   ![Vyberte virtuální síť a podsíť pro novou virtuální síť.](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Pokud používáte účet Azure Cosmos DB jinými službami Azure, jako je Azure Search nebo ke kterému se přistupuje ze služby Stream Analytics nebo Power BI, povolíte přístup tak, že **v globálním datacentru Azure vyberete přijímat připojení**.

Abyste měli jistotu, že máte přístup k Azure Cosmos DB metriky z portálu, musíte povolit možnost **Povolit přístup z možností Azure Portal** . Další informace o těchto možnostech najdete v článku [Konfigurace brány firewall protokolu IP](how-to-configure-firewall.md) . Po povolení přístupu vyberte **Uložit** a uložte nastavení.

## <a id="remove-vnet-or-subnet"></a>Odebrat virtuální síť nebo podsíť

1. V okně **všechny prostředky** najděte účet Azure Cosmos DB, pro který jste přiřadili koncové body služby.  

2. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** .  

3. Chcete-li odebrat virtuální síť nebo pravidlo podsítě, vyberte **...** vedle virtuální sítě nebo podsítě a vyberte možnost **Odebrat**.

   ![Odebrání virtuální sítě](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Pokud chcete změny použít, vyberte **Uložit** .

## <a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure PowerShell

> [!NOTE]
> Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure CLI, nezapomeňte v parametrech zadat úplný seznam filtrů IP adres a seznamů ACL virtuální sítě v parametrech, nikoli jenom ty, které je potřeba přidat.

Pomocí následujících kroků můžete nakonfigurovat koncový bod služby na účet Azure Cosmos DB pomocí Azure PowerShell:  

1. Nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) a [Přihlaste se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Povolte koncový bod služby pro existující podsíť virtuální sítě.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Získat informace o virtuální síti

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Vlastnosti Azure Cosmos DB účtu získáte spuštěním následující rutiny:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicializujte proměnné pro pozdější použití. Nastavte všechny proměnné z existující definice účtu.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualizujte vlastnosti účtu Azure Cosmos DB s novou konfigurací spuštěním následujících rutin: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Spuštěním následujícího příkazu ověřte, že se účet Azure Cosmos DB aktualizoval pomocí koncového bodu služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurace koncového bodu služby pomocí rozhraní příkazového řádku Azure

Účty Azure Cosmos se dají nakonfigurovat pro koncové body služby, když se vytvoří nebo aktualizují v pozdější době, pokud je pro ně už nakonfigurovaná podsíť. Koncové body služby je možné povolit taky na účtu Cosmos, kde pro ně není ještě nakonfigurovaná podsíť, a pak začnou fungovat, když je podsíť nakonfigurovaná později. Tato flexibilita umožňuje správcům, kteří nemají přístup k účtu Cosmos i k virtuálním síťovým prostředkům, navzájem nezávisle na sobě navzájem.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Vytvoření nového účtu Cosmos a jeho připojení k podsíti back-end pro novou virtuální síť

V tomto příkladu se vytvoří virtuální síť a podsíť s povolenými koncovými body služby při jejich vytváření.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Připojení a konfigurace účtu Cosmos k podsíti back-endu nezávisle na sobě

Tato ukázka je určená k tomu, aby ukázala, jak připojit účet Azure Cosmos k existující nové virtuální síti, ve které ještě není nakonfigurovaná podsíť pro koncové body služby. K tomu je potřeba použít parametr `--ignore-missing-vnet-service-endpoint`. To umožňuje, aby se konfigurace účtu Cosmos dokončila bez chyby předtím, než se dokončí konfigurace do podsítě virtuální sítě. Po dokončení konfigurace podsítě bude účet Cosmos dostupný přes nakonfigurovanou podsíť.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>Migrace z pravidla brány firewall protokolu IP do seznamu ACL virtuální sítě

Následující postup použijte pouze pro účty Azure Cosmos DB s existujícími pravidly brány firewall protokolu IP, které povolují podsíť, pokud chcete místo pravidla brány firewall protokolu IP použít virtuální síť a seznamy ACL založené na podsíti.

Po zapnutí koncového bodu služby pro účet Azure Cosmos DB pro podsíť se požadavky odesílají se zdrojem, který obsahuje informace o virtuální síti a podsíti namísto veřejné IP adresy. Tyto požadavky se neshodují s filtrem IP adres. Tento přepínač zdrojového kódu se stane pro všechny účty Azure Cosmos DB, ke kterým přistupovala z podsítě s povoleným koncovým bodem služby. Chcete-li zabránit výpadkům, použijte následující postup:

1. Vlastnosti Azure Cosmos DB účtu získáte spuštěním následující rutiny:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicializujte proměnné pro pozdější použití. Nastavte všechny proměnné z existující definice účtu. Přidejte seznam ACL virtuální sítě ke všem účtům Azure Cosmos DB, ke kterým se přistupoval z podsítě s příznakem `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Aktualizujte vlastnosti účtu Azure Cosmos DB s novou konfigurací spuštěním následujících rutin:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Opakujte kroky 1-3 pro všechny účty Azure Cosmos DB, ke kterým přistupujete z podsítě.

1.  Počkejte 15 minut a pak aktualizujte podsíť, aby se povolil koncový bod služby.

1.  Povolte koncový bod služby pro existující podsíť virtuální sítě.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Odeberte pravidlo brány firewall protokolu IP pro podsíť.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si článek [Podpora brány firewall](firewall-support.md) .
