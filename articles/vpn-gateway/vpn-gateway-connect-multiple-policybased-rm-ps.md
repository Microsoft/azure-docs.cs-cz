---
title: 'Připojit k více místně na základě zásad zařízení VPN Azure VPN Gateway: Azure Resource Manager: prostředí PowerShell | Microsoft Docs'
description: Konfigurace služby Azure na základě trasy VPN gateway k více na základě zásad zařízení VPN pomocí Azure Resource Manageru a prostředí PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: dc2dc660262cec892270f8d6e70691fdd169a5c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Připojení brány Azure VPN k více místně na základě zásad zařízení VPN pomocí prostředí PowerShell

Tento článek vám pomůže nakonfigurovat služby Azure na základě trasy VPN gateway se připojit k více místně na základě zásad zařízení VPN využívat vlastní zásady protokolu IPsec/IKE na připojení k síti S2S VPN.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>O zásadové a trasové brány VPN

Zásady - *oproti* zařízení VPN založené na směrování se liší v jak selektory provoz protokolu IPsec jsou nastaveny na připojení:

* **Na základě zásad** zařízení VPN použijte kombinace předpon z obou sítí můžete definovat, jak provoz je zašifrovaná nebo dešifrovat do tunelových propojení IPsec. Obvykle je založen na zařízení brány firewall, které provádějí filtrování paketů. Protokol IPsec tunel šifrování a dešifrování se přidají do paketu filtrování a zpracování modulu.
* **Založené na trasách** zařízení VPN použijte selektory provoz any-to-any (zástupný znak) a umožní směrování/předávání tabulky přímý provoz na jiné tunelových propojení IPsec. Obvykle je založen na platformách směrovače, kde je každý tunelu IPsec modelován jako síťové rozhraní nebo VTI (virtuální tunel rozhraní).

Následující diagramy zvýrazněte dva modely:

### <a name="policy-based-vpn-example"></a>Příklad sítě VPN založené na zásadách
![na základě zásad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Příklad sítě VPN založené na směrování
![založené na směrování](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Podpora Azure pro sítě VPN založené na zásadách
V současné době Azure podporuje oba režimy brány sítě VPN: brány sítě VPN založené na směrování a bran VPN na základě zásad. Tyto šablony jsou sestaveny na různých platformách interní, které mít za následek jiné specifikace:

|                          | **Brána sítě VPN PolicyBased** | **Brána sítě VPN RouteBased**               |
| ---                      | ---                         | ---                                      |
| **Služba Azure Gateway SKU**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Verze IKE**          | IKEv1                       | IKEv2                                    |
| **Max. Připojení S2S** | **1**                       | Basic nebo Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Pomocí vlastních zásad protokolu IPsec/IKE, teď můžete konfigurovat Azure brány sítě VPN založené na směrování a použít na základě předpony provoz selektory spolu s možností "**PolicyBasedTrafficSelectors**", pro připojení k místní zařízení VPN na základě zásad. Díky této funkci můžete pro připojení z virtuální sítě Azure a brány VPN k více místně na základě zásad zařízení VPN nebo brány firewall odebrání limit jednoho připojení z aktuální Azure na základě zásad VPN Gateway.

> [!IMPORTANT]
> 1. Pokud chcete povolit toto připojení, musí podporovat zařízení sítě VPN založené na zásadách místní **IKEv2** pro připojení k Azure brány sítě VPN založené na trasách. Zkontrolujte dokumentaci sítě VPN.
> 2. Místní sítě, připojení prostřednictvím zařízení VPN založené na zásadách s Tento mechanismus lze připojit pouze k virtuální síti Azure; **nelze přenosu do jiných sítích na pracovišti nebo virtuální sítě prostřednictvím tutéž bránu Azure VPN**.
> 3. Možnost konfigurace je součástí vlastní zásady protokolu IPsec/IKE připojení. Pokud povolíte možnost selektoru provozu na základě zásad, musíte zadat dokončení zásady (algoritmy šifrování a integrity protokolu IPsec/IKE, klíče síly a životnost přidružení zabezpečení).

Následující diagram ukazuje proč směrování přenosu prostřednictvím brány Azure VPN nefunguje s možností na základě zásad:

![na základě zásad přenosu](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak je vidět v diagramu, má bránu Azure VPN selektory provoz z virtuální sítě pro každé předpony místní sítě, ale není předpony křížové připojení. Například místní lokality 2, 3 lokality a lokality 4 můžete každý sdělit VNet1 v uvedeném pořadí, ale nemůže připojit prostřednictvím brány Azure VPN k sobě navzájem. Diagram znázorňuje selektory cross-connect přenosy, které nejsou k dispozici ve službě Azure VPN gateway v této konfiguraci.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Konfigurace založená na zásadách provoz selektory na připojení

Podle pokynů v tomto článku podle stejné příklad, jak je popsáno v [zásady Konfigurace protokolu IPsec/IKE pro připojení S2S nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) k navázání připojení S2S VPN. To je ukázáno v následujícím diagramu:

![zásady s2s](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

V pracovním postupu povolte tyto možnosti připojení:
1. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě pro připojení mezi různými místy
2. Vytvoření zásady protokolu IPsec/IKE
3. Použít zásady při vytváření připojení S2S nebo VNet-to-VNet a **povolit provoz na základě zásad selektory** k připojení
4. Pokud je již vytvořili připojení, můžete použít nebo aktualizaci zásad na existující připojení

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Povolit provoz na základě zásad selektory na připojení

Ujistěte se, když jste dokončili [část 3 zásady článku Konfigurace protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) pro tento oddíl. Následující příklad používá stejné parametry a kroky:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Deklarace proměnných a připojení k vašemu předplatnému
Pro toto cvičení začneme deklarací proměnných. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```
Pokud chcete používat rutiny Resource Manageru, ujistěte se, že jste přešli do režimu prostředí PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě
Následující příklad vytvoří virtuální síť, virtuální sítě TestVNet1 s tři podsítě a bránu VPN. Při nahrazování hodnot je důležité vždy pojmenovat podsítě brány konkrétně GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 – Vytvoření připojení S2S VPN pomocí zásad protokolu IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásady protokolu IPsec/IKE

> [!IMPORTANT]
> Musíte vytvořit zásady protokolu IPsec/IKE. Chcete-li povolit možnost "UsePolicyBasedTrafficSelectors" k připojení.

Následující příklad vytvoří zásadu protokolu IPsec/IKE se tyto algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* Protokol IPsec: AES256, SHA256, PFS24 SA životnost 3600 sekund & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Vytvoření připojení k síti VPN S2S s selektory provozu na základě zásad a zásad protokolu IPsec/IKE
Vytvoření připojení k síti VPN S2S a použití zásady protokolu IPsec/IKE vytvořili v předchozím kroku. Mějte na paměti další parametru "-UsePolicyBasedTrafficSelectors $True" což umožňuje na základě zásad provoz selektory pro připojení.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po dokončení těchto kroků, připojení k síti S2S VPN používat definované zásady protokolu IPsec/IKE a povolit provoz na základě zásad selektory pro připojení. Stejný postup pro přidání více připojení do dalších místních zařízení VPN založené na zásadách ze tutéž bránu Azure VPN, můžete opakovat.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualizace na základě zásad provoz selektory pro připojení
Poslední části se dozvíte, jak aktualizovat možnost selektory provozu na základě zásad pro existující připojení S2S VPN.

### <a name="1-get-the-connection"></a>1. Získání připojení
Získáte prostředek připojení.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Zaškrtněte možnost selektory provozu na základě zásad
Následující řádek ukazuje, zda na základě zásad provoz selektory se používají pro připojení:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Pokud vrátí řádek "**True**", pak selektory založené na zásadách provozu se konfigurují na připojení; jinak vrátí hodnotu "**False**."

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. Aktualizovat selektory provozu na základě zásad v připojení
Jakmile je získat prostředek připojení, můžete povolit nebo zakázat možnost.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Zakázat UsePolicyBasedTrafficSelectors
Následující příklad zakáže možnost selektory provozu na základě zásad, ale ponechá zásad protokolu IPsec/IKE beze změny:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>Povolit UsePolicyBasedTrafficSelectors
Následující příklad umožňuje selektory provozu na základě zásad, ale ponechá zásad protokolu IPsec/IKE beze změny:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Další postup
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Také zkontrolujte [zásady Konfigurace protokolu IPsec/IKE pro připojení S2S VPN nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) Další informace o vlastních zásad protokolu IPsec/IKE.
