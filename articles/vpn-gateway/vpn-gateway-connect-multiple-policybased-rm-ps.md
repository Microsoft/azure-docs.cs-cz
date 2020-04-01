---
title: 'Brána Azure VPN: Připojení bran k více místním zařízením VPN založeným na zásadách'
description: Pomocí Azure Resource Manager a PowerShellu nakonfigurujte bránu VPN založenou na směrování Azure do více zařízení VPN založených na zásadách.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123320"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Připojení bran Azure VPN k více místním zařízením VPN založeným na zásadách pomocí PowerShellu

Tento článek vám pomůže nakonfigurovat bránu VPN založenou na trase Azure pro připojení k více místním zařízením VPN založeným na zásadách využívajících vlastní zásady IPsec/IKE u připojení VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>O branách VPN založených na zásadách a na směrování

Zařízení VPN založená *na zásadách a trasách* se liší v nastavení selektorů přenosů IPsec pro připojení:

* **Na základě zásad** Zařízení VPN používají kombinace předpon z obou sítí k definování způsobu šifrování/dešifrování provozu prostřednictvím tunelových propojení IPsec. Obvykle je postaven na zařízeních brány firewall, která provádějí filtrování paketů. Šifrování a dešifrování tunelů IPsec se přidává do modulu filtrování a zpracování paketů.
* **Na základě trasy** Zařízení VPN používají voliče přenosů libovolnék a libovolnými (zástupnými symboly) a umožňují směrování/předávání tabulek směrovat provoz do různých tunelů IPsec. Obvykle je postaven na platformách směrovačů, kde je každý tunel IPsec modelován jako síťové rozhraní nebo VTI (virtuální tunelové rozhraní).

Následující diagramy zvýrazňují dva modely:

### <a name="policy-based-vpn-example"></a>Příklad sítě VPN založené na zásadách
![založené na zásadách](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Příklad VPN založené na trase
![na základě trasy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Podpora Azure pro síť VPN založenou na zásadách
V současné době Azure podporuje oba režimy bran VPN: brány VPN založené na trasách a brány VPN založené na zásadách. Jsou postaveny na různých vnitřních platformách, které vedou k různým specifikacím:

|                          | **Brána VPN založená na zásadách** | **Brána VPN založené na routebased**       |**Brána VPN založené na routebased**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **SKU brány Azure**    | Základní                       | Základní                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Verze IKE**          | IKEv1                       | IKEv2                            | IKEv1 a IKEv2                         |
| **Max. Připojení S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Pomocí vlastních zásad IPsec/IKE teď můžete nakonfigurovat brány VPN založené na směrování Azure tak, aby používaly voliče provozu založené na předponami s možností **" PolicyBasedTrafficSelectors**", pro připojení k místním zařízením VPN založeným na zásadách. Tato funkce umožňuje připojení z virtuální sítě Azure a brány VPN k více místním zařízením VPN/firewall založeným na zásadách a odebrat limit jediného připojení z aktuálních bran VPN založených na zásadách Azure.

> [!IMPORTANT]
> 1. Chcete-li povolit toto připojení, musí vaše místní zařízení VPN založená na zásadách podporovat **IKEv2** pro připojení k bráně VPN založených na trase Azure. Zkontrolujte specifikace zařízení VPN.
> 2. Místní sítě, které se připojují prostřednictvím zařízení VPN založených na zásadách pomocí tohoto mechanismu, se můžou připojit jenom k virtuální síti Azure. **nemohou přejíždět do jiných místních nebo virtuálních sítí prostřednictvím stejné brány Azure VPN**.
> 3. Možnost konfigurace je součástí vlastní zásady připojení IPsec/IKE. Pokud povolíte možnost voliče provozu na základě zásad, je nutné zadat úplnou zásadu (šifrování IPsec/IKE a algoritmy integrity, silné stránky klíče a životnosti sa).

Následující diagram znázorňuje, proč tranzitní směrování přes bránu Azure VPN nefunguje s možností založenou na zásadách:

![tranzit založený na zásadách](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Jak je znázorněno v diagramu, brána Azure VPN má voliči provozu z virtuální sítě do každé předpony místní sítě, ale ne předpony křížového připojení. Například místní lokalita 2, lokalita 3 a lokalita 4 mohou komunikovat s virtuální sítí 1, ale nemohou se vzájemně připojit prostřednictvím brány Azure VPN. Diagram znázorňuje voliči přenosů mezi připojeními, které nejsou k dispozici v bráně Azure VPN v rámci této konfigurace.

## <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

Pokyny v tomto článku postupujte podle stejného příkladu, jak je popsáno v [principu Konfigurace protokolu IPsec/IKE pro připojení S2S nebo Virtuální sítě k virtuální síti](vpn-gateway-ipsecikepolicy-rm-powershell.md) k navázání připojení VPN S2S. To je znázorněno v následujícím diagramu:

![s2s-politika](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Pracovní postup umožňující toto připojení:
1. Vytvořte virtuální síť, bránu VPN a bránu místní sítě pro připojení mezi místními prostory.
2. Vytvořte zásadu IPsec/IKE.
3. Použijte zásadu při vytváření připojení S2S nebo Virtuální sítě k virtuální síti a **povolte voliči provozu na základě zásad** v připojení.
4. Pokud je připojení již vytvořeno, můžete zásadu použít nebo aktualizovat na existující připojení.

## <a name="before-you-begin"></a>Než začnete

* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Povolení selektorů provozu založených na zásadách

V této části se zobrazí, jak povolit voliče provozu na základě zásad v připojení. Ujistěte se, že jste dokončili [část 3 článku zásad Konfigurace protokolu IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). Kroky v tomto článku používají stejné parametry.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – Vytvoření virtuální sítě, brány VPN a brány místní sítě

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Připojte se k předplatnému a deklarujte své proměnné

1. Pokud v počítači používáte prostředí PowerShell místně, přihlaste se pomocí rutiny *Connect-AzAccount.* Nebo místo toho použijte Azure Cloud Shell ve vašem prohlížeči.

2. Deklarujte proměnné. Pro toto cvičení používáme následující proměnné:

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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Vytvoření virtuální sítě, brány VPN a brány místní sítě

1. Vytvořte skupinu prostředků.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Následující příklad použijte k vytvoření virtuální sítě TestVNet1 se třemi podsítěmi a brány VPN. Pokud chcete nahradit hodnoty, je důležité vždy pojmenovat podsíť brány konkrétně "GatewaySubnet". Pokud použijete jiný název, vytvoření brány se nezdaří.

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

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 – Vytvoření připojení VPN S2S pomocí zásad protokolu IPsec/IKE

1. Vytvořte zásadu IPsec/IKE.

   > [!IMPORTANT]
   > Chcete-li povolit možnost UsePolicyBasedTrafficSelectors v připojení, je třeba vytvořit zásadu IPsec/IKE.

   Následující příklad vytvoří zásadu Protokolu IPsec/IKE s těmito algoritmy a parametry:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS Žádný, SA Životnost 14400 sekund & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Vytvořte připojení S2S VPN pomocí selektorů provozu založených na zásadách a zásad y IPsec/IKE a použijte zásadu IPsec/IKE vytvořenou v předchozím kroku. Uvědomte si další parametr "-UsePolicyBasedTrafficSelectors $True", který umožňuje voliči provozu na základě zásad na připojení.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Po dokončení kroků bude připojení S2S VPN používat definované zásady IPsec/IKE a povolí voliči provozu na základě zásad v připojení. Můžete zopakovat stejné kroky a přidat další připojení k dalším místním zařízením VPN založeným na zásadách ze stejné brány Azure VPN.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Aktualizace selektorů provozu založených na zásadách
V této části se zobrazí, jak aktualizovat možnost selektorů provozu na základě zásad pro existující připojení S2S VPN.

1. Získejte prostředek připojení.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Zobrazení možnosti selektorů provozu na základě zásad.
Následující řádek ukazuje, zda jsou pro připojení použity voliče provozu založené na zásadách:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Pokud řádek vrátí "**True**", pak jsou pro připojení konfigurovány voliči provozu na základě zásad; jinak vrátí **"False".**
1. Jakmile získáte prostředek připojení, můžete povolit nebo zakázat voliči provozu na základě zásad na připojení.

   - Chcete-li povolit

      Následující příklad umožňuje možnost selektory provozu založené na zásadách, ale ponechá zásady IPsec/IKE beze změny:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Chcete-li zakázat

      Následující příklad zakáže možnost selektorů provozu na základě zásad, ale ponechá zásadu IPsec/IKE beze změny:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Další podrobnosti o vlastních zásadách Protokolu [IPsec/IKE pro připojení S2S VPN nebo Virtuální sítě](vpn-gateway-ipsecikepolicy-rm-powershell.md) na virtuální síť najděte také k dispozici.
