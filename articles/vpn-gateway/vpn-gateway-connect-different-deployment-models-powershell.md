---
title: 'Připojení klasických virtuálních sítí k virtuálním sítím Azure Resource Manageru: PowerShell | Dokumentace Microsoftu'
description: Vytvoření připojení VPN mezi klasické virtuální sítě a virtuální sítě Resource Manageru pomocí VPN Gateway a Powershellu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: cherylmc
ms.openlocfilehash: 65faf1a4f78244d9fdd03b6415bf2cadac923504
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38706012"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí PowerShellu

Tento článek vám pomůže propojit klasických virtuálních sítí k virtuálním sítím Resource Manageru tak, aby prostředky umístěné v samostatné nasazení modelů komunikovat mezi sebou. Kroky v tomto článku pomocí Powershellu, ale můžete také vytvořit tuto konfiguraci pomocí webu Azure portal tak, že vyberete článek z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti správce prostředků je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit propojení virtuálních sítí patřících do různých předplatných a v různých oblastech. Můžete také připojit virtuální sítě, které už máte připojení k místním sítím, jako je brána, které byly nakonfigurovány pomocí dynamické nebo založené na směrování. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete, aby si ji vytvořit, můžete místo toho zvážit připojení vašich virtuálních sítí pomocí VNet Peering. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Než začnete

Následující kroky vás provedou nastavením nezbytným pro konfiguraci dynamické nebo založené na směrování brány pro každou virtuální síť a vytvoření připojení VPN mezi bránami. Tuto konfiguraci nepodporuje statické nebo na základě zásad brány.

### <a name="pre"></a>Požadavky

* Již byly vytvořeny obou virtuálních sítích.
* Rozsahy adres pro virtuální sítě se mezi sebou, ani překrývat s žádným z rozsahů pro další připojení, které brány může být připojen k.
* Nainstalovali jste nejnovější rutiny prostředí PowerShell. Zobrazit [instalace a konfigurace Azure Powershellu](/powershell/azure/overview) Další informace. Ujistěte se, že instalace služby správy (lu) a rutiny správce prostředků (SV). 

### <a name="exampleref"></a>Příklady nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Nastavení klasické virtuální sítě**

Název virtuální sítě = ClassicVNet <br>
Umístění = USA – západ <br>
Adresní prostory virtuální sítě = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Název místní sítě = RMVNetLocal <br>
GatewayType = DynamicRouting

**Nastavení virtuální sítě Resource Manageru**

Název virtuální sítě = RMVNet <br>
Skupina prostředků = RG1 <br>
Adresní prostory IP adres virtuální sítě = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Umístění = USA – východ <br>
Název veřejné IP brány = gwpip <br>
Brána místní sítě = ClassicVNetLocal <br>
Název virtuální sítě brány = RMGateway <br>
Konfigurace adresování IP brány = gwipconfig

## <a name="createsmgw"></a>Oddíl 1 – konfigurace klasické virtuální sítě
### <a name="1-download-your-network-configuration-file"></a>1. Stáhněte si soubor konfigurace sítě
1. Přihlaste se ke svému účtu Azure v konzole Powershellu se zvýšenými oprávněními. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v prostředí Azure PowerShell. Klasické rutiny služby správy Azure Powershellu se používají v této části.

  ```powershell
  Add-AzureAccount
  ```

  Získání předplatného Azure.

  ```powershell
  Get-AzureSubscription
  ```

  Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Spuštěním následujícího příkazu exportujte konfigurační soubor sítě Azure. Můžete změnit umístění souboru pro export do jiného umístění v případě potřeby.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Otevřete soubor .xml, který jste stáhli a upravte ji. Příklad konfiguračního souboru sítě, najdete v článku [schéma konfigurace sítě](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Ověřte podsíť brány
V **VirtualNetworkSites** prvku, přidejte podsíť brány k virtuální síti, pokud ještě nebyl vytvořen. Při práci s konfiguračním souboru sítě, podsítě brány musí mít název "GatewaySubnet" nebo Azure nelze rozpoznat a použít ho jako podsíť brány.

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

### <a name="3-add-the-local-network-site"></a>3. Přidání místní síťové lokality
Místní síťové lokality, které přidáte představuje virtuální síti správce prostředků, ke kterému chcete připojit. Přidat **LocalNetworkSites** – element pro soubor, pokud již neexistuje. V tomto okamžiku v konfiguraci VPNGatewayAddress může být libovolná platná veřejná IP adresa protože ještě nevytvořili bránu pro virtuální síť Resource Manageru. Po vytvoření brány, nahradíme tuto zástupnou adresu IP se správnou veřejnou IP adresou, která se přiřadila k bráně RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Virtuální síť přidružit k místní síťové lokality
V této části určíme na místní síťovou lokalitu, kterou chcete připojit k virtuální síti. V takovém případě je virtuální síť Resource Manageru, na který odkazuje dříve. Ujistěte se, že názvy odpovídají. Tento krok není vytvoření brány. Určuje, brána se připojí k místní síti.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Uložte soubor a nahrát
Soubor uložte a importujte jej do Azure, spuštěním následujícího příkazu. Ujistěte se, že změníte cestu k souboru jako nezbytné pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zobrazí se podobně jako výsledek znázorňující, import proběhl úspěšně.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Vytvoření brány

Před spuštěním tohoto příkladu, odkazovat na soubor konfigurace sítě, který jste stáhli pro přesné názvy, které Azure očekává, že chcete zobrazit. Soubor konfigurace sítě obsahuje hodnoty pro klasické virtuální sítě. Někdy názvy pro klasické virtuální sítě jsou změněny v konfiguračním souboru sítě, při vytváření nastavení klasické virtuální sítě na webu Azure Portal vzhledem k rozdílům v modelech nasazení. Při zadávání názvu virtuální sítě, která obsahuje mezery, pomocí jednoduchých uvozovek kolem hodnoty. V následujícím příkladu - VNetName je název klasickou virtuální síť a - LocalNetworkSiteName je název zadaný pro místní síťovou lokalitu.


- SharedKey je hodnota, kterou generujete a zadáváte.

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

V tomto příkladu jsme použili "abc123", ale můžete vygenerovat a používat něco složitějšího.

## <a name="creatermgw"></a>Důležité je, že hodnota, kterou zde zadáte, musí být stejnou hodnotu, kterou zadáte v dalším kroku při vytváření připojení.
Vrácení by se měla zobrazit stav: úspěšné. Vytvoření připojení k síti VPN spuštěním následujících příkazů: 

1. Všimněte si, že - ConnectionType je protokol IPsec, ne Vnet2Vnet. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure. Část 5 – ověření připojení

  ```powershell
  Connect-AzureRmAccount
  ``` 
   
  Načtěte seznam předplatných Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Chcete-li ověřit připojení z klasické virtuální sítě k virtuální síti správce prostředků

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Vytvořte bránu místní sítě. Ve virtuální síti brána místní sítě obvykle odkazuje na vaše místní umístění. Chcete-li ověřit připojení z vaší virtuální sítě Resource Manageru pro klasické virtuální sítě Zadejte název, podle kterého Azure na ni odkazuje a také zadáte předponu adresního prostoru. Azure pomocí zadané předpony IP adresy rozpozná, jaký provoz má zasílat na vaše místní umístění. Pokud je potřeba upravit tyto informace tady později, než vytvoříte bránu, můžete upravit hodnoty a spusťte ukázku znovu.
   
   **-Název** je název, kterou chcete přiřadit k odkazování na bránu místní sítě.<br>
   **-AddressPrefix** je adresní prostor pro klasické virtuální sítě.<br>
   **-GatewayIpAddress** je veřejnou IP adresu brány klasické virtuální sítě. Nezapomeňte změnit následující ukázce tak, aby odrážely správnou IP adresu.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Požádat o veřejnou IP adresu, která bude přidělena pro bránu virtuální sítě pro virtuální síť Resource Manageru. Nelze zadat IP adresu, kterou chcete použít. IP adresa se dynamicky přiřazovány do brány virtuální sítě. To ale neznamená, že se IP adresa změní. Změna IP adresy brány virtuální sítě se pouze při brány je odstranili a znovu vytvořili. Nezmění změny velikosti, resetování nebo jiné operace údržby/upgradu brány.

  V tomto kroku jsme také nastavit proměnnou, která se používá v pozdější fázi.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Ověřte, zda má virtuální síti podsíť brány. Pokud neexistuje žádná podsíť brány, přidáte. Ujistěte se, že podsíť brány má název *GatewaySubnet*.
5. Načte podsítě používané pro bránu spuštěním následujícího příkazu. V tomto kroku jsme také nastavit proměnnou, která se použije v dalším kroku.
   
   **-Název** je název vaší virtuální sítě Resource Manageru.<br>
   **-ResourceGroupName** je skupina prostředků, které je přidružené k virtuální síti. Podsíť brány musí již existovat pro tuto virtuální síť a musí mít název *GatewaySubnet* fungovala správně.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Vytvoření konfigurace adresování IP brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následující ukázky vytvořte vlastní konfiguraci brány.

  V tomto kroku **- SubnetId** a **- PublicIpAddressId** parametry musí předávat vlastnost id podsítě a IP adresu objektů, v uvedeném pořadí. Nelze použít jednoduchým řetězcem. Tyto proměnné se nastavují v kroku k vyžádání veřejné IP adresy a krok se načíst podsítě.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Spuštěním následujícího příkazu vytvořte bránu virtuální sítě Resource Manageru. `-VpnType` Musí být *RouteBased*. Může trvat 45 minut nebo déle, vytvoření brány.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Zkopírujte veřejnou IP adresu, po vytvoření brány sítě VPN. Můžete ji použít při konfiguraci nastavení místní sítě pro klasické virtuální sítě. Načíst veřejnou IP adresu můžete použít následující rutinu. Veřejná IP adresa je uvedena v vrátit jako *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Oddíl 3 – upravte nastavení místní lokality klasické virtuální sítě

V této části pracujete s klasickou virtuální síť. Můžete nahradit zástupnou IP adresu, která jste použili při nastavení místní sítě, které se použijí pro připojení k bráně virtuální sítě Resource Manageru. 

1. Exportujte soubor konfigurace sítě.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Pomocí textového editoru, upravte hodnotu pro VPNGatewayAddress. Zástupnou IP adresu nahraďte veřejnou IP adresu brány Resource Manageru a následně změny uložte.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importujte soubor konfigurace upravené sítě do Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Oddíl 4 – vytvoření propojení bran
Vytváří se připojení mezi bránami vyžaduje PowerShell. Budete muset přidat účet Azure použít klasickou verzi rutin Powershellu. Chcete-li tak učinit, použijte **Add-AzureAccount**.

1. V konzole prostředí PowerShell nastavte sdílený klíč. Před spuštěním rutin, odkazovat na soubor konfigurace sítě, který jste stáhli pro přesné názvy, které Azure očekává, že chcete zobrazit. Při zadávání názvu virtuální sítě, která obsahuje mezery, pomocí jednoduchých uvozovek kolem hodnoty.<br><br>V následujícím příkladu **- VNetName** je název klasickou virtuální síť a **- LocalNetworkSiteName** je název zadaný pro místní síťovou lokalitu. **- SharedKey** je hodnota, kterou generujete a zadáváte. V tomto příkladu jsme použili "abc123", ale můžete vygenerovat a používat něco složitějšího. Důležité je, že hodnota, kterou zde zadáte, musí být stejnou hodnotu, kterou zadáte v dalším kroku při vytváření připojení. Vrácení by se měla zobrazit **stav: úspěšné**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Vytvoření připojení k síti VPN spuštěním následujících příkazů:
   
  Nastavte proměnné.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Vytvořte připojení. Všimněte si, že **- ConnectionType** je protokol IPsec, ne Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Část 5 – ověření připojení

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Chcete-li ověřit připojení z klasické virtuální sítě k virtuální síti správce prostředků

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Chcete-li ověřit připojení z vaší virtuální sítě Resource Manageru pro klasické virtuální sítě

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
