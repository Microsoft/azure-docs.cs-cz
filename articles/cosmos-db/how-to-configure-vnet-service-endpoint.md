---
title: Konfigurace přístupu založeného na virtuální síti pro účet Azure Cosmos
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366229"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurace přístupu z virtuálních sítí (VNet)

Účty služby Azure Cosmos DB můžete nakonfigurovat tak, aby umožňovaly přístup pouze z konkrétní podsítě virtuální sítě Azure. Omezení přístupu k účtu Azure Cosmos DB pomocí připojení z podsítě ve virtuální síti:

1. Povolte podsíti odeslat identitu podsítě a virtuální sítě do Azure Cosmos DB. Toho lze dosáhnout povolením koncového bodu služby pro Azure Cosmos DB v konkrétní podsíti.

1. Přidejte pravidlo v účtu Azure Cosmos DB k určení podsítě jako zdroje, ze kterého je přístupk účtu.

> [!NOTE]
> Když je koncový bod služby pro váš účet Azure Cosmos DB povolen v podsíti, zdroj provozu, který dosáhne Azure Cosmos DB, se přepne z veřejné IP adresy do virtuální sítě a podsítě. Přepínání provozu platí pro všechny účty Azure Cosmos DB, které jsou přístupné z této podsítě. Pokud vaše účty Azure Cosmos DB mají bránu firewall založenou na PROTOKOLU IP, která tuto podsíť povoluje, požadavky z podsítě s podporou služby již neodpovídají pravidlům brány firewall IP a jsou odmítnuty.
>
> Další informace naleznete v postupech popsaných v [části Migrace z pravidla brány firewall IP do seznamu řízení přístupu virtuální sítě](#migrate-from-firewall-to-vnet) v tomto článku.

Následující části popisují, jak nakonfigurovat koncový bod virtuální síťové služby pro účet Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí portálu Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální síť a podsíť Azure

1. Z okna **Všechny prostředky** najděte účet Azure Cosmos DB, který chcete zabezpečit.

1. V nabídce nastavení vyberte **brány firewall a virtuální sítě** a zvolte povolení přístupu z vybraných **sítí**.

1. Pokud chcete udělit přístup k podsíti existující virtuální sítě, vyberte v části **Virtuální sítě**možnost Přidat existující virtuální **síť Azure**.

1. Vyberte **předplatné,** ze kterého chcete přidat virtuální síť Azure. Vyberte **virtuální sítě** a **podsítě** Azure, které chcete poskytnout přístup k účtu Azure Cosmos DB. Dále vyberte **Povolit,** chcete-li povolit vybrané sítě s koncovými body služby pro "Microsoft.AzureCosmosDB". Po dokončení vyberte **Přidat**.

   ![Výběr virtuální sítě a podsítě](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Po azure cosmos DB účet je povolen přístup z virtuální sítě, bude povolit provoz pouze z této vybrané podsítě. Přidaná virtuální síť a podsíť by se měla zobrazit tak, jak je znázorněno na následujícím snímku obrazovky:

   ![Úspěšně nakonfigurovaná virtuální síť a podsíť](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Chcete-li povolit koncové body služby virtuální sítě, potřebujete následující oprávnění předplatného:
>   * Předplatné s virtuální sítí: Síťový přispěvatel
>   * Předplatné s účtem Azure Cosmos DB: Přispěvatel účtu DocumentDB
>   * Pokud vaše virtuální síť a účet Azure Cosmos DB jsou v různých předplatných, ujistěte se, že předplatné, které má virtuální síť má `Microsoft.DocumentDB` také registrovaný poskytovatel prostředků. Pokud chcete zaregistrovat poskytovatele prostředků, přečtěte si článek [Poskytovatelé a typy prostředků Azure.](../azure-resource-manager/management/resource-providers-and-types.md)

Tady jsou pokyny pro registraci předplatného u poskytovatele prostředků.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. Z okna **Všechny prostředky** najděte účet Azure Cosmos DB, který chcete zabezpečit.  

1. V nabídce nastavení vyberte **brány firewall a virtuální sítě Azure** a zvolte povolení přístupu z vybraných **sítí**.  

1. Pokud chcete udělit přístup k nové virtuální síti Azure, vyberte v části **Virtuální sítě**možnost Přidat novou **virtuální síť**.  

1. Zadejte podrobnosti potřebné k vytvoření nové virtuální sítě a pak vyberte **Vytvořit**. Podsíť bude vytvořena s povoleným koncovým bodem služby pro "Microsoft.AzureCosmosDB".

   ![Výběr virtuální sítě a podsítě pro novou virtuální síť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Pokud váš účet Azure Cosmos DB používají jiné služby Azure, jako je Azure Cognitive Search, nebo se k němu přistupuje z analýzy Stream nebo Power BI, povolíte přístup výběrem **možnosti Přijmout připojení z globálních datových center Azure**.

Chcete-li zajistit, že máte přístup k metrikám Azure Cosmos DB z portálu, musíte povolit povolení přístupu z možností **portálu Azure.** Další informace o těchto možnostech naleznete v článku [Konfigurace brány firewall IP.](how-to-configure-firewall.md) Po povolení přístupu uložte nastavení výběrem **možnosti Uložit.**

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Odebrání virtuální sítě nebo podsítě

1. V okně **Všechny prostředky** najděte účet Azure Cosmos DB, pro který jste přiřadili koncové body služby.  

1. V nabídce nastavení vyberte **brány firewall a virtuální sítě.**  

1. Pokud chcete odebrat pravidlo virtuální sítě nebo podsítě, vyberte **...** vedle virtuální sítě nebo podsítě a vyberte **Odebrat**.

   ![Odebrání virtuální sítě](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure PowerShellu

> [!NOTE]
> Když používáte PowerShell nebo Azure CLI, nezapomeňte zadat úplný seznam IP filtrů a virtuálnísítě ACL v parametrech, ne jen ty, které je potřeba přidat.

Pomocí následujících kroků nakonfigurujte koncový bod služby pro účet Azure Cosmos DB pomocí Azure PowerShellu:  

1. Nainstalujte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) a [přihlaste se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

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

1. Získejte informace o virtuální síti.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Příprava pravidla virtuální sítě Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualizujte vlastnosti účtu Azure Cosmos DB pomocí nové konfigurace koncového bodu virtuální sítě: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Spusťte následující příkaz a ověřte, že váš účet Azure Cosmos DB je aktualizovaný koncovým bodem služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurace koncového bodu služby pomocí rozhraní příkazového příkazu Azure

Účty Azure Cosmos lze nakonfigurovat pro koncové body služby při jejich vytvoření nebo aktualizaci později, pokud je pro ně podsíť již nakonfigurovaná. Koncové body služby lze také povolit na účtu Cosmos, kde pro ně podsíť ještě není nakonfigurována, a pak začne fungovat, když je podsíť nakonfigurována později. Tato flexibilita umožňuje správcům, kteří nemají přístup k účtu Cosmos a prostředkům virtuální sítě, aby jejich konfigurace byly na sobě nezávislé.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Vytvoření nového účtu Cosmos a jeho připojení k back-endové podsíti pro novou virtuální síť

V tomto příkladu je virtuální síť a podsíť vytvořena s povolenými koncovými body služby pro obě, když jsou vytvořeny.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Nezávislé připojení a konfigurace účtu Cosmos k podsíti back-end

Tato ukázka je určena k zobrazení, jak připojit účet Azure Cosmos k existující nové virtuální síti, kde podsíť ještě není nakonfigurovaná pro koncové body služby. To se provádí `--ignore-missing-vnet-service-endpoint` pomocí parametru. To umožňuje konfiguraci účtu Cosmos dokončit bez chyby před dokončením konfigurace podsítě virtuální sítě. Po dokončení konfigurace podsítě bude účet Cosmos přístupný prostřednictvím nakonfigurované podsítě.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrace z pravidla brány firewall IP na seznam ACL virtuální sítě

Chcete-li migrovat účet Azure Cosmos DB z použití pravidel brány firewall IP na použití koncových bodů služby virtuální sítě, použijte následující kroky.

Po nakonfigurovaný účet služby Cosmos DB pro podsíť se požadavky z této podsítě odesílají do Azure Cosmos DB s informacemi o virtuální síti a zdroji podsítě namísto zdrojové veřejné IP adresy. Tyto požadavky již nebudou odpovídat filtru IP nakonfigurovanému na účtu Azure Cosmos DB, a proto jsou nezbytné následující kroky, aby se zabránilo prostojům.

Než budete pokračovat, povolte koncový bod služby Azure Cosmos DB ve virtuální síti a podsíti pomocí výše uvedeného kroku v části Povolení koncového bodu služby pro existující podsíť virtuální sítě.

1. Získání informací o virtuální síti a podsíti:

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

1. Příprava nového objektu pravidla virtuální sítě pro účet Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aktualizujte účet Azure Cosmos DB, abyste povolili přístup ke koncovému bodu služby z podsítě:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Opakujte předchozí kroky pro všechny účty Azure Cosmos DB přístupné z podsítě.

1. Odeberte pravidlo brány firewall IP pro podsíť z pravidel brány firewall účtu Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si článek [o podpoře brány firewall.](firewall-support.md)
