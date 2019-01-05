---
title: Konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos DB
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě pro službu Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 37cc7dcb3157459f6f6387323cc44d6f97954e92
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035810"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Přístup k prostředkům služby Azure Cosmos DB z virtuální sítě

Můžete nakonfigurovat účtům Azure Cosmos DB, pokud chcete povolit přístup pouze z konkrétní podsítě virtuální sítě Azure. Omezit přístup k účtu služby Azure Cosmos DB s připojeními z podsítě ve virtuální síti:
 
1. Povolte podsíť, kterou chcete odeslat podsítě a identitu virtuální sítě ke službě Azure Cosmos DB. Můžete dosáhnout tím, že koncový bod služby pro službu Azure Cosmos DB v konkrétní podsíti.

1. Přidáte pravidlo v účtu Azure Cosmos DB jako zdroje, ze kterého můžete přístupu k účtu zadat podsíť.

> [!NOTE]
> Pokud koncový bod služby pro váš účet Azure Cosmos DB je povolena v podsíti, přepne zdroje přenosů, kterou půjde používat službu Azure Cosmos DB z veřejné IP adresy pro virtuální síť a podsíť. Přepínání provoz se vztahuje na všechny účtu Azure Cosmos DB, která je přístupná z této podsítě. Pokud vaše účty služby Azure Cosmos DB brány firewall založené na protokolu IP umožňuje tato podsíť, požadavky z podsítě povolená služba už neodpovídá pravidla firewallu protokolu IP a jste odmítnut. 
>
> Další informace najdete v tématu podle kroků uvedených v [migrace z pravidla brány firewall protokolu IP na seznam řízení přístupu virtuální sítě](#migrate-from-firewall-to-vnet) části tohoto článku. 

Následující části popisují, jak nakonfigurovat koncový bod služby virtuální sítě pro účet služby Azure Cosmos DB.

## <a id="configure-using-portal"></a>Konfigurace koncového bodu služby pomocí webu Azure portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální sítě Azure a podsíť

1. Z **všechny prostředky** okno, hledání účtu služby Azure Cosmos DB, který chcete zabezpečit.

1. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení a pokud se rozhodnete povolit přístup z **vybrané sítě**.

1. K udělení přístupu k existující virtuální sítě pro podsítě, v části **virtuální sítě**vyberte **přidat existující virtuální sítě Azure**.

1. Vyberte **předplatné** ze kterého chcete přidat virtuální síť Azure. Vyberte Azure **virtuální sítě** a **podsítě** , že chcete poskytnout přístup k vašemu účtu Azure Cosmos DB. V dalším kroku vyberte **povolit** vybraných sítí s koncovými body služby povolit pro "Microsoft.AzureCosmosDB". Po dokončení vyberte **přidat**. 

   ![Vyberte virtuální síť a podsíť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Po povolení účtu Azure Cosmos DB pro přístup z virtuální sítě vám umožní provoz pouze tento vybrali podsíť. Virtuální síť a podsíť, která jste přidali by měl vypadat, jak je znázorněno na následujícím snímku obrazovky:

   ![virtuální síť a podsíť se úspěšně nakonfiguroval](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Pokud chcete povolit koncové body služeb virtuální sítě, potřebujete následující oprávnění pro předplatné:
  * Předplatné s virtuální sítí: Přispěvatel sítě
  * Předplatné s účtem služby Azure Cosmos DB: Přispěvatel účtů DocumentDB

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. Z **všechny prostředky** okno, hledání účtu služby Azure Cosmos DB, který chcete zabezpečit.  

1. Vyberte **brány firewall a virtuální sítě Azure** z nabídky nastavení a pokud se rozhodnete povolit přístup z **vybrané sítě**.  

1. K udělení přístupu k novou virtuální síť Azure, v části **virtuální sítě**vyberte **přidat novou virtuální síť**.  

1. Zadejte podrobnosti muset vytvořit novou virtuální síť a pak vyberte **vytvořit**. Podsíť se vytvoří s koncovým bodem služby pro "Microsoft.AzureCosmosDB" povolena.

   ![Vyberte virtuální síť a podsíť pro novou virtuální síť](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Pokud váš účet Azure Cosmos DB je používán dalšími službami Azure, jako je Azure Search nebo je přístupná ze Stream analytics nebo Power BI, můžete povolit přístup tak, že vyberete **přijímají připojení z v rámci veřejných datových centrech Azure**.

Aby bylo zajištěno, že máte přístup k metrikám služby Azure Cosmos DB z portálu, je potřeba povolit **povolit přístup z portálu Azure portal** možnosti. Další informace o těchto možnostech najdete v tématu [konfigurovat bránu firewall IP](how-to-configure-firewall.md) článku. Když povolíte přístup, vyberte **Uložit** uložte nastavení.

## <a id="remove-vnet-or-subnet"></a>Odebrat virtuální sítě nebo podsítě 

1. Z **všechny prostředky** okno, pro který jste přiřadili koncové body služby účet najít rozhraní Azure Cosmos DB.  

2. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení.  

3. Odebrat virtuální sítě nebo podsítě pravidla, vyberte **...**  vedle virtuální síť nebo podsítě a vyberte **odebrat**.

   ![Odebrat virtuální sítě](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Vyberte **Uložit** změny.

## <a id="configure-using-powershell"></a>Konfigurace koncového bodu služby pomocí Azure Powershellu

> [!NOTE]
> Pokud používáte PowerShell nebo rozhraní příkazového řádku Azure, nezapomeňte zadat úplný seznam filtrů IP adres a virtuální sítě seznamy ACL v parametrech, nejen ty, které je potřeba přidat.

Konfigurace koncového bodu služby do účtu služby Azure Cosmos DB pomocí Azure Powershellu, použijte následující kroky:  

1. Nainstalujte [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

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

1. Získáte informace o virtuální síti.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Získání vlastností účtu služby Azure Cosmos DB spuštěním následující rutiny:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

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

1. Aktualizujte vlastnosti účtu služby Azure Cosmos DB s novou konfigurací spuštěním následující rutiny: 

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

1. Spusťte následující příkaz k ověření, že váš účet Azure Cosmos DB se aktualizuje se koncový bod služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

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

1. Aktualizujte existující účet služby Azure Cosmos DB pomocí podsítě seznamů řízení přístupu (ACL).

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Vytvořte nový účet Azure Cosmos DB s podsítí seznamy ACL.

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

## <a id="migrate-from-firewall-to-vnet"></a>Migrace z pravidla brány firewall protokolu IP pro virtuální sítě ACL 

Pomocí následujícího postupu pouze pro účty Azure Cosmos DB s existující pravidla firewallu protokolu IP, které umožňuje podsítě, v případě, že chcete použít virtuální síť a seznamy řízení přístupu na základě podsítě místo pravidla brány firewall protokolu IP.

Po zapnutí koncový bod služby pro účet služby Azure Cosmos DB pro určitou podsíť, požadavky se posílají se zdrojem, který obsahuje virtuální síť a podsíť informace místo veřejnou IP adresu. Tyto požadavky se neshodují filtr IP. Pro všechny účty služby Azure Cosmos DB k němu přistupovat z podsítě pomocí povolený koncový bod služby se stane tento přepínač zdroje. Pokud chcete zabránit výpadku, postupujte následovně:

1. Získání vlastností účtu služby Azure Cosmos DB spuštěním následující rutiny:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicializace proměnné pro pozdější použití. Nastavte všechny proměnné z existující definice účtu. Přidat virtuální síť seznam ACL ke všem účtům Azure Cosmos DB současný přístup z podsítě s `ignoreMissingVNetServiceEndpoint` příznak.

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

1. Aktualizujte vlastnosti účtu služby Azure Cosmos DB s novou konfigurací spuštěním následující rutiny:

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

1. Opakujte kroky 1-3 pro všechny účty služby Azure Cosmos DB, které lze používat z podsítě.

1.  Počkejte 15 minut a pak aktualizujte podsíť, kterou chcete povolit koncový bod služby.

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

* Konfigurace brány firewall pro službu Azure Cosmos DB, najdete v článku [podpora brány Firewall](firewall-support.md) článku.
