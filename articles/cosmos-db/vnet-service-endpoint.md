---
title: Zabezpečený přístup k účtu služby Azure Cosmos DB s využitím koncového bodu služby virtuální sítě Azure | Dokumentace Microsoftu
description: Tento dokument popisuje kroky potřebné k nastavení koncového bodu služby virtuální sítě služby Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: a4758e5597876112fa7a85850786491e22af8c83
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037131"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Zabezpečený přístup k účtu služby Azure Cosmos DB s využitím koncového bodu služby virtuální sítě Azure

Účty Azure cosmos DB můžete nakonfigurována pro povolení přístupu jenom z konkrétní podsítě virtuální sítě Azure. Tím, že [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro Azure cosmos DB z virtuální sítě a podsítě, provoz je zajištěn optimální a bezpečný postup služby Azure Cosmos DB.  

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů. Data v účtu Azure Cosmos DB můžete replikovat do více oblastí. Azure Cosmos DB je nakonfigurována s koncový bod služby virtuální sítě, každá virtuální síť umožňuje přístup z IP adres, který patří do podsítě specicifc. Následující obrázek znázorňuje ukázku služby Azure Cosmos DB, který má povolený koncový bod služby virtuální sítě:

![Architektura koncový bod služby virtuální sítě](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Jakmile účet služby Azure Cosmos DB má nakonfigurovanou koncový bod služby virtuální sítě, je přístupný pouze z Zadaná podsíť, se odeberou všechny veřejné/internetový přístup. Výuku v kurzech mooc podrobné o koncových bodech služeb najdete na Azure [přehled koncových bodů služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) článku.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurace koncového bodu služby pomocí webu Azure portal
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Nakonfigurujte koncový bod služby pro existující virtuální sítě Azure a podsíť

1. Z **všechny prostředky** okně Najít virtuální sítě, kterou chcete nakonfigurovat koncový bod služby pro službu Azure Cosmos DB. Přejděte **koncové body služby** blade a ujistěte se, že podsíť ve virtuální síti se povolila pro koncový bod služby "Azure.CosmosDB".  

   ![Potvrďte povolený koncový bod služby](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Z **všechny prostředky** okně hledání účtu služby Azure Cosmos DB, který jste chcete zabezpečit.  

3. Než povolíte koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojenou s vaším účtem Azure Cosmos DB pro budoucí použití. Brány firewall protokolu IP můžete znovu povolit po dokončení konfigurace koncového bodu služby.  

4. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení a zvolte možnost povolit přístup z **vybrané sítě**.  

3. Chcete-li udělit přístup k existující virtuální sítě pro podsítě v rámci virtuální sítě, vyberte **přidat existující virtuální sítě Azure**.  

4. Vyberte **předplatné** ze kterého chcete přidat virtuální síť Azure. Vyberte Azure **virtuální sítě** a **podsítě** , které chcete poskytnout přístup k vašemu účtu Azure Cosmos DB. Dále vyberte **povolit** vybraných sítí s koncovými body služby povolit pro "Microsoft.AzureCosmosDB". Po dokončení vyberte **přidat**.  

   ![Vyberte virtuální síť a podsíť](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Pokud koncový bod služby pro službu Azure Cosmos DB se dříve nakonfigurované pro vybrané virtuální sítě Azure a podsítě, můžete nakonfigurovat jako součást této operace. Povolení přístupu bude trvat až 15 minut. Je velmi důležité po poznamenat obsah brány firewall seznamu ACL pro renabling je později zakázat brány firewall protokolu IP. 

   ![virtuální síť a podsíť se úspěšně nakonfiguroval](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Nyní bude účtu služby Azure Cosmos DB povoluje pouze provoz z to vybrali podsíť. Pokud jste dříve měli povolili brány firewall protokolu IP, znovu povolte je prosím pomocí dřívější informace.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu service pro novou virtuální síť Azure a podsíť

1. Z **všechny prostředky** okně hledání účtu služby Azure Cosmos DB, který jste chcete zabezpečit.  

> [!NOTE]
> Pokud máte existující brány firewall protokolu IP nakonfigurovaný pro váš účet Azure Cosmos DB, mějte prosím na paměti konfigurace brány firewall, odeberte brány firewall protokolu IP a potom povolit koncový bod služby. Pokud povolíte koncový bod služby bez disbling bránu firewall, provoz z tohoto rozsahu ip budou ztraceny virtuální IP identit a se ukončí s chybovou zprávou filtru IP. Takže k této chybě zabránit zakážete vždy pravidla brány firewall, je zkopírovat, povolit koncový bod služby z podsítě a nakonec ACL podsítě ze služby Cosmos DB. Po konfiguraci koncového bodu služby a přidávání seznamu ACL můžete znovu povolit brány firewall protokolu IP znovu v případě potřeby.

2. Než povolíte koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojenou s vaším účtem Azure Cosmos DB pro budoucí použití. Brány firewall protokolu IP můžete znovu povolit po dokončení konfigurace koncového bodu služby.  

3. Vyberte **brány firewall a virtuální sítě Azure** z nabídky nastavení a zvolte možnost povolit přístup z **vybrané sítě**.  

4. Chcete-li udělit přístup k novou virtuální síť Azure, v rámci virtuální sítě, vyberte **přidat novou virtuální síť**.  

5. Zadejte podrobnosti si muset vytvořit novou virtuální síť a potom vyberte možnost vytvořit. Podsíť se vytvoří s koncovým bodem služby pro "Microsoft.AzureCosmosDB" povolena.

   ![Vyberte virtuální síť a podsíť pro novou virtuální síť](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Povolit přístup z portálu Azure portal

Po koncových bodů služby Azure Virtual Network jsou povoleny pro váš účet databáze Azure Cosmos DB, přístup z portálu nebo jiných služeb Azure je ve výchozím nastavení zakázaný. Přístup k vašemu účtu databáze Azure Cosmos DB z počítače mimo nakonfigurovaný podsítě jsou blokovány, včetně přístupu z portálu.

![Povolit přístup z portálu](./media/vnet-service-endpoint/allow-access-from-portal.png)

Pokud váš účet Azure Cosmos DB je používané jinými službami Azure, jako je Azure Search nebo k němu přistupovat z Stream analytics nebo Power BI, můžete povolit přístup tak, že zkontrolujete **povolit přístup ke službám Azure**.

Aby bylo zajištěno, budete mít přístup k Azure Cosmos DB metriky z portálu, je potřeba povolit **povolit přístup k webu Azure portal** možnosti. Další informace o těchto možnostech najdete v tématu [připojení z webu Azure portal](firewall-support.md#connections-from-the-azure-portal) a [připojení ze služeb Azure PaaS](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) oddíly. Po výběru přístup, vyberte **Uložit** uložte nastavení.

## <a name="remove-a-virtual-network-or-subnet"></a>Odebrat virtuální sítě nebo podsítě 

1. Z **všechny prostředky** okno, pro který jste přiřadili koncové body služby účet najít rozhraní Azure Cosmos DB.  

2. Vyberte **virtuální sítí a bran firewall** z nabídky nastavení.  

3. Odebrat virtuální sítě nebo podsítě pravidla, vyberte "..." vedle virtuální síť nebo podsítě a vyberte **odebrat**.

   ![Odebrat virtuální sítě](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Klikněte na tlačítko **Uložit** změny.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurace koncového bodu služby pomocí Azure Powershellu 

Následujícím postupem nakonfigurujte koncový bod služby na účet služby Azure Cosmos DB pomocí Azure Powershellu:  

1. Nainstalujte nejnovější [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Zajistěte, pamatujte nastavení brány firewall protokolu IP a zcela odstranit brány firewall protokolu IP, než povolíte koncový bod služby pro účet.


> [!NOTE]
> Pokud máte existující brány firewall protokolu IP nakonfigurovaný pro váš účet Azure Cosmos DB, mějte prosím na paměti konfigurace brány firewall, odeberte brány firewall protokolu IP a potom povolit koncový bod služby. Pokud povolíte koncový bod služby bez disbling bránu firewall, provoz z tohoto rozsahu ip budou ztraceny virtuální IP identit a se ukončí s chybovou zprávou filtru IP. Takže k této chybě zabránit zakážete vždy pravidla brány firewall, je zkopírovat, povolit koncový bod služby z podsítě a nakonec ACL podsítě ze služby Cosmos DB. Po konfiguraci koncového bodu služby a přidávání seznamu ACL můžete znovu povolit brány firewall protokolu IP znovu v případě potřeby.

2. Než povolíte koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojenou s vaším účtem Azure Cosmos DB pro budoucí použití. Brány firewall protokolu IP se znovu povolit po dokončení konfigurace koncového bodu služby.  

3. Povolte koncový bod služby pro existující podsítě virtuální sítě.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Příprava k povolení seznamu ACL na účet služby cosmos DB tak, že se, že virtuální síť a podsíť pro službu Azure Cosmos DB je povolený koncový bod služby.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Získání vlastností účtu služby Azure Cosmos DB spuštěním následující rutiny:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicializace proměnné pro pozdější použití. Nastavit všechny proměnné z existující definice účtu, pokud máte více míst, musíte je přidat jako součást pole. V tomto kroku můžete také nakonfigurovat koncový bod služby virtuální sítě tak, že nastavíte proměnné "accountVNETFilterEnabled" na "True". Tato hodnota je později přiřazené k parametru "isVirtualNetworkFilterEnabled".  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Aktualizujte vlastnosti účtu služby Azure Cosmos DB s novou konfigurací spuštěním následující rutiny: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Spusťte následující příkaz k ověření, že váš účet Azure Cosmos DB se aktualizuje se koncový bod služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Přidat koncový bod služby virtuální sítě pro účet služby Azure Cosmos DB, která má povolenou bránu Firewall protokolu IP

1. Nejprve zakažte IP brány firewall, přístup k účtu Azure Cosmos DB.  

2. Povolte koncový bod virtuální sítě k účtu Azure Cosmos DB pomocí jedné z metod popsaných v předchozích částech.  

3. Povolte přístup přes bránu firewall protokolu IP. 

## <a name="test-the-access"></a>Testování přístupu

Pokud chcete zkontrolovat, pokud jsou nakonfigurované koncové body služby pro službu Azure Cosmos DB, podle očekávání, postupujte následovně:

* Nastavte dvě podsítě ve virtuální síti jako front-endových a back-endu, povolit koncový bod služby Cosmos DB pro podsíť back-endu.  

* Povolení přístupu k účtu služby Cosmos DB pro podsíť back-endu.  

* Vytvoření dvou virtuálních počítačů: 1 v back-endové podsítě a další ve front-endové podsítě.  

* Zkuste přístup k účtu Azure Cosmos DB z obou virtuálních počítačů. Je třeba připojit z virtuálního počítače vytvořeného v back-endové podsítě, ale ne z vytvořeny ve front-endové podsítě. Při pokusu o připojení z podsítě front-endu, který potvrdí, že je zabezpečený přístup ke službě Azure Cosmos DB pomocí koncového bodu služby virtuální sítě, žádost skončí chybou s 404. Počítače v back-endové podsítě budou moci pracovat správně.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Co se stane při přístupu k účtu Azure Cosmos DB, který má virtuální síť, kterou povolený seznam řízení přístupu (ACL)?  

HTTP 404 chyba je vrácena.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Jsou podsítě virtuální sítě vytvářet v různých oblastech můžou přistupovat k účtu služby Azure Cosmos DB v jiné oblasti? Pokud je účet služby Azure Cosmos DB v USA – západ nebo USA – východ a virtuální sítě jsou například v několika oblastech, můžete ve virtuální síti přístup k Azure Cosmos DB?  

Ano, virtuálními sítěmi v různých oblastech můžou používat tak novou funkci. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Účet služby Azure Cosmos DB může mít koncový bod služby virtuální sítě a brány firewall?  

Ano, koncový bod služby virtuální sítě a brány firewall může existovat vedle sebe. Obecně platí je třeba zajistit, že přístup k portálu je vždy povoleno před konfigurací koncového bodu služby virtuální sítě umožňují zobrazit metriky související s kontejnerem.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Můžu "Povolit přístup k jiným službám Azure z dané oblasti Azure" když je povolen přístup koncových bodů služby pro službu Azure Cosmos DB?  

To je potřeba, pouze když chcete svůj účet služby Azure Cosmos DB ke kterým přistupují jiné Azure od služby jako Azure Data factory, Azure Search nebo některý službu, která je nasazena v dané oblasti Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Kolik koncových bodů služby virtuální sítě jsou povoleny pro službu Azure Cosmos DB?  

64 koncové body služeb virtuální sítě jsou povoleny pro účet služby Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Jaký je vztah mezi pravidly koncového bodu služby a skupina zabezpečení sítě (NSG)?  

Pravidla skupiny zabezpečení sítě ve službě Azure Cosmos DB umožní omezit přístup na konkrétní rozsah adres IP adres Azure Cosmos DB. Pokud chcete povolit přístup k instanci služby Azure Cosmos DB, která se nachází v konkrétní [oblasti](https://azure.microsoft.com/global-infrastructure/regions/), můžete zadat tuto oblast v následujícím formátu: 

    AzureCosmosDB.<region name>

Další informace o NSG značky viz [značky služeb virtuální sítě](../virtual-network/security-overview.md#service-tags) článku. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Jaký je vztah mezi brány firewall protokolu IP a možnost koncový bod služby virtuální sítě?  

Tyto dvě funkce se vzájemně k zajištění izolace prostředků služby Azure Cosmos DB a zabezpečit doplňují. Pomocí IP adresy brány firewall zajišťuje statických IP adres přístup účtu služby Azure Cosmos DB.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Můžete IP adresu místního zařízení, který je připojený prostřednictvím Azure Virtual Network gateway(VPN) nebo Express route gateway přístup k účtu Azure Cosmos DB?  

Rozsah IP adres nebo IP adresu místního zařízení přidaly do seznamu statických IP adres pro přístup k účtu Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Co se stane, když odstraníte virtuální síť, která obsahuje nastavení pro koncový bod služby pro službu Azure Cosmos DB?  

Při odstranění virtuální sítě, informace o seznamu ACL spojené se, že služby Azure Cosmos DB se odstraní a odebere interakce mezi virtuální sítí a účet služby Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Co se stane, pokud účet Azure Cosmos DB, který má povolený koncový bod služby virtuální sítě je odstranit?

Metadata spojená s konkrétní účet služby Azure Cosmos DB je odstraněn z podsítě. A zodpovídá za koncový uživatel se odstranit podsíť a virtuální sítě.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Můžete použít partnerské virtuální síti vytvořit koncový bod služby pro službu Azure Cosmos DB?  

Ne, jen s přímým přístupem virtuální sítě a podsítě, můžete vytvořit koncové body služby Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Co se stane IP adresu zdrojového prostředku, jako jsou virtuální počítače v podsíti, ke které nemá povolený koncový bod služby Azure Cosmos DB?

Pokud jsou povolené koncové body služeb virtuální sítě, zdrojové IP adresy prostředků v rámci podsítě virtuální sítě se přepnou od používání veřejných IPV4 adres na privátní adresy Azure Virtual Network pro provoz do služby Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Nacházejí služby Azure Cosmos DB ve službě Azure virtual network poskytuje zákazníka?  

Azure Cosmos DB je víceklientská služba s použitím veřejné IP adresy. Omezení přístupu k podsíti služby Azure Virtual network pomocí funkce pro koncový bod služby přístup omezen pro váš účet Azure Cosmos DB pomocí dané virtuální síti Azure a její podsítě.  Účet Azure Cosmos DB se nenachází v dané virtuální síti Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Co když nic se zaznamenají v Log Analytics a OMS je-li aktivní?  

Azure Cosmos DB bude nabízet protokoly s IP adresou (bez poslední oktet) se stavem 403 pro požadavek blokována seznamu ACL.  

## <a name="next-steps"></a>Další postup
Konfigurace brány firewall služby Azure Cosmos DB najdete v tématu [podpora brány firewall](firewall-support.md) článku.

