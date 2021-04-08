---
title: 'Propojení klasických virtuálních sítí s Azure Resource Manager virtuální sítě: PowerShell'
description: Vytvořte připojení VPN mezi klasickými virtuální sítě a Správce prostředků virtuální sítě pomocí VPN Gateway a PowerShellu.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376484"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí PowerShellu

Tento článek vám pomůže připojit klasický virtuální sítě k Správce prostředků virtuální sítě, aby bylo možné zajistit vzájemnou komunikaci prostředků umístěných v různých modelech nasazení. Kroky v tomto článku používají PowerShell, ale tuto konfiguraci můžete také vytvořit pomocí Azure Portal tak, že vyberete článek z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Správce prostředků je podobné připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit propojení mezi virtuální sítě, která jsou v různých předplatných a v různých oblastech. Můžete taky připojit virtuální sítě, které už mají připojení k místním sítím, pokud je brána, se kterou je nakonfigurovaná, dynamická nebo založená na směrování. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete ji vytvořit, možná budete chtít místo toho připojit virtuální sítě pomocí partnerského vztahu VNet. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Následující kroky vás provedou nastavením potřebným ke konfiguraci dynamické brány nebo brány založené na trasách pro každou virtuální síť a vytvoření připojení VPN mezi bránami. Tato konfigurace nepodporuje statické brány ani brány založené na zásadách.

### <a name="prerequisites"></a><a name="pre"></a>Požadavky

* Oba virtuální sítě již byly vytvořeny. Pokud potřebujete vytvořit virtuální síť Resource Manageru, přečtěte si téma [Vytvoření skupiny prostředků a virtuální sítě](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Pokud chcete vytvořit klasickou virtuální síť, přečtěte si téma vytvoření virtuální sítě [typu Classic](/previous-versions/azure/virtual-network/create-virtual-network-classic).
* Rozsahy adres pro virtuální sítě se vzájemně nepřekrývají, nebo se překrývají s žádným z rozsahů pro další připojení, ke kterým je možné brány připojit.
* Nainstalovali jste nejnovější rutiny PowerShellu. Další informace najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/) . Nezapomeňte nainstalovat rutinu Service Management (SM) i Správce prostředků (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Příklad nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Nastavení pro klasickou virtuální síť**

Název virtuální sítě = ClassicVNet <br>
Location = Západní USA <br>
Virtual Network adresních prostorů = 10.0.0.0/24 <br>
Podsíť-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Název místní sítě = RMVNetLocal <br>
GatewayType = DynamicRouting

**Nastavení virtuální sítě Správce prostředků**

Název virtuální sítě = RMVNet <br>
Skupina prostředků = RG1 <br>
Virtual Network adresní prostory IP adres = 192.168.0.0/16 <br>
Podsíť-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Location = Východní USA <br>
Název veřejné IP adresy brány = gwpip <br>
Brána místní sítě = ClassicVNetLocal <br>
Název Virtual Network brány = RMGateway <br>
Konfigurace adresování IP adres brány = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Oddíl 1 – konfigurace klasické virtuální sítě
### <a name="1-download-your-network-configuration-file"></a>1. Stáhněte si konfigurační soubor sítě.
1. Přihlaste se ke svému účtu Azure v konzole PowerShellu se zvýšenými právy. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v prostředí Azure PowerShell. V této části se používají rutiny služby SM (Classic Service Management) Azure PowerShell.

   ```azurepowershell
   Add-AzureAccount
   ```

   Získejte své předplatné Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportujte soubor konfigurace sítě Azure spuštěním následujícího příkazu. V případě potřeby můžete změnit umístění souboru, aby se v případě potřeby exportovali do jiného umístění.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Otevřete soubor. XML, který jste stáhli, a upravte ho. Příklad konfiguračního souboru sítě najdete v tématu [schéma konfigurace sítě](/previous-versions/azure/reference/jj157100(v=azure.100)).

### <a name="2-verify-the-gateway-subnet"></a>2. ověření podsítě brány
V elementu **VirtualNetworkSites** přidejte podsíť brány do vaší virtuální sítě, pokud ještě nebyla vytvořena. Když pracujete s konfiguračním souborem sítě, podsíť brány musí mít název "GatewaySubnet" nebo Azure ji nemůže rozpoznat a použít jako podsíť brány.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Příklad:**

```xml
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
```

### <a name="3-add-the-local-network-site"></a>3. Přidejte lokalitu místní sítě.
Místní síťová lokalita, kterou přidáte, představuje virtuální síť RM, ke které se chcete připojit. Pokud ještě neexistuje, přidejte do souboru element **LocalNetworkSites** . V této fázi konfigurace může být VPNGatewayAddress jakákoli platná veřejná IP adresa, protože ještě jsme nevytvořili bránu pro Správce prostředků VNet. Jakmile vytvoříte bránu, nahradíme tuto zástupnou IP adresu správnou veřejnou IP adresou, která byla přiřazena k bráně RM.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. přidružte virtuální síť k místní síťové lokalitě.
V této části určíme místní síťovou lokalitu, ke které se má připojit virtuální síť. V tomto případě se jedná o Správce prostředků virtuální síť, na kterou jste předtím odkazovali. Ujistěte se, že se názvy shodují. Tento krok nevytváří bránu. Určuje místní síť, ke které se bude brána připojovat.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Uložte soubor a nahrajte ho.
Uložte soubor a pak ho importujte do Azure spuštěním následujícího příkazu. Ujistěte se, že jste změnili cestu k souboru, jak je to nutné pro vaše prostředí.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Podobný výsledek se zobrazí při úspěšném importu.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. Vytvoření brány

Před spuštěním tohoto příkladu se podívejte na konfigurační soubor sítě, který jste stáhli pro přesné názvy, které Azure očekává k zobrazení. Konfigurační soubor sítě obsahuje hodnoty pro klasické virtuální sítě. Někdy se názvy klasických virtuální sítě při vytváření nastavení klasických virtuálních sítí v Azure Portal v důsledku rozdílů v modelech nasazení změní v souboru konfigurace sítě. Pokud jste například použili Azure Portal k vytvoření klasické virtuální sítě s názvem ' Classic VNet ' a vytvořili jste ji ve skupině prostředků s názvem ' ClassicRG ', název obsažený v konfiguračním souboru sítě se převede na ' Group ClassicRG Classic VNet '. Při zadávání názvu virtuální sítě, která obsahuje mezery, použijte kolem hodnoty uvozovky.


Pomocí následujícího příkladu vytvořte bránu dynamického směrování:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stav brány můžete zjistit pomocí rutiny **Get-AzureVNetGateway** .

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Oddíl 2 – konfigurace brány virtuální sítě RM



Předpoklady předpokládají, že již máte vytvořenou virtuální síť služby RM. V tomto kroku vytvoříte bránu VPN pro virtuální síť RM. Tyto kroky nespouštějte, dokud nenačtete veřejnou IP adresu pro bránu klasické virtuální sítě. 

1. Přihlaste se ke svému účtu Azure v konzole PowerShellu. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure. Po přihlášení se stáhne nastavení účtu, aby bylo možné Azure PowerShell. Volitelně můžete pomocí funkce "vyzkoušet IT" spustit Azure Cloud Shell v prohlížeči.

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
2. Vytvořte bránu místní sítě. Ve virtuální síti brána místní sítě obvykle odkazuje na vaše místní umístění. V takovém případě brána místní sítě odkazuje na vaši klasickou virtuální síť. Zadejte název, podle kterého se na něj bude Azure odkazovat, a také zadejte předponu adresního prostoru. Azure pomocí zadané předpony IP adresy rozpozná, jaký provoz má zasílat na vaše místní umístění. Pokud potřebujete tyto informace později upravit, můžete před vytvořením brány upravit hodnoty a znovu spustit ukázku.
   
   **-Name** je název, který chcete přiřadit, aby odkazoval na bránu místní sítě.<br>
   **– AddressPrefix** je adresní prostor pro vaši klasickou virtuální síť.<br>
   **-GatewayIpAddress** je veřejná IP adresa brány klasické virtuální sítě. Nezapomeňte změnit následující vzorový text "n. n. n. n", aby odrážel správnou IP adresu.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Vyžádejte si veřejnou IP adresu, která se má přidělit bráně virtuální sítě pro Správce prostředků virtuální síť. Nelze zadat IP adresu, kterou chcete použít. IP adresa se dynamicky přiděluje k bráně virtuální sítě. To ale neznamená, že se IP adresa změní. Jediná IP adresa brány virtuální sítě se změní jenom v případě, že se brána odstraní a znovu vytvoří. Nemění se v průběhu změny velikosti, resetování nebo jiné operace údržby/upgradu brány.

   V tomto kroku jsme také nastavili proměnnou, která se používá v pozdějším kroku.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Ověřte, jestli má vaše virtuální síť podsíť brány. Pokud žádná podsíť brány neexistuje, přidejte ji. Ujistěte se, že podsíť brány má název *GatewaySubnet*.
5. K načtení podsítě používané pro bránu spusťte následující příkaz. V tomto kroku jsme také nastavili proměnnou, která se má použít v dalším kroku.
   
   **-Name** je název vaší virtuální sítě Správce prostředků.<br>
   **-ResourceGroupName** je skupina prostředků, ke které je virtuální síť přidružená. Podsíť brány už musí existovat pro tuto virtuální síť a musí mít název *GatewaySubnet* , aby fungovala správně.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Vytvořte konfiguraci adresování IP brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následující ukázky vytvořte vlastní konfiguraci brány.

   V tomto kroku musí být parametry **-SubnetId** a **-PublicIpAddressId** předány vlastností ID z podsítě a objektů IP adres v uvedeném pořadí. Nemůžete použít jednoduchý řetězec. Tyto proměnné jsou nastaveny v kroku pro vyžádání veřejné IP adresy a kroku pro načtení podsítě.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Vytvořte bránu virtuální sítě Správce prostředků spuštěním následujícího příkazu. `-VpnType`Musí být *RouteBased*. Vytvoření brány může trvat 45 minut nebo déle.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Po vytvoření služby VPN Gateway zkopírujte veřejnou IP adresu. Použijete ji při konfiguraci nastavení místní sítě pro klasickou virtuální síť. K načtení veřejné IP adresy můžete použít následující rutinu. Veřejná IP adresa je uvedena v části návrat jako *IPAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Oddíl 3 – Změna nastavení místního webu virtuální sítě

V této části pracujete s klasickou virtuální sítí. Nahraďte zástupnou IP adresu, kterou jste použili při zadávání nastavení místních lokalit, která se použijí pro připojení k Správce prostředků bráně virtuální sítě. Vzhledem k tomu, že pracujete s klasickou virtuální sítí, použijte místně nainstalovaný PowerShell pro váš počítač, nikoli Azure Cloud Shell TryIt.

1. Exportujte konfigurační soubor sítě.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Pomocí textového editoru upravte hodnotu pro VPNGatewayAddress. Nahraďte zástupnou IP adresu veřejnou IP adresou brány Správce prostředků a pak změny uložte.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importujte upravený konfigurační soubor sítě do Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Oddíl 4 – vytvoření připojení mezi branami
Vytvoření připojení mezi bránami vyžaduje prostředí PowerShell. Je možné, že budete muset přidat účet Azure, abyste mohli používat klasickou verzi rutin PowerShellu. Uděláte to tak, že použijete **příkaz Add-AzureAccount**.

1. V konzole PowerShellu nastavte sdílený klíč. Před spuštěním rutin najdete informace o konfiguračním souboru sítě, který jste stáhli pro přesné názvy, které Azure očekává k zobrazení. Při zadávání názvu virtuální sítě, která obsahuje mezery, použijte kolem hodnoty jednoduché uvozovky.<br><br>V následujícím příkladu je **-VNetName** název klasické virtuální sítě a parametr **-LocalNetworkSiteName** je název, který jste zadali pro místní síťovou lokalitu. **-SharedKey** je hodnota, kterou vygenerujete a zadáte. V tomto příkladu jsme použili "abc123", ale můžete vygenerovat a používat něco složitějšího. Důležité je, že hodnota, kterou zde zadáte, musí být stejná jako hodnota, kterou zadáte v dalším kroku při vytváření připojení. Vrácená položka by měla zobrazovat **stav: úspěšné**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Vytvořte připojení k síti VPN spuštěním následujících příkazů:
   
   Nastavte proměnné.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Vytvořte připojení. Všimněte si, že **-ConnectionType** je IPSec, nikoli Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Oddíl 5 – ověření připojení

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Ověření připojení z vaší klasické virtuální sítě k vaší Správce prostředků virtuální síti

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>portál Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Ověření připojení z vaší virtuální sítě Správce prostředků k vaší klasické virtuální síti

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>portál Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
