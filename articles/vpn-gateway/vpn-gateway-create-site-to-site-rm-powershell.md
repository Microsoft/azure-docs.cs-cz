---
title: 'Připojení místní sítě k virtuální síti Azure: síť VPN typu Site-to-site: PowerShell'
description: Vytvořte připojení Site-to-Site VPN Gateway s protokolem IPsec z místní sítě k virtuální síti Azure přes veřejný Internet pomocí PowerShellu.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 1488aa6f48c05a8c2dfa2c6162c1bd1df35d4f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380478"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Vytvoření virtuální sítě pomocí připojení VPN Site-to-Site s použitím prostředí PowerShell

Tento článek ukazuje, jak pomocí PowerShellu vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN.
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. vytvoření virtuální sítě a podsítě brány

Pokud ještě nemáte virtuální síť, vytvořte si ji. Při vytváření virtuální sítě ověřte, že se zadané adresní prostory nepřekrývají s adresními prostory ve vaší místní síti. 

>[!NOTE]
>Aby se tato virtuální síť připojila k místnímu umístění, budete se muset domluvit se správcem vaší místní sítě a vyčlenit rozsah IP adres, který můžete použít speciálně pro tuto virtuální síť. Pokud existuje duplicitní rozsah adres na obou stranách připojení VPN, provoz se nemusí směrovat očekávaným způsobem. Pokud navíc chcete připojit tuto virtuální síť k jiné virtuální síti, adresní prostor se nesmí překrývat s jinou virtuální sítí. Podle toho pečlivě naplánujte konfiguraci sítě.
>
>

### <a name="about-the-gateway-subnet"></a>O podsíti brány

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>Vytvoření virtuální sítě a podsítě brány

Tento příklad vytvoří virtuální síť a podsíť brány. Pokud již máte virtuální síť, do které potřebujete přidat podsíť brány, přejděte k části [Chcete-li přidat podsíť brány k již vytvořené virtuální síti](#gatewaysubnet).

Vytvořte skupinu prostředků:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Vytvořte virtuální síť.

1. Nastavte proměnné.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Vytvořte virtuální síť.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Chcete přidat podsíť brány k již vytvořené virtuální síti

Postup v této části použijte v případě, že už máte virtuální síť, ale potřebujete přidat podsíť brány.

1. Nastavte proměnné.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Vytvořte podsíť brány.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Nastavte konfiguraci.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a> vytvořte bránu místní sítě.

Brána místní sítě (LNG) obvykle odkazuje na vaše místní umístění. Nejedná se o stejnou bránu virtuální sítě. Pro toto umístění určíte název, podle kterého na něj bude Azure odkazovat, a pak zadáte IP adresu místního zařízení VPN, ke kterému vytvoříte připojení. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. V případě změny vaší místní sítě můžete tyto předpony snadno aktualizovat.

Použijte následující hodnoty:

* *GatewayIPAddress* je IP adresa vašeho místního zařízení VPN.
* *AddressPrefix* je váš místní adresní prostor.

Chcete-li přidat bránu místní sítě s jednou předponou adresy:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Chcete-li přidat bránu místní sítě s více předponami adresy:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Chcete-li upravit předpony IP adres pro bránu místní sítě:

Někdy dojde ke změně předpon brány místní sítě. Postup upravení předpon IP adresy závisí na tom, zda jste vytvořili připojení k bráně VPN. Viz oddíl [Úprava předpon IP adresy pro bránu místní sítě](#modify) v tomto článku.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. vyžádání veřejné IP adresy

Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. 

Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To však neznamená, že se IP adresa po přiřazení k vaší bráně VPN změní. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

Vyžádejte si veřejnou IP adresu, která bude přiřazena k bráně VPN vaší virtuální sítě.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. vytvoření konfigurace adresování IP brány

Konfigurace brány definuje podsíť (GatewaySubnet) a veřejnou IP adresu, která se má použít. Podle následujícího příkladu vytvořte vlastní konfiguraci brány:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. Vytvoření brány VPN

Vytvořte bránu VPN virtuální sítě.

Použijte následující hodnoty:

* Hodnota *-GatewayType* pro konfiguraci Site-to-Site je *Vpn*. Typ brány je vždy specifický pro konfiguraci, kterou implementujete. Například jiné konfigurace brány mohou vyžadovat jako -GatewayType hodnotu ExpressRoute.
* Hodnota *-VpnType* může být *RouteBased* (v některé dokumentaci nazývaná Dynamická brána), nebo *PolicyBased* (v některé dokumentaci nazývaná Statická brána). Další informace o typech brány VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).
* Vyberte SKU brány, kterou chcete použít. Pro určité skladové jednotky (SKU) platí omezení konfigurace. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Pokud při vytváření brány VPN dojde k chybě související s parametrem -GatewaySku, ověřte, že máte nainstalovanou nejnovější verzi rutin PowerShellu.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

Po spuštění tohoto příkazu může dokončení konfigurace brány trvat až 45 minut.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující položky:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány virtuální sítě pomocí PowerShellu, použijte následující příklad. V tomto příkladu je VNet1GWPIP názvem prostředku veřejné IP adresy, který jste vytvořili v předchozím kroku.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. vytvoření připojení k síti VPN

Dále vytvoříte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a zařízením VPN. Hodnoty na obrázcích nahraďte vlastními hodnotami. Sdílený klíč se musí shodovat s hodnotou, kterou jste použili pro konfiguraci zařízení VPN. Všimněte si, že hodnota -ConnectionType pro připojení typu Site-to-Site je **IPsec**.

1. Nastavte proměnné.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Vytvořte připojení.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

Za malou chvíli dojde k vytvoření připojení.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. ověření připojení VPN

Existuje několik různých způsobů, jak ověřit připojení VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Úprava předpony IP adresy pro místní síťovou bránu

Pokud se změní předpony IP adres, které chcete směrovat do vašeho místního umístění. můžete upravit bránu místní sítě. Při použití těchto příkladů upravte hodnoty tak, aby odpovídaly vašemu prostředí.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Změna IP adresy místní síťové brány

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>Odstranění připojení brány

Pokud neznáte název připojení, můžete ho najít pomocí rutiny Get-AzVirtualNetworkGatewayConnection.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>Další kroky

*  Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](../index.yml).
* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vytvoření připojení VPN typu Site-to-site pomocí šablony Azure Resource Manager najdete v tématu [vytvoření připojení VPN typu Site-to-site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Informace o vytvoření připojení VPN typu VNet-to-VNet pomocí šablony Azure Resource Manager najdete v tématu [nasazení HBA pro geografickou replikaci](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).