---
title: 'Připojení místní sítě k virtuální síti Azure: Síť VPN typu Site-to-Site: PowerShell | Dokumentace Microsoftu'
description: Postup vytvoření připojení IPsec z vaší místní sítě k virtuální síti Azure přes veřejný internet. Tyto kroky vám pomůžou vytvořit připojení VPN Gateway typu Site-to-Site mezi různými místy pomocí PowerShellu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2018
ms.author: cherylmc
ms.openlocfilehash: 84df8b286bd9f3ae3012f297c3a929ef57369ff6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Vytvoření virtuální sítě pomocí připojení VPN Site-to-Site s použitím prostředí PowerShell

Tento článek ukazuje, jak pomocí PowerShellu vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.
* Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Rutiny PowerShellu se často aktualizují a obvykle bude třeba rutiny PowerShellu aktualizovat, abyste získali nejnovější funkce. Pokud rutiny PowerShellu neaktualizujete, zadané hodnoty nemusí fungovat. Další informace o stažení a instalaci rutin PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

### <a name="example"></a>Příklady hodnot

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1. Připojení k vašemu předplatnému

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Vytvoření virtuální sítě a podsítě brány

Pokud ještě nemáte virtuální síť, vytvořte si ji. Při vytváření virtuální sítě ověřte, že se zadané adresní prostory nepřekrývají s adresními prostory ve vaší místní síti. 

>[!NOTE]
>Aby se tato virtuální síť připojila k místnímu umístění, budete se muset domluvit se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se nemusí směrovat očekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho pečlivě naplánujte konfiguraci sítě.
>
>

### <a name="about-the-gateway-subnet"></a>O podsíti brány

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Vytvoření virtuální sítě a podsítě brány

Tento příklad vytvoří virtuální síť a podsíť brány. Pokud již máte virtuální síť, do které potřebujete přidat podsíť brány, přejděte k části [Chcete-li přidat podsíť brány k již vytvořené virtuální síti](#gatewaysubnet).

Vytvořte skupinu prostředků:

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

Vytvořte virtuální síť.

1. Nastavte proměnné.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. Vytvořte virtuální síť.

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Chcete přidat podsíť brány k již vytvořené virtuální síti

1. Nastavte proměnné.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. Vytvořte podsíť brány.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
  ```
3. Nastavte konfiguraci.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Vytvoření brány místní sítě

Brána místní sítě obvykle odkazuje na vaše místní umístění. Pro toto umístění určíte název, podle kterého na něj bude Azure odkazovat, a pak zadáte IP adresu místního zařízení VPN, ke kterému vytvoříte připojení. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. V případě změny vaší místní sítě můžete tyto předpony snadno aktualizovat.

Použijte následující hodnoty:

* *GatewayIPAddress* je IP adresa vašeho místního zařízení VPN. Zařízení VPN nesmí být umístěné za překladem adres (NAT).
* *AddressPrefix* je váš místní adresní prostor.

Chcete-li přidat bránu místní sítě s jednou předponou adresy:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

Chcete-li přidat bránu místní sítě s více předponami adresy:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

Chcete-li upravit předpony IP adres pro bránu místní sítě:<br>
Někdy dojde ke změně předpon brány místní sítě. Postup upravení předpon IP adresy závisí na tom, zda jste vytvořili připojení k bráně VPN. Viz oddíl [Úprava předpon IP adresy pro bránu místní sítě](#modify) v tomto článku.

## <a name="PublicIP"></a>4. Vyžádání veřejné IP adresy

Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

Vyžádejte si veřejnou IP adresu, která bude přiřazena k bráně VPN vaší virtuální sítě.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Vytvoření konfigurace adresování IP brány

Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následujícího příkladu vytvořte vlastní konfiguraci brány:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Vytvoření brány VPN

Vytvořte bránu VPN virtuální sítě. Vytvoření brány VPN může trvat až 45 minut nebo ještě déle.

Použijte následující hodnoty:

* Hodnota *-GatewayType* pro konfiguraci Site-to-Site je *Vpn*. Typ brány je vždy specifický pro konfiguraci, kterou implementujete. Například jiné konfigurace brány mohou vyžadovat jako -GatewayType hodnotu ExpressRoute.
* Hodnota *-VpnType* může být *RouteBased* (v některé dokumentaci nazývaná Dynamická brána), nebo *PolicyBased* (v některé dokumentaci nazývaná Statická brána). Další informace o typech brány VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).
* Vyberte SKU brány, kterou chcete použít. Pro určité skladové jednotky (SKU) platí omezení konfigurace. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pokud při vytváření brány VPN dojde k chybě související s parametrem -GatewaySku, ověřte, že máte nainstalovanou nejnovější verzi rutin PowerShellu.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7. Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány virtuální sítě pomocí PowerShellu, použijte následující příklad:

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8. Vytvoření připojení VPN

Dále vytvoříte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a zařízením VPN. Nezapomeňte hodnoty nahradit vlastními. Sdílený klíč se musí shodovat s hodnotou, kterou jste použili pro konfiguraci zařízení VPN. Všimněte si, že hodnota -ConnectionType pro připojení typu Site-to-Site je *IPsec*.

1. Nastavte proměnné.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. Vytvořte připojení.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Za malou chvíli dojde k vytvoření připojení.

## <a name="toverify"></a>9. Ověření připojení VPN

Existuje několik různých způsobů, jak ověřit připojení VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Úprava předpon IP adres pro bránu místní sítě

Pokud se změní předpony IP adres, které chcete směrovat do vašeho místního umístění. můžete upravit bránu místní sítě. K dispozici jsou dvě sady pokynů. Pokyny, které zvolíte, závisí na tom, jestli jste už vytvořili připojení brány.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Úprava IP adresy brány místní sítě

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Další kroky

*  Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vytvoření připojení VPN typu Site-to-Site pomocí šablony Azure Resource Manageru najdete v tématu [Vytvoření připojení VPN typu Site-to-Site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Informace o vytvoření připojení VPN typu VNet-to-VNet pomocí šablony Azure Resource Manageru najdete v tématu [Nasazení geografické replikace HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
