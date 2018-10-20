---
title: 'Propojení virtuální sítě Azure s jinou virtuální síti s použitím připojení typu VNet-to-VNet: PowerShell | Dokumentace Microsoftu'
description: Propojení virtuálních sítí s použitím připojení typu VNet-to-VNet a PowerShellu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/14/2018
ms.author: cherylmc
ms.openlocfilehash: a6bdafffb11fce40ecdd876105e17edd2660c751
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466899"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Konfigurace připojení brány VPN typu VNet-to-VNet pomocí PowerShellu

Tento článek vám pomůže propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Pokud připojujete virtuální sítě z různých předplatných, tato předplatná nemusí být přidružená ke stejnému tenantovi Active Directory.

Postupy v tomto článku se týkají modelu nasazení Resource Manager a používají PowerShell. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>Informace o propojování virtuálních sítí

Existuje více způsobů propojení virtuálních sítí. Následující oddíly popisují různé způsoby připojení virtuálních sítí.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení VNet-to-VNet je dobrým způsobem, jak snadno připojit virtuální sítě. Propojení virtuální sítě s jinou virtuální sítí s použitím typu připojení VNet-to-VNet je podobné jako vytvoření připojení Site-to-Site IPsec k místnímu umístění.  Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE a oba komunikují stejným způsobem. Rozdílem mezi těmito typy připojení je způsob konfigurace místní síťové brány. Při vytváření připojení typu VNet-to-VNet se nezobrazí adresní prostor místní síťové brány. Vytvoří a naplní se automaticky. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť bude automaticky znát trasu do aktualizovaného adresního prostoru. Vytvoření připojení VNet-to-VNet je obvykle rychlejší a snazší než vytvoření připojení Site-to-Site mezi virtuálními sítěmi.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Pokud pracujete se složitou konfigurací sítě, můžete chtít vaše virtuální sítě raději připojit pomocí kroků [Site-to-Site](vpn-gateway-create-site-to-site-rm-powershell.md) než pomocí kroků VNet-to-VNet. Při použití kroků Site-to-Site vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Ten vám umožní pro místní síťovou bránu zadat další adresní prostor pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte aktualizovat odpovídající bránu místní sítě tak, aby tuto změnu odrážela. Nedojde k automatické aktualizaci.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Můžete chtít zvážit připojení vašich virtuálních sítí pomocí VNET Peering. VNET Peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Proč vytvářet připojení typu VNet-to-VNet?

Virtuální sítě může být vhodné propojit pomocí připojení VNet-to-VNet z následujících důvodů:

* **Geografická redundance napříč oblastmi a geografická přítomnost**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * Pomocí Azure Traffic Manageru a služby Load Balancer je možné vytvářet úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s izolací nebo administrativní hranicí**

  * V rámci stejné oblasti můžete vytvářet vícevrstvé aplikace s několika virtuálními sítěmi propojenými z důvodu izolace nebo požadavků na správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

## <a name="steps"></a>Které kroky VNet-to-VNet mám použít?

V tomto článku uvidíte dvě různé sady kroků. Jedna sada kroků pro [virtuální sítě patřící do stejného předplatného](#samesub) a jedna pro [virtuální sítě patřící do různých předplatných](#difsub).
Klíčovým rozdílem mezi těmito sadami je, že pokud konfigurujete připojení pro virtuální sítě, které patří do různých předplatných, musíte použít samostatné relace PowerShellu. 

Pro toto cvičení můžete konfigurace kombinovat nebo prostě vybrat tu, se kterou chcete pracovat. Všechny konfigurace používají typ připojení VNet-to-VNet. Provoz probíhá mezi virtuálními sítěmi, které jsou vzájemně přímo propojené. V tomto cvičení se provoz ze sítě TestVNet4 nesměruje do sítě TestVNet5.

* [Virtuální sítě patřící do stejného předplatného:](#samesub) V postupu pro tuto konfiguraci se používají sítě TestVNet1 a TestVNet4.

  ![Diagram v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Virtuální sítě patřící do různých předplatných:](#difsub) V postupu pro tuto konfiguraci se používají sítě TestVNet1 a TestVNet5.

  ![Diagram v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Postup při propojování virtuálních sítí patřících ke stejnému předplatnému

### <a name="before-you-begin"></a>Než začnete

Než začnete, bude třeba nainstalovat nejnovější verzi rutin PowerShellu pro Azure Resource Manager, alespoň verzi 4.0 nebo novější. Další informace o instalaci rutin PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

### <a name="Step1"></a>Krok 1: Plánování rozsahů IP adres

V následujících krocích vytvoříte dvě virtuální sítě spolu s příslušnými podsítěmi a konfiguracemi brány. Potom vytvoříte propojení VPN mezi oběma virtuálními sítěmi. Je důležité určit rozsahy IP adres pro konfiguraci vaší sítě. Mějte na paměti, že je třeba zajistit, aby se žádné rozsahy virtuálních sítí ani místní síťové rozsahy žádným způsobem nepřekrývaly. V těchto příkladech nezahrnujeme server DNS. Pokud chcete překlad IP adres pro virtuální sítě, přečtěte si téma [Překlad IP adres](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

V příkladech používáme následující hodnoty:

**Hodnoty pro virtuální síť TestVNet1:**

* Název virtuální sítě: TestVNet1
* Skupina prostředků: TestRG1
* Umístění: Východní USA
* TestVNet1: 10.11.0.0/16 a 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* Název brány: VNet1GW
* Veřejná IP adresa: VNet1GWIP
* Typ sítě VPN: RouteBased
* Připojení (1 ke 4): VNet1toVNet4
* Připojení (1 k 5): VNet1toVNet5 (pro virtuální sítě v různých předplatných)
* Typ připojení: VNet2VNet

**Hodnoty pro virtuální síť TestVNet4:**

* Název virtuální sítě: TestVNet4
* TestVNet2: 10.41.0.0/16 a 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Skupina prostředků: TestRG4
* Umístění: Západní USA
* Název brány: VNet4GW
* Veřejná IP adresa: VNet4GWIP
* Typ sítě VPN: RouteBased
* Připojení: VNet4toVNet1
* Typ připojení: VNet2VNet


### <a name="Step2"></a>Krok 2: Vytvoření a konfigurace virtuální sítě TestVNet1

1. Deklarujte proměnné. V tomto příkladu jsou proměnné deklarovány s použitím hodnot pro tento ukázkový postup. Ve většině případů byste měli hodnoty nahradit vlastními. Tyto hodnoty proměnných ale můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné podle potřeby a pak je zkopírujte a vložte do konzoly PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```powershell
  Connect-AzureRmAccount
  ```

  Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```

  Určete předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. Vytvořte novou skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Vytvořte konfigurace podsítí pro virtuální síť TestVNet1. Tato ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem BackEnd. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

  Následující příklad používá proměnné, které jste nastavili dříve. V příkladu používá podsíť brány možnost /27. I když je možné vytvořit podsíť brány s minimální velikostí /29, doporučujeme vytvořit větší podsíť, která pojme více adres, tzn. vybrat velikost alespoň /28 nebo /27. Tím vznikne dostatečný prostor pro adresy, který umožní nastavení případných dalších konfigurací v budoucnu.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Vytvořte virtuální síť TestVNet1.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Vyžádejte si veřejnou IP adresu, která bude přidělena bráně, kterou vytvoříte pro příslušnou virtuální síť. Všimněte si, že metoda AllocationMethod je dynamická. Není možné určit IP adresu, kterou chcete používat. Přiděluje se pro bránu dynamicky. 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Vytvořte konfiguraci brány. Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následující ukázky vytvořte vlastní konfiguraci brány.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Vytvořte bránu pro virtuální síť TestVNet1. V tomto kroku vytvoříte bránu virtuální sítě pro virtuální síť TestVNet1. Konfigurace propojení VNet-to-VNet vyžadují typ sítě VPN RouteBased. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Krok 3: Vytvoření a konfigurace virtuální sítě TestVNet4

Po konfiguraci virtuální sítě TestVNet1 vytvořte virtuální síť TestVNet4. Postupujte podle následujících kroků a podle potřeby nahrazujte hodnoty vlastními. Tento krok lze provést v rámci stejné relace prostředí PowerShell, protože se jedná o stejné předplatné.

1. Deklarujte proměnné. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. Vytvořte novou skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Vytvořte konfigurace podsítí pro virtuální síť TestVNet4.

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Vytvořte virtuální síť TestVNet4.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Vyžádejte si veřejnou IP adresu.

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Vytvořte konfiguraci brány.

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Vytvořte bránu virtuální sítě TestVNet4. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>Krok 4: Vytvoření připojení

1. Získejte obě brány virtuální sítě. Pokud jsou obě brány ve stejném předplatném, jako je tomu v příkladu, můžete tento krok dokončit ve stejné relaci PowerShellu.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet4. V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do virtuální sítě TestVNet4. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Vytvořte připojení virtuální sítě TestVNet4 k virtuální síti TestVNet1. Tento krok je podobný předchozímu, vytváříte však připojení z virtuální sítě TestVNet4 do virtuální sítě TestVNet1. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. Ověřte své propojení. Viz část [Ověření připojení](#verify).

## <a name="difsub"></a>Postup při propojování virtuálních sítí patřících k různým předplatným

V tomto scénáři propojíte sítě TestVNet1 a TestVNet5. Virtuální sítě TestVNet1 a TestVNet5 patří do různých předplatných. Předplatná nemusí být přidružená ke stejnému tenantovi Active Directory. Rozdíl mezi těmito kroky a předchozí sadou spočívá v tom, že část kroků konfigurace je třeba provést v samostatné relaci PowerShellu v kontextu druhého předplatného. To je zvláště podstatné, když druhé předplatné patří jiné organizaci.

### <a name="step-5---create-and-configure-testvnet1"></a>Krok 5: Vytvoření a konfigurace virtuální sítě TestVNet1

Je třeba vytvořit a konfigurovat virtuální síť TestVNet1 a bránu VPN Gateway pro virtuální síť TestVNet1 provedením [kroku 1](#Step1) a [kroku 2](#Step2) z předchozí části. Pro tuto konfiguraci není nutné vytvářet virtuální síť TestVNet4 z předchozí části, ale pokud ji vytvoříte, nebude to s těmito kroky v konfliktu. Po dokončení kroků 1 a 2 pokračujte krokem 6 a vytvořte síť TestVNet5. 

### <a name="step-6---verify-the-ip-address-ranges"></a>Krok 6: Ověření rozsahů IP adres

Je důležité zajistit, aby se prostor IP adres nové virtuální sítě TestVNet5 nepřekrýval se žádným z rozsahů virtuálních sítí ani rozsahů bran místních sítí. V tomto příkladu můžou virtuální sítě různým organizacím. Pro tento postup použijte následující hodnoty pro virtuální síť TestVNet5:

**Hodnoty pro virtuální síť TestVNet5:**

* Název virtuální sítě: TestVNet5
* Skupina prostředků: TestRG5
* Umístění: Japonsko – východ
* TestVNet5: 10.51.0.0/16 a 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* Název brány: VNet5GW
* Veřejná IP adresa: VNet5GWIP
* Typ sítě VPN: RouteBased
* Připojení: VNet5toVNet1
* Typ připojení: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Krok 7: Vytvoření a konfigurace virtuální sítě TestVNet5

Tento krok je třeba provést v rámci nového předplatného. Tuto část může provést správce v organizaci, která je vlastníkem druhého předplatného.

1. Deklarujte proměnné. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Připojte se k předplatnému 5. Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```powershell
  Connect-AzureRmAccount
  ```

  Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```

  Určete předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Vytvořte novou skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Vytvořte konfigurace podsítí pro virtuální síť TestVNet5.

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Vytvořte virtuální síť TestVNet5.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Vyžádejte si veřejnou IP adresu.

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Vytvořte konfiguraci brány.

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Vytvořte bránu virtuální sítě TestVNet5.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>Krok 8: Vytvoření připojení

Jelikož brány v tomto příkladu patří do různých předplatných, rozdělíme tento krok do dvou relací prostředí PowerShell označených [Předplatné 1] a [Předplatné 5].

1. **[Předplatné 1]** Získejte bránu virtuální sítě pro předplatné 1. Před spuštěním následujícího příkladu se přihlaste a připojte k předplatnému 1:

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  Zkopírujte výstup následujících prvků a pošlete je správci předplatného 5 prostřednictvím e-mailu nebo jiným způsobem.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  Tyto dva prvky budou mít hodnoty podobné výstupu v následujícím příkladu:

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Předplatné 5]** Získejte bránu virtuální sítě pro předplatné 5. Před spuštěním následujícího příkladu se přihlaste a připojte k předplatnému 5:

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  Zkopírujte výstup následujících prvků a pošlete jej správci předplatného 1 prostřednictvím e-mailu nebo jiným způsobem.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  Tyto dva prvky budou mít hodnoty podobné výstupu v následujícím příkladu:

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Předplatné 1]** Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet5. V tomto kroku vytvoříte propojení z virtuální sítě TestVNet1 do sítě TestVNet5. Rozdíl zde spočívá v tom, že hodnotu $vnet5gw nelze získat přímo, protože patří do jiného předplatného. Je třeba vytvořit nový objekt prostředí PowerShell s hodnotami zjištěnými z předplatného 1 v předchozích krocích. Postupujte podle následujícího příkladu. Nahraďte název, ID a sdílený klíč vlastními hodnotami. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

  Před spuštěním následujícího příkladu se připojte k předplatnému 1:

  ```powershell
  $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Předplatné 5]** Vytvořte připojení virtuální sítě TestVNet5 k virtuální síti TestVNet1. Tento krok je podobný předchozímu, vytváříte však připojení z virtuální sítě TestVNet5 do virtuální sítě TestVNet1. Stejný postup vytváření objektu prostředí PowerShell na základě hodnot zjištěných z předplatného 1 se používá i zde. V tomto kroku ověřte, že se sdílené klíče shodují.

  Před spuštěním následujícího příkladu se připojte k předplatnému 5:

  ```powershell
  $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  $Connection51 = "VNet5toVNet1"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Ověření připojení

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další postup

* Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v [dokumentaci ke službě Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
