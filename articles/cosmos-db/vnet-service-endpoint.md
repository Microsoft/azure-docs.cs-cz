---
title: Zabezpečený přístup k účtu Azure Cosmos DB pomocí koncový bod služby Azure Virtual Network | Microsoft Docs
description: Tento dokument popisuje kroky potřebné k instalaci koncový bod služby virtuální sítě Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: e6b263c1eb9fe3b151f0a51b5da9a92b8ced4549
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109738"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Zabezpečený přístup k účtu Azure Cosmos DB pomocí koncový bod služby Azure Virtual Network

Účty Azure CosmosDB můžete nakonfigurovat tak, aby přístup pouze z konkrétní podsítě virtuální sítě Azure. Povolením [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro Azure CosmosDB z virtuální sítě a jeho podsíti, je provoz zajištěn trasu optimální a bezpečný pro Azure Cosmos DB.  

Azure Cosmos DB je globálně distribuovaná databázová služba pro více modelů. Data v Azure Cosmos DB účtu můžete replikovat do několika oblastí. V případě virtuální sítě koncového bodu služby nastavena Azure Cosmos DB, každá virtuální síť umožňuje přístup z IP adres, který patří do specicifc podsítě. Následující obrázek znázorňuje obrázek databáze Cosmos Azure, který má povolené koncový bod služby virtuální sítě:

![Architektura koncový bod služby virtuální sítě](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Jakmile účet Azure Cosmos DB nakonfigurovaný s koncového bodu služby virtuální sítě, je přístupná jenom z Zadaná podsíť, je odebrán všechny veřejné nebo Internetu. Další informace v podrobné o koncové body služby, získáte na Azure [přehled koncové body služby virtuálních sítí](../virtual-network/virtual-network-service-endpoints-overview.md) článku.

> [!NOTE]
> Aktuálně koncové body služby virtuální sítě lze nakonfigurovat pro rozhraní API pro Azure Cosmos DB SQL nebo rozhraní API Mongo účty. Možnost konfigurace koncových bodů služby pro ostatní rozhraní API a svrchovaných cloudy, například Azure v Německu nebo Azure Government bude brzy k dispozici. Pokud máte existující brány firewall IP nakonfigurovaných pro váš účet Azure Cosmos DB, je potřeba počítat s konfiguraci brány firewall, odeberte IP brány firewall a pak nakonfigurujte koncový bod služby seznamu ACL. Po dokončení konfigurace koncového bodu služby, můžete znovu povolit IP brány firewall v případě potřeby.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurace koncového bodu služby pomocí portálu Azure
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro existující virtuální síť Azure a podsíť

1. Z **všechny prostředky** okně Najít virtuální sítě, kterou chcete nakonfigurovat koncový bod služby pro Azure Cosmos DB. Přejděte na **koncové body služby** okno a ujistěte se, že je povoleno podsíť virtuální sítě pro koncový bod služby "Azure.CosmosDB".  

   ![Zkontrolujte povolené koncový bod služby](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Z **všechny prostředky** okně Najít účet Azure Cosmos DB chcete zabezpečit.  

3. Před povolením koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojené s vaším účtem Azure Cosmos DB pro budoucí použití. Po dokončení konfigurace koncového bodu služby můžete znovu povolit IP brány firewall.  

4. Vyberte **brány firewall a virtuální sítě** z nabídky nastavení a vyberte Povolit přístup ze **vybrané sítě**.  

3. K udělení přístupu k existující virtuální síť na podsíť, v rámci virtuální sítě, vyberte **přidat existující virtuální síť Azure**.  

4. Vyberte **předplatné** ze kterého chcete přidat virtuální síť Azure. Vyberte Azure **virtuální sítě** a **podsítě** , které chcete poskytnout přístup k účtu Azure Cosmos DB. Dále vyberte **povolit** umožňující vybrané sítě se koncové body služby "Microsoft.AzureCosmosDB". Po dokončení vyberte **přidat**.  

   ![Vyberte virtuální síť a podsíť](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Pokud koncový bod služby pro Azure Cosmos DB není dříve nakonfigurovaný pro vybrané virtuální sítě Azure a podsítě, můžete nakonfigurovat jako součást této operace. Povolení přístupu bude trvat až 15 minut. Je velmi důležité, abyste po poznamenat dolů obsah brány firewall seznamu ACL pro renabling je později zakázat IP brány firewall. 

   ![virtuální síť a podsíť byl úspěšně nakonfigurován](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Teď váš účet Azure Cosmos DB povolí pouze provoz z tohoto vybraná podsíť. Pokud byl dříve povolen IP brány firewall, prosím znovu povolte pomocí dřívější informace.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurace koncového bodu služby pro novou virtuální síť Azure a podsíť

1. Z **všechny prostředky** okně Najít účet Azure Cosmos DB chcete zabezpečit.  

> [!NOTE]
> Pokud máte existující brány firewall IP nakonfigurovaných pro váš účet Azure Cosmos DB, je potřeba počítat s konfiguraci brány firewall, odeberte IP brány firewall a pak povolte koncový bod služby. Pokud povolíte koncový bod služby bez disbling bránu firewall, provoz z tohoto rozsahu ip budou ztraceny virtuální IP identity a je vyřazeno s chybovou zprávou filtru IP. Tak, aby se tato chyba zakážete vždy pravidla brány firewall, zkopírujte je, povolte koncový bod služby z podsítě a nakonec seznamu ACL podsíť z databáze Cosmos. Po konfiguraci koncového bodu služby a přidání seznamu ACL můžete znovu povolit bránu firewall IP znovu v případě potřeby.

2. Před povolením koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojené s vaším účtem Azure Cosmos DB pro budoucí použití. Po dokončení konfigurace koncového bodu služby můžete znovu povolit IP brány firewall.  

3. Vyberte **brány firewall a virtuální sítě Azure** z nabídky nastavení a vyberte Povolit přístup ze **vybrané sítě**.  

4. K udělení přístupu k novou virtuální síť Azure, v rámci virtuální sítě, vyberte **přidat nové virtuální sítě**.  

5. Zadejte informace potřebné pro vytvoření nové virtuální sítě a vyberte možnost vytvořit. Pro "Microsoft.AzureCosmosDB" povoleno, bude vytvořeno podsíť s koncového bodu služby.

   ![Vyberte virtuální síť a podsíť pro novou virtuální síť](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Povolit přístup z portálu Azure

Po koncové body služby Azure Virtual Network jsou povolené pro databázový účet Azure Cosmos DB, přístup z portálu nebo jiné služby Azure ve výchozím nastavení vypnutá. Přístup ke svému účtu Azure Cosmos DB databáze z počítače mimo nakonfigurovaný podsítě jsou zablokovány, včetně přístupu z portálu.

![Povolit přístup z portálu](./media/vnet-service-endpoint/allow-access-from-portal.png)

Pokud váš účet Azure Cosmos DB používané jinými službami Azure, jako je Azure Search, nebo k němu přistupovat z Stream analytics nebo Power BI, povolíte přístup kontrolou **povolit přístup ke službám Azure**.

Aby máte přístup k metrikám Azure Cosmos DB z portálu, musíte povolit **povolit přístup k portálu Azure** možnosti. Další informace o těchto možnostech najdete v tématu [připojení z portálu Azure](firewall-support.md#connections-from-the-azure-portal) a [připojení ze služeb Azure PaaS](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) oddíly. Po výběru přístup, vyberte **Uložit** uložte nastavení.

## <a name="remove-a-virtual-network-or-subnet"></a>Odebrat virtuální síť nebo podsíť 

1. Z **všechny prostředky** okno, najít Azure Cosmos DB účet, pro který jste přiřadili koncové body služby.  

2. Vyberte **brány firewall a virtuální sítě** z nabídky nastavení.  

3. Chcete-li odebrat virtuální síť nebo podsíť pravidla, vyberte "..." vedle virtuální síť nebo podsíť a vyberte **odebrat**.

   ![Odebrat virtuální síť](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Klikněte na tlačítko **Uložit** proveďte změny.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurace koncového bodu služby pomocí prostředí Azure PowerShell 

Konfigurace koncového bodu služby k účtu Azure Cosmos DB pomocí prostředí Azure PowerShell, použijte následující kroky:  

1. Nainstalujte si nejnovější verzi [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a [přihlášení](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Ujistěte se, poznamenejte si nastavení brány firewall IP adresy a IP brány firewall úplně než povolíte, odstraňte koncový bod služby pro účet.


> [!NOTE]
> Pokud máte existující brány firewall IP nakonfigurovaných pro váš účet Azure Cosmos DB, je potřeba počítat s konfiguraci brány firewall, odeberte IP brány firewall a pak povolte koncový bod služby. Pokud povolíte koncový bod služby bez disbling bránu firewall, provoz z tohoto rozsahu ip budou ztraceny virtuální IP identity a je vyřazeno s chybovou zprávou filtru IP. Tak, aby se tato chyba zakážete vždy pravidla brány firewall, zkopírujte je, povolte koncový bod služby z podsítě a nakonec seznamu ACL podsíť z databáze Cosmos. Po konfiguraci koncového bodu služby a přidání seznamu ACL můžete znovu povolit bránu firewall IP znovu v případě potřeby.

2. Před povolením koncový bod služby virtuální sítě, zkopírujte informace o bráně firewall IP spojené s vaším účtem Azure Cosmos DB pro budoucí použití. IP brány firewall se znovu povolit po dokončení konfigurace koncového bodu služby.  

3. Povolte koncový bod služby pro existující podsíť virtuální sítě.  

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

4. Připravte k povolování seznam ACL u účtu CosmosDB tím, že zajistí, že virtuální síť a podsíť mají koncový bod služby, které jsou povolené pro Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Získáte vlastnosti účtu Azure Cosmos DB spuštěním následující rutiny:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Inicializace proměnných pro pozdější použití. Nastavit všechny proměnné z existující definice účtu, pokud máte více lokalit, je nutné je přidat jako součást pole. V tomto kroku nakonfigurujete také koncový bod služby virtuální sítě nastavením proměnné "accountVNETFilterEnabled" na "True". Tato hodnota je později přiřazené k parametru "isVirtualNetworkFilterEnabled".  

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

7. Aktualizujte vlastnosti účtu Azure Cosmos DB novou konfigurací spuštěním následující rutiny: 

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

8. Spusťte následující příkaz pro ověření, jestli je váš účet Azure Cosmos DB aktualizováno koncový bod služby virtuální sítě, který jste nakonfigurovali v předchozím kroku:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Přidat koncový bod služby virtuální sítě pro Azure Cosmos DB účtu, který má povolenou bránu Firewall IP

1. Nejdřív zakážete IP brány firewall přístup k účtu Azure Cosmos DB.  

2. Povolte koncový bod virtuální sítě k účtu Azure Cosmos DB pomocí jedné z metod popsaných v předchozích částech.  

3. Znovu povolte přístup IP brány firewall. 

## <a name="test-the-access"></a>Test přístupu

Pokud chcete zkontrolovat, jestli jsou koncové body služby pro Azure Cosmos DB nakonfigurované podle očekávání, použijte následující postup:

* Nastavte dvě podsítě ve virtuální síti jako front-endové a back-end, povolit Cosmos DB koncový bod služby pro back-end podsíť.  

* Povolení přístupu v účtu Cosmos DB pro podsíť back-end.  

* Vytvořte dva virtuální počítače: 1 v back-end podsítě a druhý v podsíti front-endu.  

* Vyzkoušejte si přístupu k účtu Azure Cosmos DB z obou virtuálních počítačů. Nyní byste měli mít připojení z virtuální počítač vytvořený v podsíti back-end, ale nikoli z vytvořeném v podsíť frontend. Žádost bude s 404 chybu při pokusu o připojení z podsítě front-endu, který potvrdí, že přístup k databázi Azure Cosmos zabezpečené pomocí koncový bod služby virtuální sítě. Počítače v podsíti back-end budou moci bez problémů fungují.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Co se stane při přístupu k účtu Azure Cosmos DB, který má virtuální síť, kterou povoleno seznamu řízení přístupu (ACL)?  

HTTP 404 je vrácena chyba.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Jsou podsítě virtuální sítě vytvořené v různých oblastech povolen přístup k účtu Azure Cosmos DB v jiné oblasti? Pokud účet Azure Cosmos DB v západní USA nebo Východ USA a virtuální sítě jsou například v několika oblastech, můžete virtuální síti přístup k databázi Cosmos Azure?  

Ano, virtuálním sítím vytvořeným v různých oblastech můžete přejít pomocí nové funkce. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Může mít účet Azure Cosmos DB koncový bod služby virtuální sítě a brány firewall?  

Ano, koncový bod služby virtuální sítě a brány firewall může společně existovat. Obecně platí se ujistěte, že přístup k portálu je vždy povolena před konfigurací koncového bodu služby virtuální sítě a umožňují zobrazit metriku související s kontejneru.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Můžete I "Povolit přístup k jiným službám Azure z dané oblasti Azure" Pokud je povolen přístup koncový bod služby pro Azure Cosmos DB?  

To je potřeba, jen když chcete, aby váš účet Azure Cosmos DB přístup ostatní Azure první strany služby jako objekt pro vytváření dat Azure, Azure Search nebo všechny služby, která je nasazena v dané oblasti Azure.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Kolik koncových bodů služby virtuální sítě jsou povoleny pro Azure Cosmos DB?  

Koncové body služby 64 virtuální sítě jsou povoleny pro účet Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Co je vztah mezi pravidla pro koncový bod služby a skupina zabezpečení sítě (NSG)?  

Pravidla NSG v Azure Cosmos DB umožňují omezit přístup na konkrétní rozsah Azure Cosmos DB IP adres. Pokud chcete povolit přístup k instanci databáze Cosmos Azure, která se nachází v konkrétní [oblast](https://azure.microsoft.com/global-infrastructure/regions/), můžete zadat oblast v následujícím formátu: 

    AzureCosmosDB.<region name>

Další informace o NSG značky najdete [virtuální sítě služby značky](../virtual-network/security-overview.md#service-tags) článku. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Co je vztah mezi IP brány firewall a možnost koncový bod služby virtuální sítě?  

Tyto dvě funkce doplňují ostatní k zajištění izolace Azure Cosmos DB prostředků a jejich zabezpečení. Použití IP brány firewall zajišťuje, aby statické IP adresy můžete přístup k účtu Azure Cosmos DB.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Můžete IP adresu místního zařízení, která je připojený prostřednictvím Azure Virtual Network gateway(VPN) nebo Express trasy brány přístup k účtu Azure Cosmos DB?  

Zařízení místní IP adresu nebo rozsah IP adres musí být přidaní do seznamu statických IP adres pro přístup k účtu Azure Cosmos DB.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Co se stane, když je odstranit virtuální síť, která má nastavení koncového bodu služby pro Azure Cosmos DB?  

Při odstranění virtuální sítě přidružené informace o seznamu ACL s, se odstraní databázi Cosmos Azure a odebere interakce mezi virtuální síť a účet Azure Cosmos DB. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Co se stane, když Azure Cosmos DB účtu, který má povolené koncový bod služby virtuální sítě je odstranit?

Budou metadata spojená s konkrétní účet Azure Cosmos DB je odstraněn z podsítě. A je zodpovědností koncového uživatele se odstranit podsíť a virtuální sítě.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Můžete použít peered virtuální sítě k vytvoření koncového bodu služby pro Azure Cosmos DB?  

Ne, jen přímé virtuální sítě a podsítě, můžete vytvořit koncové body služby Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Co se stane zdrojovou IP adresu prostředků jako virtuální počítač v podsíti, který má koncový bod služby Azure Cosmos DB povolené?

Pokud jsou povolené koncové body služby virtuální sítě, se změní zdrojové IP adresy prostředků v podsíti virtuální sítě pomocí veřejné adresy IPV4 na Azure Virtual Network privátní adresy pro data odesílaná do Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB nacházet ve virtuální síti Azure, poskytované zákazníkovi?  

Azure Cosmos DB je víceklientské služby s veřejnou IP adresu. Pokud omezíte přístup k podsíti Azure virtuální sítě pomocí funkce koncový bod služby, přístup je omezen pro váš účet Azure Cosmos DB prostřednictvím dané virtuální síti Azure a jeho podsíti.  Účet Azure Cosmos DB není umístěn v této virtuální síti Azure. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Co když nic bude do protokolu v protokolu analýzy nebo OMS je-li aktivní?  

Azure Cosmos DB předá protokoly s IP adresou (bez poslední oktet) se stavem 403 pro požadavek blokována seznamu ACL.  

## <a name="next-steps"></a>Další postup
Konfigurace brány firewall najdete v tématu Azure Cosmos DB [brány firewall podporu](firewall-support.md) článku.

