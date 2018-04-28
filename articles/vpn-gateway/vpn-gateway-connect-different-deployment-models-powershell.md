---
title: 'Připojení klasické virtuální sítě k virtuálním sítím Azure Resource Manager: prostředí PowerShell | Microsoft Docs'
description: Vytvoření připojení VPN mezi klasické virtuální sítě a virtuální sítě Resource Manager pomocí brány sítě VPN a prostředí PowerShell.
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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí PowerShellu

Tento článek usnadňuje připojení virtuální sítě classic do Resource Manager virtuální sítě tak, aby prostředky, které jsou umístěné v samostatné nasazení modely pro komunikaci mezi sebou. Kroky v tomto článku pomocí prostředí PowerShell, ale můžete také vytvořit této konfigurace pomocí portálu Azure tak, že vyberete článek z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Resource Manager je podobné propojení virtuální sítě do umístění místního webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit připojení mezi virtuální sítě, které jsou v různých předplatných a v různých oblastech. Virtuální sítě, které už máte připojení k místní sítě, můžete také připojit, pokud je brány, které byly nakonfigurovány k dynamické nebo založené na trasách. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě není bránu virtuální sítě a nechcete, aby si ji vytvořit, můžete místo toho zvažte připojení vaší virtuální sítě pomocí virtuální sítě partnerský vztah. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Než začnete

Následující postup vás provede procesem nastavení, která je potřeba nakonfigurovat bránu dynamické nebo založené na směrování pro každou virtuální síť a vytvoření připojení VPN mezi bránami. Tato konfigurace nepodporuje statickou nebo na základě zásad brány.

### <a name="pre"></a>Požadavky

* Již byly vytvořeny obě virtuální sítě.
* Rozsahy adres pro virtuální sítě není navzájem překrývají nebo nepřekrývá s žádným z rozsahů pro další připojení, které brány může být připojen k.
* Nainstalovali jste nejnovější rutiny prostředí PowerShell. V tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) Další informace. Ujistěte se, že instalujete službu správy (SM) a rutiny Resource Manager (RM). 

### <a name="exampleref"></a>Příklady nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasické nastavení sítě VNet**

Název virtuální sítě = ClassicVNet <br>
Umístění = západní USA <br>
Adresní prostory virtuální sítě = 10.0.0.0/24 <br>
Podsíť 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Název místní sítě = RMVNetLocal <br>
GatewayType = DynamicRouting

**Nastavení virtuální sítě Resource Manageru**

Název virtuální sítě = RMVNet <br>
Skupina prostředků = RG1 <br>
Virtuální síť adresní prostory IP adres = 192.168.0.0/16 <br>
Podsíť 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Umístění = východní USA <br>
Název veřejné IP adresy brány = gwpip <br>
Brána místní sítě = ClassicVNetLocal <br>
Název virtuální síťová brána = RMGateway <br>
Konfigurace adresování IP brány = gwipconfig

## <a name="createsmgw"></a>Oddíl 1 – konfigurace klasické virtuální sítě
### <a name="1-download-your-network-configuration-file"></a>1. Stáhněte soubor konfigurace sítě
1. Přihlaste se k účtu Azure v konzole PowerShell se zvýšenými oprávněními. Následující rutiny zobrazí výzvu pro přihlašovací údaje pro účet Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v prostředí Azure PowerShell. Classic rutin Powershellu pro Azure Service Management (SM) se používají v této části.

  ```powershell
  Add-AzureAccount
  ```

  Získáte předplatné Azure.

  ```powershell
  Get-AzureSubscription
  ```

  Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Spuštěním následujícího příkazu exportujte konfiguračního souboru sítě Azure. Můžete změnit umístění souboru pro export do jiného umístění v případě potřeby.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Otevřete soubor .xml, který jste stáhli, pokud ho Pokud chcete upravit. Příklad konfiguračního souboru sítě, naleznete v části [schéma konfigurace sítě](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Ověřte podsíť brány
V **VirtualNetworkSites** elementu přidat podsíť brány k virtuální síti, pokud dosud nebyla vytvořena. Při práci s konfiguračního souboru sítě, podsítě brány musí mít název "GatewaySubnet" nebo Azure nelze rozpoznat a používejte ho jako podsíť brány.

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

### <a name="3-add-the-local-network-site"></a>3. Přidat na místní síťový Web
Na místní síťový web, které přidáte představuje RM virtuální síť, ke kterému se chcete připojit. Přidat **LocalNetworkSites** element do souboru, pokud dosud neexistuje. V tomto okamžiku v konfiguraci prvek VPNGatewayAddress může být jakékoli platná veřejná IP adresa protože jsme ještě nevytvořili brány pro virtuální sítě Resource Manageru. Jakmile se nám vytvořit bránu, jsme nahraďte tuto IP adresu zástupný symbol správné veřejnou IP adresu, která byla přiřazena k bráně RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Přidružit na místní síťový web virtuální sítě
V této části určíme na místní síťový web, který chcete připojit síť VNet. V takovém případě je virtuální sítě Resource Manageru, který jste dříve odkazuje. Ujistěte se, že názvy shodovat. Tento krok není vytvořit bránu. Určuje místní sítě, kterému se bude připojovat brány.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Soubor uložte a nahrajte
Uložte soubor a potom ho importovat do Azure tak, že spustíte následující příkaz. Ujistěte se, že změníte cestu k souboru v případě potřeby pro vaše prostředí.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zobrazí se podobné výsledek zobrazující, že import proběhlo úspěšně.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Vytvoření brány

Před spuštěním tohoto příkladu, najdete v do konfiguračního souboru sítě, který jste stáhli pro přesnou názvy, které Azure očekává, že v tématu. Konfigurační soubor sítě obsahuje hodnoty pro klasické virtuální sítě. Názvy pro virtuální sítě classic se někdy při vytváření classic nastavení sítě VNet na portálu Azure kvůli rozdílům v modelech nasazení změní v konfiguračním souboru na síti. Například pokud jste použili portál Azure k vytvoření klasické virtuální sítě s názvem klasické virtuální sítě a vytvořit ve skupině prostředků s názvem 'ClassicRG', název, který je obsažen v konfiguračním souboru sítě jsou převedeny na "Skupiny ClassicRG klasické virtuální sítě". Při zadávání názvu virtuální sítě, který obsahuje mezery, použijte hodnotu do uvozovek.


Použijte následující příklad k vytvoření brány dynamického směrování:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stav brány, můžete zkontrolovat pomocí **Get-AzureVNetGateway** rutiny.

## <a name="creatermgw"></a>Oddíl 2 – konfigurace brány virtuální sítě RM
Pokud chcete vytvořit bránu VPN pro virtuální síť RM, postupujte podle následujících pokynů. Nespouštějte kroky až po načtení veřejnou IP adresu brány, klasické virtuální sítě. 

1. Přihlaste se k účtu Azure v konzole PowerShell. Následující rutiny zobrazí výzvu pro přihlašovací údaje pro účet Azure. Po přihlášení, se stáhnou nastavení svého účtu, aby byly k dispozici pro prostředí Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ``` 
   
  Načtěte seznam předplatných Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Pokud máte více než jedno předplatné, určete předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Vytvořte bránu místní sítě. Ve virtuální síti brána místní sítě obvykle odkazuje na vaše místní umístění. V takovém případě místní síťová brána odkazuje na klasické virtuální síti. Zadejte jeho název, pomocí kterého můžete Azure na ni odkazuje a také zadat předponu adresního prostoru. Azure pomocí zadané předpony IP adresy rozpozná, jaký provoz má zasílat na vaše místní umístění. Pokud potřebujete upravit informace sem později, před vytvořením brány, můžete upravit hodnoty a znovu spusťte vzorku.
   
   **-Název** je název, kterou chcete přiřadit k odkazování na bránu místní sítě.<br>
   **-AddressPrefix** adresní prostor je pro klasické virtuální síti.<br>
   **-GatewayIpAddress** je veřejná IP adresa brány klasické virtuální sítě. Nezapomeňte změnit následující ukázka tak, aby odrážela správnou IP adresu.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Požádat o veřejnou IP adresu, která bude přidělena pro bránu virtuální sítě pro virtuální sítě Resource Manageru. Nelze zadat IP adresu, kterou chcete použít. IP adresa se pro bránu virtuální sítě přidělí dynamicky. To ale neznamená, že se IP adresa změní. Změna IP adresy brány virtuální sítě je pouze při brány je odstraněno a znovu vytvořeno. Nezmění, v rámci změny velikosti, resetování nebo jiné operace údržby/upgradu brány.

  V tomto kroku jsme také nastavit proměnné, která se používá v pozdější fázi.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Ověřte, že virtuální sítě má podsíť brány. Pokud neexistuje žádná podsíť brány, přidáte. Zajistěte, aby podsíť brány je s názvem *GatewaySubnet*.
5. Načtěte podsítě používané pro bránu spuštěním následujícího příkazu. V tomto kroku jsme také nastavit proměnnou, která se použije v dalším kroku.
   
   **-Název** je název virtuální sítě Resource Manager.<br>
   **-ResourceGroupName** je skupina prostředků, který je přidružený virtuální sítě. Podsíť brány musí již existovat pro tuto virtuální síť a musí mít název *GatewaySubnet* správně fungovat.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Vytvoření konfigurace adresování IP brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následující ukázky vytvořte vlastní konfiguraci brány.

  V tomto kroku **- SubnetId** a **- PublicIpAddressId** parametry musí být předán vlastnost id z podsíť a IP adresu objekty, v uvedeném pořadí. Nelze použít jednoduchý řetězec. Tyto proměnné jsou nastavené v kroku k vyžádání veřejné IP adresy a kroku k načtení podsíť.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Spuštěním následujícího příkazu vytvořte bránu virtuální sítě Resource Manager. `-VpnType` Musí být *RouteBased*. 45 minut nebo déle pro vytvoření brány může trvat.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Po vytvoření brány sítě VPN, zkopírujte veřejnou IP adresu. Použijete jej při konfiguraci nastavení místní sítě pro vaše klasické virtuální sítě. Následující rutiny můžete získat veřejnou IP adresu. Veřejná IP adresa je uvedena v návratový jako *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Oddíl 3 - upravte nastavení místní lokality klasické virtuální sítě

V této části můžete pracovat s klasické virtuální sítě. Nahraďte zástupný symbol IP adresu, kterou jste použili při zadávání nastavení místní sítě, které se použijí pro připojení k bráně virtuální sítě Resource Manageru. 

1. Exportujte konfigurační soubor sítě.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Pomocí textového editoru, změňte hodnotu pro prvek VPNGatewayAddress. Nahraďte zástupný symbol IP adresu s veřejnou IP adresu brány, Resource Manager a potom uložte změny.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importujte konfiguračního souboru upravené sítě do Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Oddíl 4 – vytvoření připojení mezi bránami
Vytvoření připojení mezi bránami vyžaduje rozhraní PowerShell. Musíte přidat váš účet Azure použít klasickou verzi rutin prostředí PowerShell. Chcete-li to provést, použijte **Add-AzureAccount**.

1. V konzole PowerShell nastavte sdílený klíč. Před spuštěním rutin, najdete v do konfiguračního souboru sítě, který jste stáhli pro přesnou názvy, které Azure očekává, že v tématu. Při zadávání názvu virtuální sítě, který obsahuje mezery, použijte jednoduché uvozovky hodnota.<br><br>V následujícím příkladu **- VNetName** je název klasické virtuální sítě a **- LocalNetworkSiteName** je název zadaný pro místní síťový Web. **- SharedKey** je hodnota, která můžete vygenerovat a zadat. V příkladu jsme použili 'abc123', ale můžete vygenerovat a použít něco složitější. Důležité je, že hodnota, kterou tady zadáte, musí být stejnou hodnotou, kterou zadáte v dalším kroku, při vytváření připojení. Návratový by měl zobrazit **stav: úspěšné**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Vytvořte připojení k síti VPN spuštěním následujících příkazů:
   
  Nastavte proměnné.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Vytvořte připojení. Všimněte si, že **- ConnectionType** je protokol IPsec, není Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Část 5 – ověřte připojení

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Chcete-li ověřit připojení z vaší klasické virtuální sítě k virtuální síti Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Chcete-li ověřit připojení z vaší virtuální sítě Resource Manager k vaší klasické virtuální sítě

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
