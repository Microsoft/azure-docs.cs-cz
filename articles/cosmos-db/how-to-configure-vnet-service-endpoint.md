---
title: Konfigurace přístupu na základě virtuální sítě pro účet Azure Cosmos
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě pro Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637179"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Konfigurace přístupu k Azure Cosmos DB z virtuálních sítí (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Účet Azure Cosmos můžete nakonfigurovat tak, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Povolením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro přístup k Azure Cosmos DB v podsíti ve virtuální síti se přenos z této podsítě pošle Azure Cosmos DB s identitou podsítě a Virtual Network. Po povolení koncového bodu služby Azure Cosmos DB můžete omezit přístup k podsíti tím, že ji přidáte do svého účtu Azure Cosmos.

Ve výchozím nastavení je účet Azure Cosmos přístupný z libovolného zdroje, pokud je žádost doprovázena platným autorizačním tokenem. Když do virtuální sítě přidáte jednu nebo víc podsítí, budou se v rámci požadavků, které pocházejí z těchto podsítí, získat platná odpověď. Žádosti pocházející z jakéhokoliv jiného zdroje obdrží odpověď 403 (zakázáno). 

Účty služby Azure Cosmos DB můžete nakonfigurovat tak, aby umožňovaly přístup pouze z konkrétní podsítě virtuální sítě Azure. Chcete-li omezit přístup k účtu Azure Cosmos DB s připojením z podsítě ve virtuální síti:

1. Umožněte, aby podsíť odesílala podsíť a identitu virtuální sítě do Azure Cosmos DB. To můžete dosáhnout povolením koncového bodu služby pro Azure Cosmos DB v konkrétní podsíti.

1. Přidejte do účtu Azure Cosmos DB pravidlo, abyste mohli určit podsíť jako zdroj, ze kterého je účet k účtu k dispozici.

> [!NOTE]
> Pokud je v podsíti povolen koncový bod služby pro váš Azure Cosmos DB účet, zdroj provozu, který dosáhne Azure Cosmos DB přepínačů z veřejné IP adresy do virtuální sítě a podsítě. Přepínání přenosů se vztahuje na libovolný účet Azure Cosmos DB, který je z této podsítě k dispozici. Pokud vaše účty Azure Cosmos DB mají bránu firewall založenou na protokolu IP, která by umožňovala tuto podsíť, požadavky z podsítě s povolenými službami už nebudou odpovídat pravidlům brány firewall protokolu IP a budou odmítnuty.
>
> Další informace najdete v části Postup [migrace z pravidla brány firewall protokolu IP na virtuální síť](#migrate-from-firewall-to-vnet) v tomto článku.

Následující části popisují, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos DB.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.

1. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** a zvolte možnost povolení přístupu z **vybraných sítí**.

1. Pokud chcete udělit přístup k podsíti existující virtuální sítě, vyberte v části **virtuální sítě** možnost **Přidat existující virtuální síť Azure**.

1. Vyberte **předplatné** , ze kterého chcete přidat virtuální síť Azure. Vyberte **virtuální sítě** a **podsítě** Azure, které chcete poskytnout přístup k vašemu Azure Cosmos DB účtu. V dalším kroku vyberte **Povolit** a povolte vybrané sítě s koncovými body služby pro Microsoft. AzureCosmosDB. Až se dokončí, vyberte **Přidat**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Vybrat virtuální síť a podsíť":::

1. Až bude účet Azure Cosmos DB povolený pro přístup z virtuální sítě, povolí provoz jenom z této zvolené podsítě. Virtuální síť a podsíť, kterou jste přidali, by se měla zobrazit, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Virtuální síť a podsíť se úspěšně nakonfigurovaly.":::

> [!NOTE]
> K povolení koncových bodů služby virtuální sítě budete potřebovat následující oprávnění předplatného:
>   * Předplatné s virtuální sítí: Přispěvatel sítě
>   * Předplatné s Azure Cosmos DBm účtem: Přispěvatel účtu DocumentDB
>   * Pokud je vaše virtuální síť a účet Azure Cosmos DB v různých předplatných, ujistěte se, že odběr, který má virtuální síť, má taky `Microsoft.DocumentDB` zaregistrovaný poskytovatel prostředků. Pokud chcete zaregistrovat poskytovatele prostředků, přečtěte si článek [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Tady jsou pokyny pro registraci předplatného u poskytovatele prostředků.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. V okně **všechny prostředky** Najděte Azure Cosmos DB účet, který chcete zabezpečit.  

1. V nabídce nastavení vyberte **brány firewall a Azure Virtual Networks** a zvolte možnost povolení přístupu z **vybraných sítí**.  

1. Pokud chcete udělit přístup k nové službě Azure Virtual Network, vyberte v části **virtuální sítě** možnost **Přidat novou virtuální síť**.  

1. Zadejte podrobnosti požadované k vytvoření nové virtuální sítě a pak vyberte **vytvořit**. Podsíť se vytvoří s koncovým bodem služby pro Microsoft. AzureCosmosDB, který je povolený.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Vyberte virtuální síť a podsíť pro novou virtuální síť.":::

Pokud váš Azure Cosmos DB účet používají jiné služby Azure, jako je Azure Kognitivní hledání nebo ke kterému se přistupuje ze služby Stream Analytics nebo Power BI, povolíte přístup tak, že **v globálním datacentru Azure vyberete přijímat připojení**.

Abyste měli jistotu, že máte přístup k Azure Cosmos DB metriky z portálu, musíte povolit možnost **Povolit přístup z možností Azure Portal** . Další informace o těchto možnostech najdete v článku [Konfigurace brány firewall protokolu IP](how-to-configure-firewall.md) . Po povolení přístupu vyberte **Uložit** a uložte nastavení.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Odebrat virtuální síť nebo podsíť

1. V okně **všechny prostředky** najděte účet Azure Cosmos DB, pro který jste přiřadili koncové body služby.  

1. V nabídce nastavení vyberte možnost **brány firewall a virtuální sítě** .  

1. Chcete-li odebrat virtuální síť nebo pravidlo podsítě, vyberte **...** vedle virtuální sítě nebo podsítě a vyberte možnost **Odebrat**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Odebrání virtuální sítě":::

1. Vyberte **Uložit** , aby se tyto změny použily.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pomocí následujících kroků můžete nakonfigurovat koncový bod služby na účet Azure Cosmos DB pomocí Azure PowerShell:  

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [Přihlaste se](/powershell/azure/authenticate-azureps).  

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

   > [!NOTE]
   > Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure CLI, nezapomeňte v parametrech zadat úplný seznam filtrů IP adres a seznamů ACL virtuální sítě v parametrech, nikoli jenom ty, které je potřeba přidat.

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

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tady jsou některé nejčastější dotazy týkající se konfigurace přístupu z virtuálních sítí:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Jsou poznámkové bloky a prostředí Mongo/Cassandra aktuálně kompatibilní s účty s povoleným Virtual Network?

V současné době se [Mongo prostředí PowerShellu](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) a [prostředí Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) ve službě Cosmos DB Průzkumník dat a [Služba Jupyter poznámkových bloků](./cosmosdb-jupyter-notebooks.md)nepodporuje s přístupem VNET. Toto je aktuálně aktivní vývoj.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Můžu v účtu Azure Cosmos zadat jak koncový bod služby virtuální sítě, tak i zásady řízení přístupu IP? 

V účtu Azure Cosmos můžete povolit koncový bod služby virtuální sítě a zásadu řízení přístupu IP (označovanou taky jako brána firewall). Tyto dvě funkce jsou doplňující a společně zajišťují izolaci a zabezpečení účtu Azure Cosmos. Použití brány firewall protokolu IP zajišťuje, že ke svému účtu budou mít přístup statické IP adresy. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Návody omezit přístup k podsíti v rámci virtuální sítě? 

K omezení přístupu k účtu Azure Cosmos z podsítě se vyžadují dva kroky. Nejdřív povolíte provoz z podsítě, aby se mohla Azure Cosmos DBa identita vaší podsítě a virtuální sítě. To se provádí povolením koncového bodu služby pro Azure Cosmos DB v podsíti. V dalším kroku se přidá pravidlo do účtu Azure Cosmos, který určuje tuto podsíť jako zdroj, ze kterého se dá dostat k účtu.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Budou seznamy řízení přístupu k virtuální síti a žádosti o připojení brány firewall protokolu IP odmítnuty? 

Když se přidají pravidla přístupu k bránám firewall nebo k virtuální síti, žádosti z povolených zdrojů získají platné odpovědi. Jiné požadavky se odmítnou s 403 (zakázáno). Bránu firewall účtu Azure Cosmos je důležité odlišit od brány firewall na úrovni připojení. Zdroj se stále může ke službě připojit a samotné připojení se neodmítlo.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Když se povolí koncový bod služby Azure Cosmos DB v podsíti, začaly se zablokovat moje žádosti. Co se stalo?

Jakmile je koncový bod služby pro Azure Cosmos DB v podsíti povolený, zdroj provozu, který přiblíží účtu, se přepne z veřejné IP adresy do virtuální sítě a podsítě. Pokud má váš účet Azure Cosmos jenom bránu firewall založenou na protokolu IP, provoz z podsítě s povolenými službami už nebude odpovídat pravidlům brány firewall protokolu IP a proto se odmítne. Přečtěte si postup plynule migrace z brány firewall založené na protokolu IP na řízení přístupu na základě virtuální sítě.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Jsou pro účty Azure Cosmos s koncovými body služby virtuální sítě potřeba další oprávnění Azure RBAC?

Po přidání koncových bodů služby virtuální sítě do účtu Azure Cosmos budete potřebovat přístup k `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` akci pro všechny virtuální sítě nakonfigurované na vašem účtu Azure Cosmos. Toto oprávnění je povinné, protože proces autorizace ověřuje přístup k prostředkům (například k prostředkům databáze a virtuální sítě) před vyhodnocením jakýchkoli vlastností.
 
Autorizace ověřuje oprávnění prostředku virtuální sítě i v případě, že uživatel nezadá seznamy ACL virtuální sítě pomocí rozhraní příkazového řádku Azure CLI. V současné době řídicí plocha účtu Azure Cosmos podporuje nastavení kompletního stavu účtu Azure Cosmos. Jeden z parametrů pro volání řídicí roviny je `virtualNetworkRules` . Pokud tento parametr nezadáte, Azure CLI vytvoří volání metody Get Database, které načte `virtualNetworkRules` a použije tuto hodnotu v volání aktualizace.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Mají partnerské virtuální sítě taky přístup k účtu Azure Cosmos? 
Jenom virtuální síť a jejich podsítě přidávané k účtu Azure Cosmos mají přístup. Jejich partnerský virtuální sítě nemá přístup k účtu, dokud nebudou do účtu přidány podsítě v rámci partnerských virtuálních sítí.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaký je maximální počet podsítí s povoleným přístupem k jednomu účtu Cosmos? 
V současné době můžete mít pro účet Azure Cosmos povolený maximálně 256 podsítí.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Můžu povolit přístup z sítě VPN a Express Route? 
Pro přístup k účtu Azure Cosmos přes Express Route z místního prostředí budete muset povolit partnerské vztahy Microsoftu. Po umístění brány firewall protokolu IP nebo pravidla přístupu k virtuální síti můžete přidat veřejné IP adresy používané pro partnerský vztah Microsoftu na svém účtu brány firewall Azure Cosmos, aby místní služby mohly přistupovat k účtu Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Potřebuji aktualizovat pravidla skupin zabezpečení sítě (NSG)? 
Pravidla NSG se používají k omezení připojení k podsíti s virtuální sítí a z ní. Když do podsítě přidáte koncový bod služby pro Azure Cosmos DB, není nutné otevírat odchozí připojení v NSG pro váš účet Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Jsou k dispozici koncové body služby pro všechny virtuální sítě?
Ne, u koncového bodu služby můžou být povolené jenom Azure Resource Manager virtuální sítě. Klasické virtuální sítě nepodporují koncové body služby.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Můžu přijmout připojení z veřejných datacenter Azure, když je povolený koncový bod služby Access pro Azure Cosmos DB?  
To se vyžaduje jenom v případě, že chcete, aby k účtu Azure Cosmos DB přistupovaly jiné služby Azure, jako je Azure Data Factory, Azure Kognitivní hledání nebo jakákoli služba nasazená v dané oblasti Azure.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nakonfigurovat bránu firewall pro Azure Cosmos DB, přečtěte si článek [Podpora brány firewall](how-to-configure-firewall.md) .
