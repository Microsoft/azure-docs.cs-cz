---
title: 'Připojení Azure VPN Gateway k několika místním na základě zásad zařízení VPN: Azure Resource Manageru: PowerShell | Dokumentace Microsoftu'
description: Konfigurace služby Azure založené na směrování VPN gateway k několika založené na zásadách zařízením VPN pomocí Azure Resource Manageru a Powershellu.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 46555bf121e674b82c0c7dd39f74ee3708fc4439
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850633"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Připojení Azure VPN Gateway k několika místním založené na zásadách zařízením VPN pomocí Powershellu

Tento článek vám pomůže nakonfigurovat služby Azure založené na směrování VPN gateway pro připojení k několika místním založené na zásadách zařízením VPN využití vlastních zásad IPsec/IKE na připojení S2S VPN.

## <a name="about"></a>O zásadové a trasové brány VPN

Zásady – *vs.* zařízení sítě VPN založené na směrování se liší v nastavení protokolu IPsec selektory provozu na připojení:

* **Na základě zásad** zařízení VPN použijte k definování, jak provoz je do tunelových propojení IPsec šifrované/dešifrovat kombinace předpon z obou sítích. Obvykle je založený na zařízení brány firewall, které provádějí filtrování paketů. Šifrování tunel IPsec a dešifrování se přidají do paketu filtrování a modul pro zpracování.
* **Založené na trasách** zařízení VPN použijte selektory provozu any-to-any (zástupný znak) a umožní směrování a předávání tabulky směrovat přenos dat do různých tunelových propojení IPsec. Obvykle orchard je založen na platformách směrovače, kde každý tunelu IPsec je modelovaná jako síťové rozhraní nebo VTI (tunel virtuálního rozhraní).

Následující diagramy zvýrazněte dva modely:

### <a name="policy-based-vpn-example"></a>Příklad připojení VPN na základě zásad
![na základě zásad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Příklad sítě VPN založené na trasách
![založené na trasách](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Podpora Azure pro síť VPN založená na zásadách
V současné době podporuje oba režimy bran VPN Azure: trasovými bránami VPN a bránami VPN na základě zásad. Tyto šablony jsou sestaveny na různých platformách interní, vedlo různé požadavky:

|                          | **Brány sítě VPN PolicyBased** | **Brány VPN typu RouteBased**               |
| ---                      | ---                         | ---                                      |
| **Azure skladové položky brány**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Verze IKE**          | IKEv1                       | IKEv2                                    |
| **Max. Připojení S2S** | **1**                       | Basic nebo Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Pomocí vlastních zásad IPsec/IKE, teď můžete nakonfigurovat Azure založené na směrování VPN Gateway používat selektory provozu na základě předpony s možností "**PolicyBasedTrafficSelectors**", které umožňuje připojení k místním zařízením VPN na základě zásad. Díky této funkci můžete připojení z virtuální sítě Azure a bránu VPN k několika místních na základě zásad zařízení VPN nebo brána firewall odebrání limitu jednoho připojení z aktuální Azure na základě zásad VPN Gateway.

> [!IMPORTANT]
> 1. Pokud chcete povolit tyto možnosti připojení, musí podporovat místní zařízení VPN na základě zásad **IKEv2** k připojení ke službám Azure VPN Gateway založená na trasách. Zkontrolujte vaše specifikace zařízení VPN.
> 2. Sítích na pracovišti připojení prostřednictvím zařízení VPN založené na zásadách se tento mechanismus lze připojit pouze k virtuální síti Azure; **nelze přenosu do jiných sítích na pracovišti nebo virtuálních sítí prostřednictvím stejné brány Azure VPN**.
> 3. Možnost konfigurace je součástí vlastní zásady IPsec/IKE připojení. Pokud povolíte možnost selektor provozu na základě zásad, musíte zadat úplnou zásad (algoritmy šifrování a integrita protokolu IPsec/IKE, síly klíče a doby životnosti přidružení zabezpečení).

Následující diagram ukazuje, proč při směrování prostřednictvím brány Azure VPN nefunguje s možností na základě zásad:

![na základě zásad přenosu](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak je znázorněno v diagramu, brána Azure VPN se selektory provozu z virtuální sítě ke každé z předpony místní sítě, ale ne předpony křížové připojení. Například v místním serveru 2, web 3 a 4 webu může každý komunikovat do sítě VNet1 v uvedeném pořadí, ale nemůže připojit přes bránu Azure VPN mezi sebou. Diagram znázorňuje cross-connect selektory provozu, které nejsou k dispozici ve službě Azure VPN gateway v rámci této konfigurace.

## <a name="configurepolicybased"></a>Konfigurace selektory provozu na základě zásad připojení

Pokyny v tomto článku použijte stejný příklad, jak je popsáno v [zásady Konfigurace protokolu IPsec/IKE pro připojení typu S2S nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) k navázání připojení S2S VPN. To je ukázáno v následujícím diagramu:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Pracovní postup pro povolení tato připojení:
1. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě pro připojení mezi různými místy
2. Vytvoření zásady IPsec/IKE
3. Když vytvoříte připojení typu S2S nebo VNet-to-VNet, použijte zásady a **povolit selektory provozu na základě zásad** připojení
4. Pokud se připojení vytvoří, můžete použít nebo aktualizovat zásady na existující připojení

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Povolit selektory provozu na základě zásad připojení

Ujistěte se, že jste dokončili [část 3 z článku o zásadách konfigurace protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) pro tento oddíl. Následující příklad používá stejné parametry a kroky:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Připojení k vašemu předplatnému a deklarace proměnných

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps login.md)]

Deklarujte proměnné. Pro toto cvičení můžeme použít následující proměnné:

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

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Vytvoření virtuální sítě, brána sítě VPN a bránu místní sítě

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

Použijte následující příklad k vytvoření virtuální sítě TestVNet1 s tři podsítě a bránu VPN. Pokud chcete nahradit hodnoty, je důležité vždy pojmenovat podsítě brány speciálně "GatewaySubnet". Pokud použijete jiný název, vytvoření brány se nezdaří.

```azurepowershell-interactive
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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2: vytvoření připojení S2S VPN pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Vytvoření zásady IPsec/IKE

> [!IMPORTANT]
> Je potřeba vytvořit zásady IPsec/IKE, chcete-li povolit možnost "UsePolicyBasedTrafficSelectors" připojení.

Následující příklad vytvoří zásady IPsec/IKE pomocí těchto algoritmů a parametrů:
* IKEv2: AES256, SHA384, DHGroup24
* Protokol IPsec: AES256, SHA256, PFS24, doba života přidružení zabezpečení. 3600 sekund & 2048KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Vytvořte připojení S2S VPN selektory provozu na základě zásad a zásad IPsec/IKE
Vytvoření připojení S2S VPN a použití zásady IPsec/IKE vytvořili v předchozím kroku. Mějte na další parametr "-UsePolicyBasedTrafficSelectors $True" umožňující selektory provozu na základě zásad připojení.

```azurepowershell-interactive
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Po dokončení kroků, připojení S2S VPN používat zásady IPsec/IKE definována a povolit selektory provozu na základě zásad připojení. Stejný postup k přidání další připojení k další místní zařízení VPN na základě zásad ze stejné Azure VPN gateway můžete opakovat.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualizovat selektory provozu na základě zásad pro připojení
Poslední části se dozvíte, jak aktualizovat možnost selektory provozu na základě zásad pro existující připojení S2S VPN.

### <a name="1-get-the-connection"></a>1. Získat připojení
Získáte prostředek připojení.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Zaškrtněte možnost selektory provozu na základě zásad
Následující řádek určuje, zda selektory provozu na základě zásad se používá pro připojení:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Pokud vrátí řádek "**True**", pak selektory provozu na základě zásad jsou nakonfigurovány pro připojení; jinak vrátí "**False**."

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Povolí nebo zakáže selektory provozu na základě zásad připojení
Jakmile získáte prostředek připojení, můžete povolit nebo zakázat možnost.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Chcete-li povolit UsePolicyBasedTrafficSelectors
Následující příklad povolí možnost selektory provozu na základě zásad, ale ponechá zásady IPsec/IKE beze změny:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Chcete-li zakázat UsePolicyBasedTrafficSelectors
Následující příklad zakazuje možnost selektory provozu na základě zásad, ale ponechá beze změny zásad IPsec/IKE:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Další postup
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Projděte si také [zásady Konfigurace protokolu IPsec/IKE pro připojení S2S VPN nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md) podrobné informace o vlastních zásad IPsec/IKE.
