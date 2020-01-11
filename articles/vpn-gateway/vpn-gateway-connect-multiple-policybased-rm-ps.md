---
title: 'Azure VPN Gateway: připojení bran k několika místním zařízením VPN založeným na zásadách'
description: Nakonfigurujte bránu VPN založenou na směrováních Azure na několik zařízení VPN založených na zásadách pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: 029b7ce31e7e3f1c71d9fbe806b2d76d8a98d24b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863736"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Připojení bran Azure VPN k několika místním zařízením VPN založeným na zásadách pomocí PowerShellu

Tento článek vám pomůže nakonfigurovat bránu sítě VPN založenou na trasách Azure pro připojení k několika místním zařízením VPN založeným na zásadách využívajících vlastní zásady IPsec/IKE na připojení S2S VPN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>O bránách sítě VPN založených na zásadách a směrování

Zásady a zařízení VPN založená *na trasách* se liší v tom, jak jsou selektory přenosu protokolu IPSec nastaveny u připojení:

* **Založené na zásadách** Zařízení VPN používají kombinace předpon z obou sítí k definování způsobu šifrování/dešifrování provozu prostřednictvím tunelů IPsec. Obvykle je postaven na zařízeních brány firewall, která provádějí filtrování paketů. Šifrování a dešifrování tunelů IPsec se přidává do modulu filtrování a zpracování paketů.
* **Založené na trasách** Zařízení VPN používají selektory přenosu libovolných a všech (se zástupnými znaky) a umožňují směrování a předávání dat směrovat do různých tunelů IPsec. Obvykle je postaven na platformách směrovačů, kde každé tunelové propojení IPsec je modelováno jako síťové rozhraní nebo VTI (virtuální tunelové rozhraní).

Následující diagramy zvýrazňují dva modely:

### <a name="policy-based-vpn-example"></a>Příklad sítě VPN založené na zásadách
![založené na zásadách](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Příklad sítě VPN založené na trasách
![založené na trasách](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Podpora Azure pro síť VPN založená na zásadách
V současné době podporuje Azure oba režimy bran sítě VPN: brány sítě VPN založené na směrováních a brány VPN založené na zásadách. Jsou postavené na různých interních platformách, což vede k různým specifikacím:

|                          | **PolicyBased VPN Gateway** | **RouteBased VPN Gateway**       |**RouteBased VPN Gateway**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **SKU brány Azure**    | Úroveň Basic                       | Úroveň Basic                            | Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3  |
| **Verze protokolu IKE**          | IKEv1                       | IKEv2                            | IKEv1 a IKEv2                                    |
| **Počet. Připojení S2S** | **1**                       | 10                               |Standard: 10<br> Jiné SKU: 30                     |
|                          |                             |                                  |                                                    |

Pomocí vlastních zásad IPsec/IKE teď můžete nakonfigurovat brány sítě VPN založené na trasách, aby používaly selektory přenosu založené na předponách s možností "**PolicyBasedTrafficSelectors**", aby se připojovaly k místním zařízením VPN založeným na zásadách. Tato funkce umožňuje připojit se z virtuální sítě Azure a brány VPN k několika místním zařízením VPN a firewallem založeným na zásadách a odebrat omezení jediného připojení z aktuálních bran sítě VPN založených na zásadách Azure.

> [!IMPORTANT]
> 1. Pokud chcete toto připojení povolit, musí vaše místní zařízení VPN založená na zásadách podporovat protokol **IKEv2** pro připojení k branám VPN založeným na trasách Azure. Podívejte se na specifikace zařízení VPN.
> 2. Místní sítě, které se připojují prostřednictvím zařízení VPN na základě zásad s tímto mechanismem, se můžou připojit jenom k virtuální síti Azure. **nemůžou přes stejnou bránu Azure VPN využít k přenosu do jiných místních sítí ani virtuálních sítí**.
> 3. Možnost konfigurace je součástí vlastních zásad připojení IPsec/IKE. Pokud povolíte možnost výběru provozu na základě zásad, musíte zadat kompletní zásady (šifrování IPsec/IKE a algoritmy integrity, síly klíčů a životnosti SA).

Následující diagram ukazuje, proč přenosové směrování přes Azure VPN Gateway nefunguje s možností založenou na zásadách:

![přenos na základě zásad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak je znázorněno v diagramu, brána Azure VPN Gateway selektory přenosu dat z virtuální sítě do každé z prefixů místní sítě, ale ne z předpon mezi připojeními. Například místní lokalita 2, lokalita 3 a site 4 může komunikovat s VNet1, ale nemůže se vzájemně připojit přes bránu Azure VPN. Diagram zobrazuje selektory přenosů mezi připojeními, které nejsou v bráně Azure VPN v rámci této konfigurace k dispozici.

## <a name="configurepolicybased"></a>Konfigurace selektorů přenosu na základě zásad pro připojení

Pokyny v tomto článku se řídí stejným příkladem, jak je popsáno v tématu [Konfigurace zásad IPSec/IKE pro připojení S2S nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) pro vytvoření připojení S2S VPN. To je znázorněno v následujícím diagramu:

![S2S – zásady](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Pracovní postup pro povolení tohoto připojení:
1. Vytvořte virtuální síť, bránu sítě VPN a bránu místní sítě pro připojení mezi různými místy.
2. Vytvoření zásady IPsec/IKE
3. Zásady použijte při vytváření připojení S2S nebo VNet-to-VNet a **Povolte selektory provozu na základě zásad** připojení.
4. Pokud je připojení už vytvořené, můžete zásady použít nebo aktualizovat na existující připojení.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Povolit pro připojení selektory přenosu na základě zásad

Ujistěte se, že jste pro tuto část dokončili [část 3 článku Konfigurace zásad IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) . Následující příklad používá stejné parametry a kroky:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – vytvoření virtuální sítě, brány sítě VPN a brány místní sítě

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Připojte se k předplatnému a deklarujte proměnné.

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Deklarujte proměnné. Pro toto cvičení používáme následující proměnné:

```azurepowershell-interactive
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

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Vytvořte virtuální síť, bránu VPN a bránu místní sítě.

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Pomocí následujícího příkladu vytvořte virtuální síť virtuální sítě testvnet1 se třemi podsítěmi a bránou VPN. Pokud chcete nahradit hodnoty, je důležité vždycky pojmenovat podsíť brány konkrétně "GatewaySubnet". Pokud použijete jiný název, vytvoření brány se nezdaří.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 – Vytvoření připojení S2S VPN pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. vytvoření zásady IPsec/IKE

> [!IMPORTANT]
> Pokud chcete pro připojení povolit možnost UsePolicyBasedTrafficSelectors, musíte vytvořit zásadu IPsec/IKE.

Následující příklad vytvoří zásadu IPsec/IKE s těmito algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, životnost SA 14400 sekund & 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Vytvořte připojení S2S VPN s selektory přenosu na základě zásad a zásadami IPsec/IKE.
Vytvořte připojení S2S VPN a použijte zásady IPsec/IKE vytvořené v předchozím kroku. Uvědomte si další parametr-UsePolicyBasedTrafficSelectors $True, který umožňuje selektorům přenosu na základě zásad v připojení.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po dokončení postupu bude připojení S2S VPN používat definované zásady IPsec/IKE a pro připojení povolí selektory přenosu na základě zásad. Stejný postup můžete opakovat, pokud chcete přidat další připojení k dalším místním zařízením VPN založeným na zásadách ze stejné brány Azure VPN.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualizace selektorů přenosu na základě zásad pro připojení
V poslední části se dozvíte, jak aktualizovat možnost selektory přenosu na základě zásad pro existující připojení S2S VPN.

### <a name="1-get-the-connection"></a>1. získání připojení
Získání prostředku připojení

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. zaškrtněte možnost pro selektory provozu na základě zásad.
Následující řádek ukazuje, jestli se pro připojení používají selektory přenosu dat založené na zásadách:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Pokud řádek vrátí hodnotu "**true**", pak se v připojení nakonfigurují selektory přenosu na základě zásad. v opačném případě vrátí "**false**".

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Povolte nebo zakažte selektory provozu na základě zásad připojení.
Po získání prostředku připojení můžete tuto možnost povolit nebo zakázat.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Povolení UsePolicyBasedTrafficSelectors
Následující příklad povoluje možnost selektory přenosu na základě zásad, ale ponechá zásady IPsec/IKE beze změny:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Zakázání UsePolicyBasedTrafficSelectors
Následující příklad zakáže možnost selektory přenosu na základě zásad, ale ponechá zásady IPsec/IKE beze změny:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další informace o vlastních zásadách IPsec/IKE najdete v podrobnostech o [konfiguraci zásad IPSec/IKE pro připojení S2S VPN nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) .
