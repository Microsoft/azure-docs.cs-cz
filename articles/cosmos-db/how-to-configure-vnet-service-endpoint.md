---
title: Konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě služby Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 16cd959a83850a3bc940803cd23e7542e34825c8
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283208"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Jak získat přístup k prostředkům služby Azure Cosmos DB z virtuální sítě

Účty Azure cosmos DB můžete nakonfigurována pro povolení přístupu jenom z konkrétní podsítě virtuální sítě Azure. Existují dva kroky potřebné k omezení přístupu k účtu Azure Cosmos se připojení z podsítě ve virtuální síti (VNET).
 
1. Povolte podsíť, kterou chcete odeslat podsítě a identitu virtuální sítě ke službě Azure Cosmos DB. Můžete dosáhnout tím, že koncový bod služby pro službu Azure Cosmos DB v konkrétní podsíti.

1. Přidat pravidlo v účtu Azure Cosmos zadání podsítě jako zdroj, ze kterého, přístupu k účtu.

> [!NOTE]
> Jednou koncový bod služby pro vaši službu Azure Cosmos je povolen v podsíti, zdroje přenosů dosažení služby Azure Cosmos DB se přepne z veřejné IP adresy do virtuální sítě a podsítě. Přepínání přenosy platí pro všechny účty Azure Cosmos současný přístup z této podsítě. Pokud vašim účtům Azure Cosmos na základě IP adresy brány firewall umožňující tuto podsíť, požádá povolenou službu podsítě už neodpovídá pravidla firewallu protokolu IP a byly zamítnuty. Další informace najdete v tématu podle kroků uvedených v [migrace z pravidlo brány firewall protokolu IP do seznamu řízení přístupu k virtuální síti](#migrate-from-firewall-to-vnet) části tohoto článku. 

Následující části popisují, jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos.

## <a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí webu Azure portal

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Nakonfigurujte koncový bod služby pro existující virtuální sítě Azure a podsíť

1. Z **všechny prostředky** okně hledání účtu Azure Cosmos, který jste chcete zabezpečit.

1. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení a zvolte možnost povolit přístup z **vybrané sítě**.

1. K udělení přístupu k existující virtuální sítě pro podsítě, v části **virtuální sítě**vyberte **přidat existující virtuální sítě Azure**.

1. Vyberte **předplatné** ze kterého chcete přidat virtuální síť Azure. Vyberte Azure **virtuální sítě** a **podsítě** , které chcete poskytnout přístup k vašemu účtu Azure Cosmos. Dále vyberte **povolit** vybraných sítí s koncovými body služby povolit pro "Microsoft.AzureCosmosDB". Po dokončení vyberte **přidat**. 

   ![Vyberte virtuální síť a podsíť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Po povolení účtu Azure Cosmos pro přístup k virtuální síti se povoluje pouze provoz z to vybrali podsíť. Virtuální síť a podsíť, ve které jste přidali by měl vypadat, jak je znázorněno na následujícím snímku obrazovky:

   ![virtuální síť a podsíť se úspěšně nakonfiguroval](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Pokud chcete povolit koncové body služeb virtuální sítě, potřebujete následující oprávnění pro předplatné:
  * Předplatné s virtuální sítí: Přispěvatel sítě
  * Předplatné se účet služby Azure Cosmos: Přispěvatel účtů DocumentDB

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu service pro novou virtuální síť Azure a podsíť

1. Z **všechny prostředky** okně hledání účtu Azure Cosmos, který jste chcete zabezpečit.

1. Vyberte **brány firewall a virtuální sítě Azure** z nabídky nastavení a zvolte možnost povolit přístup z **vybrané sítě**.  

1. Chcete-li udělit přístup k novou virtuální síť Azure, v rámci virtuální sítě, vyberte **přidat novou virtuální síť**.  

1. Zadejte podrobnosti si muset vytvořit novou virtuální síť a potom vyberte možnost vytvořit. Podsíť se vytvoří s koncovým bodem služby pro "Microsoft.AzureCosmosDB" povolena.

   ![Vyberte virtuální síť a podsíť pro novou virtuální síť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Pokud váš účet Azure Cosmos je používaný dalšími službami Azure, jako je Azure Search nebo k němu přistupovat z Stream analytics nebo Power BI, můžete povolit přístup tak, že zkontrolujete **přijímají připojení z v rámci veřejných datových centrech Azure**.

Aby bylo zajištěno, budete mít přístup k Azure Cosmos DB metriky z portálu, je potřeba povolit **povolit přístup z portálu Azure Portal** možnosti. Další informace o těchto možnostech najdete v tématu požadavky z webu Azure portal a požadavek z oddílů služby Azure PaaS konfigurací [brány firewall protokolu IP](how-to-configure-firewall.md) článku. Po výběru přístup, vyberte **Uložit** uložte nastavení.

## <a id="remove-vnet-or-subnet"></a>Odebrat virtuální sítě nebo podsítě 

1. Z **všechny prostředky** okně Najít účet Azure Cosmos, pro který jste přiřadili koncových bodů služby.  

2. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení.  

3. Odebrat virtuální sítě nebo podsítě pravidla, vyberte "..." vedle virtuální síť nebo podsítě a vyberte **odebrat**.

   ![Odebrat virtuální sítě](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Klikněte na tlačítko **Uložit** změny.

## <a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure Powershellu

> [!NOTE]
> Při použití prostředí PowerShell nebo rozhraní příkazového řádku, je potřeba zadat úplný seznam filtrů IP adres a virtuální sítě ACL v parametrech, nejen ty, které je potřeba přidat.

Použijte následující postup ke konfiguraci koncového bodu služby k účtu Azure Cosmos pomocí Azure Powershellu:  

1. Nainstalujte nejnovější [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Povolte koncový bod služby pro existující podsítě virtuální sítě.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Získejte informace o virtuální síti.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Získání vlastností účtu Azure Cosmos spuštěním následující rutiny:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicializace proměnné pro pozdější použití. Nastavte všechny proměnné z existující definice účtu.

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

1. Aktualizujte vlastnosti účtu Azure Cosmos s novou konfigurací spuštěním následující rutiny: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Spusťte následující příkaz k ověření, že váš účet Azure Cosmos je aktualizováno koncový bod služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurace koncového bodu služby pomocí rozhraní příkazového řádku Azure 

1. Povolte koncový bod služby pro podsíť ve virtuální síti.

1. Aktualizovat stávající účet služby Azure Cosmos s podsítí seznamy ACL

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Vytvořit nový účet Azure Cosmos s podsítí seznamy ACL

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrace z pravidlo brány firewall protokolu IP pro virtuální sítě ACL 

Následující kroky jsou zapotřebí pouze pro účty služby Azure Cosmos pomocí existujícího pravidla firewallu protokolu IP umožňuje podsíť a chcete použít virtuální síť a seznamy řízení přístupu na základě podsítě místo pravidlo brány firewall protokolu IP.

Jakmile se koncový bod služby pro účet služby Azure Cosmos je zapnuté pro určitou podsíť, jsou odesílány požadavky s zdroje, který obsahuje informace o virtuální síť a podsíť namísto veřejná IP adresa. Proto tyto žádosti se neshodují s filtr IP. Pro všechny účty Azure Cosmos k němu přistupovat z podsítě pomocí povolený koncový bod služby se stane tento přepínač zdroje. Aby nedošlo k výpadku, postupujte následovně:

1. Získání vlastností účtu Azure Cosmos spuštěním následující rutiny:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicializace proměnné pro pozdější použití. Nastavte všechny proměnné z existující definice účtu. Přidejte seznam ACL virtuální sítě do všechny služby Azure Cosmos účtům současný přístup z podsítě s `ignoreMissingVNetServiceEndpoint` příznak.

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

1. Aktualizujte vlastnosti účtu Azure Cosmos s novou konfigurací spuštěním následující rutiny:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Opakujte kroky 1-3 pro všechny účty služby Azure Cosmos, ke kterým přistupujete z podsítě.

1.  Počkejte 15 minut a pak aktualizujte podsítě povolit koncový bod služby.

1.  Povolte koncový bod služby pro existující podsítě virtuální sítě.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
      -ResourceGroupName $rgname `
      -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
      -Name $sname `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Odeberte pravidlo brány firewall protokolu IP pro podsíť.

## <a name="next-steps"></a>Další postup

* Konfigurace brány firewall pro službu Azure Cosmos DB, najdete v článku [podpora brány firewall](firewall-support.md) článku.
