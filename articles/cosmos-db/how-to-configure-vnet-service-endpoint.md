---
title: Konfigurace přístupu na základě virtuální sítě pro účet Azure Cosmos
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 995e5a1a87ee332c48641f42c4134e3e58f11cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495416"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurace přístupu z virtuálních sítí (VNet)

Účty služby Azure Cosmos DB můžete nakonfigurovat tak, aby umožňovaly přístup pouze z konkrétní podsítě virtuální sítě Azure. Chcete-li omezit přístup k účtu Azure Cosmos DB s připojením z podsítě ve virtuální síti:

1. Umožněte, aby podsíť odesílala podsíť a identitu virtuální sítě do Azure Cosmos DB. To můžete dosáhnout povolením koncového bodu služby pro Azure Cosmos DB v konkrétní podsíti.

1. Přidejte do účtu Azure Cosmos DB pravidlo, abyste mohli určit podsíť jako zdroj, ze kterého je účet k účtu k dispozici.

> [!NOTE]
> Pokud je v podsíti povolen koncový bod služby pro váš Azure Cosmos DB účet, zdroj provozu, který dosáhne Azure Cosmos DB přepínačů z veřejné IP adresy do virtuální sítě a podsítě. Přepínání přenosů se vztahuje na libovolný účet Azure Cosmos DB, který je z této podsítě k dispozici. Pokud vaše účty Azure Cosmos DB mají bránu firewall založenou na protokolu IP, která by umožňovala tuto podsíť, požadavky z podsítě s povolenými službami už nebudou odpovídat pravidlům brány firewall protokolu IP a budou odmítnuty.
>
> Další informace najdete v části Postup [migrace z pravidla brány firewall protokolu IP na virtuální síť](#migrate-from-firewall-to-vnet) v tomto článku.

Následující části popisují, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.

1. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** a zvolte možnost povolení přístupu z **vybraných sítí**.

1. Pokud chcete udělit přístup k podsíti existující virtuální sítě, vyberte v části **virtuální sítě**možnost **Přidat existující virtuální síť Azure**.

1. Vyberte **předplatné** , ze kterého chcete přidat virtuální síť Azure. Vyberte **virtuální sítě** a **podsítě** Azure, které chcete poskytnout přístup k vašemu Azure Cosmos DB účtu. V dalším kroku vyberte **Povolit** a povolte vybrané sítě s koncovými body služby pro Microsoft. AzureCosmosDB. Až se dokončí, vyberte **Přidat**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Vybrat virtuální síť a podsíť":::

1. Až bude účet Azure Cosmos DB povolený pro přístup z virtuální sítě, povolí provoz jenom z této zvolené podsítě. Virtuální síť a podsíť, kterou jste přidali, by se měla zobrazit, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Vybrat virtuální síť a podsíť":::

> [!NOTE]
> K povolení koncových bodů služby virtuální sítě budete potřebovat následující oprávnění předplatného:
>   * Předplatné s virtuální sítí: Přispěvatel sítě
>   * Předplatné s Azure Cosmos DBm účtem: Přispěvatel účtu DocumentDB
>   * Pokud je vaše virtuální síť a účet Azure Cosmos DB v různých předplatných, ujistěte se, že odběr, který má virtuální síť, má taky `Microsoft.DocumentDB` zaregistrovaný poskytovatel prostředků. Pokud chcete zaregistrovat poskytovatele prostředků, přečtěte si článek [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Tady jsou pokyny pro registraci předplatného u poskytovatele prostředků.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.  

1. V nabídce nastavení vyberte **brány firewall a Azure Virtual Networks** a zvolte možnost povolení přístupu z **vybraných sítí**.  

1. Pokud chcete udělit přístup k nové službě Azure Virtual Network, vyberte v části **virtuální sítě**možnost **Přidat novou virtuální síť**.  

1. Zadejte podrobnosti požadované k vytvoření nové virtuální sítě a pak vyberte **vytvořit**. Podsíť se vytvoří s koncovým bodem služby pro Microsoft. AzureCosmosDB, který je povolený.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Vybrat virtuální síť a podsíť":::

Pokud váš Azure Cosmos DB účet používají jiné služby Azure, jako je Azure Kognitivní hledání nebo ke kterému se přistupuje ze služby Stream Analytics nebo Power BI, povolíte přístup tak, že **v globálním datacentru Azure vyberete přijímat připojení**.

Abyste měli jistotu, že máte přístup k Azure Cosmos DB metriky z portálu, musíte povolit možnost **Povolit přístup z možností Azure Portal** . Další informace o těchto možnostech najdete v článku [Konfigurace brány firewall protokolu IP](how-to-configure-firewall.md) . Po povolení přístupu vyberte **Uložit** a uložte nastavení.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Odebrat virtuální síť nebo podsíť

1. V okně **všechny prostředky** najděte účet Azure Cosmos DB, pro který jste přiřadili koncové body služby.  

1. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** .  

1. Chcete-li odebrat virtuální síť nebo pravidlo podsítě, vyberte **...** vedle virtuální sítě nebo podsítě a vyberte možnost **Odebrat**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Vybrat virtuální síť a podsíť":::

1. Vyberte **Uložit**, aby se tyto změny použily.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure PowerShell

> [!NOTE]
> Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure CLI, nezapomeňte v parametrech zadat úplný seznam filtrů IP adres a seznamů ACL virtuální sítě v parametrech, nikoli jenom ty, které je potřeba přidat.

Pomocí následujících kroků můžete nakonfigurovat koncový bod služby na účet Azure Cosmos DB pomocí Azure PowerShell:  

1. Nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) a [Přihlaste se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Povolte koncový bod služby pro existující podsíť virtuální sítě.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Získat informace o virtuální síti

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Příprava pravidla Cosmos DB Virtual Network

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualizujte Azure Cosmos DB vlastností účtu novou konfigurací Virtual Network koncového bodu: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Spuštěním následujícího příkazu ověřte, že se účet Azure Cosmos DB aktualizoval pomocí koncového bodu služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurace koncového bodu služby pomocí rozhraní příkazového řádku Azure

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

Tato ukázka je určená k tomu, aby ukázala, jak připojit účet Azure Cosmos k existující nové virtuální síti, ve které ještě není nakonfigurovaná podsíť pro koncové body služby. To se provádí pomocí `--ignore-missing-vnet-service-endpoint` parametru. To umožňuje, aby se konfigurace účtu Cosmos dokončila bez chyby předtím, než se dokončí konfigurace do podsítě virtuální sítě. Po dokončení konfigurace podsítě bude účet Cosmos dostupný přes nakonfigurovanou podsíť.

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

## <a name="port-range-when-using-direct-mode"></a>Rozsah portů při použití přímého režimu

Pokud používáte koncové body služby s účtem Azure Cosmos prostřednictvím připojení k přímému režimu, musíte zajistit, aby byl otevřený rozsah portů TCP od 10000 do 20000.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrace z pravidla brány firewall protokolu IP do seznamu ACL virtuální sítě

Chcete-li migrovat účet Azure Cosmos DB pomocí pravidel brány firewall protokolu IP pro použití koncových bodů služby virtuální sítě, použijte následující postup.

Po nakonfigurování účtu Azure Cosmos DB pro koncový bod služby pro podsíť se požadavky z této podsítě odesílají do Azure Cosmos DB pomocí virtuální sítě a zdroje informací o zdroji podsítě namísto zdrojové veřejné IP adresy. Tyto požadavky již nebudou odpovídat filtru IP nakonfigurovanému na účtu Azure Cosmos DB, což je důvod, proč je nutné, abyste se vyhnuli výpadkům.

Než budete pokračovat, povolte koncový bod služby Azure Cosmos DB ve virtuální síti a podsíti pomocí kroku uvedeného výše v tématu Povolení koncového bodu služby pro existující podsíť virtuální sítě.

1. Získat informace o virtuální síti a podsíti:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Příprava nového objektu Virtual Network pravidla pro účet Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualizujte účet Azure Cosmos DB, aby se povolil přístup koncového bodu služby z podsítě:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Opakujte předchozí kroky pro všechny účty Azure Cosmos DB, ke kterým přistupovala z podsítě.

1. Odeberte pravidlo brány firewall protokolu IP pro podsíť z pravidel brány firewall účtu Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si článek [Podpora brány firewall](firewall-support.md) .
