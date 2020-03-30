---
title: 'Připojení klasických virtuálních sítí k virtuálním sítím Azure Resource Manager: PowerShell'
description: Vytvořte připojení VPN mezi klasickými virtuálními sítěmi a virtuálními sítěmi Správce prostředků pomocí brány VPN a Prostředí PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152002"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí PowerShellu

Tento článek vám pomůže připojit klasické virtuální sítě k virtuálním sítím Správce prostředků, abyste umožnili prostředkům umístěným v samostatných modelech nasazení vzájemnou komunikaci. Kroky v tomto článku používají PowerShell, ale můžete také vytvořit tuto konfiguraci pomocí portálu Azure výběrem článku z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Správce prostředků je podobné připojení virtuální sítě k místnímu umístění webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit připojení mezi virtuálními sítěmi, které jsou v různých předplatných a v různých oblastech. Můžete také připojit virtuální sítě, které už mají připojení k místním sítím, pokud je brána, se kterou byly nakonfigurované, dynamická nebo založená na trasách. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete ji vytvořit, můžete místo toho zvážit připojení virtuálních sítí pomocí partnerského vztahu virtuální sítě. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Následující kroky vás provedou nastavením potřebným ke konfiguraci dynamické brány nebo brány založené na trase pro každou virtuální síť a k vytvoření připojení VPN mezi branami. Tato konfigurace nepodporuje statické brány nebo brány založené na zásadách.

### <a name="prerequisites"></a><a name="pre"></a>Požadavky

* Obě virtuální sítě už byly vytvořeny. Pokud potřebujete vytvořit virtuální síť správce prostředků, přečtěte si informace [o vytvoření skupiny prostředků a virtuální sítě](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Pokud chcete vytvořit klasickou virtuální síť, [přečtěte si tématu Vytvoření klasické virtuální sítě](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Rozsahy adres pro virtuální sítě se navzájem nepřekrývají nebo se překrývají s žádným rozsahem pro jiná připojení, ke kterým mohou být brány připojeny.
* Nainstalovali jste nejnovější rutiny prostředí PowerShell. Další informace najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell.](/powershell/azure/overview) Ujistěte se, že jste nainstalovali rutiny Správy služeb (SM) i Správce prostředků (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Příklad nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasické nastavení virtuální sítě**

Název virtuální sítě = ClassicVNet <br>
Umístění = Západní USA <br>
Adresní prostory virtuální sítě = 10.0.0.0/24 <br>
Podsíť 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Název místní sítě = RMVnetLocal <br>
GatewayType = DynamicRouting

**Nastavení virtuální sítě Správce prostředků**

Název virtuální sítě = RMVNet <br>
Skupina prostředků = RG1 <br>
Ip adresní prostory virtuální sítě = 192.168.0.0/16 <br>
Podsíť 1 = 192.168.1.0/24 <br>
Podsíť gateway = 192.168.0.0/26 <br>
Umístění = východní USA <br>
Název veřejné IP adresy brány = gwpip <br>
Brána místní sítě = ClassicVNetLocal <br>
Název brány virtuální sítě = RMGateway <br>
Konfigurace adres IP brány = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Oddíl 1 – Konfigurace klasické virtuální sítě
### <a name="1-download-your-network-configuration-file"></a>1. Stažení síťového konfiguračního souboru
1. Přihlaste se ke svému účtu Azure v konzole PowerShell se zvýšenými právy. Následující rutina vás vyzve k zadání přihlašovacích údajů k vašemu účtu Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v prostředí Azure PowerShell. Klasické rutiny Azure PowerShell (Správa služeb) se používají v této části.

   ```azurepowershell
   Add-AzureAccount
   ```

   Získejte předplatné Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportujte konfigurační soubor sítě Azure spuštěním následujícího příkazu. V případě potřeby můžete změnit umístění souboru, které chcete exportovat do jiného umístění.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Otevřete soubor XML, který jste stáhli a upravte ho. Příklad konfiguračního síťového souboru naleznete v [tématu Schéma konfigurace sítě](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Ověření podsítě brány
V elementu **VirtualNetworkSites** přidejte podsíť brány do virtuální sítě, pokud ještě nebyla vytvořena. Při práci s konfiguračním souborem sítě musí být podsíť brány pojmenována "GatewaySubnet" nebo Azure ji nemůže rozpoznat a použít jako podsíť brány.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Příklad:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Přidání lokality místní sítě
Přidávaná lokalita místní sítě představuje virtuální síť RM, ke které se chcete připojit. Přidejte prvek **LocalNetworkSites** do souboru, pokud ještě neexistuje. V tomto okamžiku v konfiguraci VPNGatewayAddress může být libovolná platná veřejná IP adresa, protože jsme ještě nevytvořili bránu pro virtuální síť Správce prostředků. Jakmile vytvoříme bránu, nahradíme tuto zástupnou IP adresu správnou veřejnou IP adresou, která byla přiřazena k bráně RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Přidružení virtuální sítě k místní síti
V této části určíme lokalitu místní sítě, ke které chcete virtuální síť připojit. V takovém případě je virtuální síť Správce prostředků, na kterou jste odkazovali dříve. Ujistěte se, že se jména shodují. Tento krok nevytvoří bránu. Určuje místní síť, ke které se brána připojí.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Uložte soubor a nahrajte
Uložte soubor a pak jej importujte do Azure spuštěním následujícího příkazu. Ujistěte se, že změníte cestu k souboru podle potřeby pro vaše prostředí.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zobrazí se podobný výsledek, který ukazuje, že import byl úspěšný.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Vytvoření brány

Před spuštěním tohoto příkladu, odkazovat na konfigurační soubor sítě, které jste stáhli pro přesné názvy, které Azure očekává, že vidět. Konfigurační síťový soubor obsahuje hodnoty pro klasické virtuální sítě. Někdy se názvy klasických virtuálních sítí změní v konfiguračním souboru sítě při vytváření klasických nastavení virtuální sítě na portálu Azure kvůli rozdílům v modelech nasazení. Například pokud jste použili portál Azure k vytvoření klasické virtuální sítě s názvem "Classic Virtuální síť" a vytvořili ji ve skupině prostředků s názvem ClassicRG, název, který je obsažen v souboru konfigurace sítě se převede na "Group ClassicRG Classic Virtuální síť". Při zadávání názvu virtuální sítě, která obsahuje mezery, použijte uvozovky kolem hodnoty.


K vytvoření dynamické brány směrování použijte následující příklad:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stav brány můžete zkontrolovat pomocí rutiny **Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Oddíl 2 – Konfigurace brány virtuální sítě RM



Požadavky předpokládají, že jste již vytvořili virtuální síť RM. V tomto kroku vytvoříte bránu VPN pro virtuální síť RM. Nespouštějte tyto kroky až po načtení veřejné IP adresy pro bránu klasické virtuální sítě. 

1. Přihlaste se ke svému účtu Azure v konzole PowerShellu. Následující rutina vás vyzve k zadání přihlašovacích údajů k vašemu účtu Azure. Po přihlášení se stáhnou nastavení vašeho účtu, aby bylo dostupné pro Azure PowerShell. Volitelně můžete použít funkci "Vyzkoušet" ke spuštění Služby Azure Cloud Shell v prohlížeči.

   Pokud používáte Azure Cloud Shell, přeskočte následující rutinu:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Chcete-li ověřit, zda používáte správné předplatné, spusťte následující rutinu:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Vytvořte bránu místní sítě. Ve virtuální síti brána místní sítě obvykle odkazuje na vaše místní umístění. V takovém případě brána místní sítě odkazuje na klasickou virtuální síť. Pojmenujte ho, pod kterým na něj Azure může odkazovat, a také zadejte předponu adresního prostoru. Azure pomocí zadané předpony IP adresy rozpozná, jaký provoz má zasílat na vaše místní umístění. Pokud potřebujete upravit informace zde později, před vytvořením brány, můžete upravit hodnoty a spustit ukázku znovu.
   
   **-Name** je název, který chcete přiřadit k odkazu na bránu místní sítě.<br>
   **-AddressPrefix** je adresní prostor pro klasickou virtuální síť.<br>
   **-GatewayIpAddress** je veřejná IP adresa brány klasické virtuální sítě. Nezapomeňte změnit následující ukázkový text "n.n.n.n" tak, aby odrážel správnou adresu IP.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Požádejte o přidělení veřejné IP adresy bráně virtuální sítě pro virtuální síť Správce prostředků. Nelze zadat IP adresu, kterou chcete použít. IP adresa je dynamicky přidělena bráně virtuální sítě. To ale neznamená, že se IP adresa změní. Ip adresa virtuální síťové brány se mění pouze v případě, že je brána odstraněna a znovu vytvořena. Nemění se v rámci změny velikosti, resetování nebo jiné interní údržby nebo upgradů brány.

   V tomto kroku také nastavíme proměnnou, která se používá v pozdějším kroku.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Ověřte, zda má vaše virtuální síť podsíť brány. Pokud neexistuje žádná podsíť brány, přidejte ji. Ujistěte se, že podsíť brány se jmenuje *GatewaySubnet*.
5. Načtěte podsíť použitou pro bránu spuštěním následujícího příkazu. V tomto kroku také nastavíme proměnnou, která se použije v dalším kroku.
   
   **-Name** je název virtuální sítě Správce prostředků.<br>
   **-ResourceGroupName** je skupina prostředků, ke které je virtuální síť přidružena. Podsíť brány musí již existovat pro tuto virtuální síť a musí mít název *GatewaySubnet,* aby fungovala správně.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Vytvořte konfiguraci adres IP brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   V tomto kroku parametry **-SubnetId** a **-PublicIpAddressId** musí být předány vlastnosti id z podsítě a objekty ip adresy. Nemůžete použít jednoduchý řetězec. Tyto proměnné jsou nastaveny v kroku požádat o veřejnou IP adresu a krok k načtení podsítě.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Pomocí následujícího příkazu vytvořte bránu virtuální sítě Resource Manager. Musí `-VpnType` být *RouteBased*. Vytvoření brány může trvat 45 minut nebo více.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Po vytvoření brány VPN zkopírujte veřejnou IP adresu. Použijete ji při konfiguraci nastavení místní sítě pro klasickou virtuální síť. K načtení veřejné IP adresy můžete použít následující rutinu. Veřejná IP adresa je uvedena v returnu jako *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Oddíl 3 – Změna klasického nastavení místního webu virtuální sítě

V této části pracujete s klasickou virtuální sítí. Nahradit zástupný IP adresu, kterou jste použili při zadávání nastavení místní ho webu, který se bude používat pro připojení k bráně virtuální sítě Správce prostředků. Protože pracujete s klasickou virtuální sítí, použijte PowerShell nainstalovaný místně do počítače, ne Azure Cloud Shell TryIt.

1. Exportujte konfigurační soubor sítě.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Pomocí textového editoru upravte hodnotu adresy VPNGatewayAddress. Nahraďte zástupnou adresu IP veřejnou IP adresou brány Správce prostředků a uložte změny.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importujte upravený konfigurační síťový soubor do Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Oddíl 4 - Vytvoření spojení mezi branami
Vytvoření připojení mezi branami vyžaduje Prostředí PowerShell. Možná budete muset přidat svůj účet Azure, abyste mohli používat klasickou verzi rutin Prostředí PowerShell. Chcete-li tak učinit, použijte **Add-AzureAccount**.

1. V konzole PowerShell nastavte sdílený klíč. Před spuštěním rutiny, odkazovat na konfigurační soubor sítě, které jste stáhli přesné názvy, které Azure očekává, že vidět. Při zadávání názvu virtuální sítě, která obsahuje mezery, použijte jednoduché uvozovky kolem hodnoty.<br><br>V následujícím příkladu **-VNetName** je název klasické virtuální sítě a **-LocalNetworkSiteName** je název, který jste zadali pro lokalitu místní sítě. **-SharedKey** je hodnota, kterou generujete a určujete. V příkladu jsme použili 'abc123', ale můžete generovat a používat něco složitějšího. Důležité je, že hodnota, kterou zde zadáte, musí být stejná hodnota, kterou zadáte v dalším kroku při vytváření připojení. Návrat by měl zobrazit **Stav: Úspěšné**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Připojení VPN vytvořte spuštěním následujících příkazů:
   
   Nastavte proměnné.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Vytvořte připojení. Všimněte si, že **-ConnectionType** je IPsec, nikoli Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Oddíl 5 - Ověření připojení

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Ověření připojení z klasické virtuální sítě k virtuální síti Správce prostředků

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>portál Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Ověření připojení z virtuální sítě Správce prostředků ke klasické virtuální síti

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>portál Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
