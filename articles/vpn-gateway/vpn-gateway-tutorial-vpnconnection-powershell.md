---
title: 'Brána Azure VPN: Vytváření a správa připojení VPN S2S: Výuka'
description: Kurz – Vytváření a správa připojení VPN typu Site-to-Site pomocí modulu Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: c42d164647d608fc45e1135cd4111ff3900a733c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137156"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Kurz: Vytváření a správa připojení VPN S2S pomocí PowerShellu

Připojení VPN Azure typu Site-to-Site poskytují zabezpečené propojení různých míst mezi zákazníkem a Azure. Tento kurz vás provede životními cykly připojení VPN typu Site-to-Site s protokolem IPsec, například vytvořením a správou připojení VPN typu Site-to-Site. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření připojení VPN typu Site-to-Site
> * Aktualizace vlastnosti připojení: předsdílený klíč, BGP, zásady IPsec/IKE
> * Přidání dalších připojení VPN
> * Odstranění připojení VPN

Následující diagram ukazuje topologii pro tento kurz:

![Diagram připojení VPN typu Site-to-Site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Spolupráce s Azure Cloud Shell a Azure PowerShellem

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>Požadavky

Dokončete první kurz: [Vytvořte bránu VPN pomocí Azure PowerShellu](vpn-gateway-tutorial-create-gateway-powershell.md) a vytvořte tak následující prostředky:

1. Skupina prostředků (TestRG1), virtuální síť (VNet1) a GatewaySubnet
2. Brána VPN (VNet1GW)

Hodnoty parametrů virtuální sítě jsou uvedené níže. Poznamenejte si další hodnoty pro bránu místní sítě, které představují místní síť. Změňte níže uvedené hodnoty na základě nastavení prostředí a sítě a potom zkopírujte a vložte a nastavte proměnné pro tento kurz. Pokud vám časový čas relace prostředí Cloud nebo potřebujete použít jiné okno PowerShellu, zkopírujte a vložte proměnné do nové relace a pokračujte v kurzu.

>[!NOTE]
> Pokud používáte toto připojení, nezapomeňte změnit hodnoty tak, aby odpovídaly vaší místní síti. Pokud právě spouštěte tyto kroky jako kurz, nemusíte provádět změny, ale připojení nebude fungovat.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Pracovní postup vytvoření připojení VPN typu Site-to-Site je jednoduchý:

1. Vytvoření brány místní sítě reprezentující místní síť
2. Vytvoření propojení mezi bránou VPN Azure a bránou místní sítě

## <a name="create-a-local-network-gateway"></a>Vytvoření brány místní sítě

Brána místní sítě reprezentuje vaši místní síť. V bráně místní sítě můžete zadat vlastnosti vaší místní sítě, včetně následujících:

* Veřejná IP adresa vašeho zařízení VPN
* Adresní prostor místního prostředí
* (Volitelné) Atributy BGP (IP adresa a číslo AS partnera BGP)

Vytvořte bránu místní sítě pomocí příkazu [New-AzLocalNetworkGateway.](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway)

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Vytvoření připojení VPN typu Site-to-Site

Dále vytvořte připojení VPN site-to-site mezi bránou virtuální sítě a zařízením VPN pomocí [nového připojení AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Všimněte si, že hodnota -ConnectionType pro VPN typu Site-to-Site je *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

Pokud používáte BGP a chcete pro připojení povolit BGP, přidejte volitelnou vlastnost **-EnableBGP $True**. Ve výchozím nastavení je zakázaná. Parametr '-ConnectionProtocol' je volitelný s IKEv2 jako výchozí. Připojení k protokolům IKEv1 můžete vytvořit zadáním **protokolu -ConnectionProtocol IKEv1**.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aktualizace předsdíleného klíče, BGP a zásad IPsec/IKE pro připojení VPN

### <a name="view-and-update-your-pre-shared-key"></a>Zobrazení a aktualizace předsdíleného klíče

Připojení VPN Azure typu Site-to-Site využívá předsdílený klíč (tajný klíč) k ověřování mezi místním zařízením VPN a bránou VPN Azure. Předsdílený klíč můžete zobrazit a aktualizovat pro připojení pomocí [služby Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) a [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Předsdílený klíč je řetězec **tisknutelných znaků ASCII** o maximální délce 128 znaků.

Tento příkaz zobrazí předsdílený klíč pro připojení:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Výstup bude "**\@Azure !b2C3**" podle výše uvedeného příkladu. Pomocí následujícího příkazu můžete změnit hodnotu předsdíleného klíče na "**Azure\@!_b2=C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Povolení BGP pro připojení VPN

Brána VPN Azure podporuje protokol dynamického směrování BGP. Pro jednotlivá připojení můžete BGP povolit v závislosti na tom, jestli používáte BGP v místních sítích a zařízeních. Před povolením BGP pro připojení zadejte následující vlastnosti BGP:

* Číslo autonomního systému (ASN) VPN Azure
* ASN místní brány místní sítě
* IP adresa partnera BGP místní brány místní sítě

Pokud jste vlastnosti protokolu BGP nenakonfigurovali, přidejte tyto vlastnosti do brány VPN a brány místní sítě následující příkazy: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) a [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

Ke konfiguraci vlastností protokolu BGP použijte následující příklad:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Povolte protokol BGP pomocí [protokolu Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP můžete zakázat změnou hodnoty vlastnosti -EnableBGP na **$False**. Podrobnější popis BGP v branách VPN Azure najdete v tématu [BGP v branách VPN Azure](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Použití vlastních zásad IPsec/IKE pro připojení

Místo použití [výchozích návrhů](vpn-gateway-about-vpn-devices.md#ipsec) můžete použít volitelné zásady IPsec/IKE a zadat pro připojení přesnou kombinaci kryptografických algoritmů IPsec/IKE a síly klíče. Následující ukázkový skript vytvoří jinou zásadu IPsec/IKE s následujícími algoritmy a parametry:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, životnost SA 14 400 sekund a 102 400 000 kB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Úplný seznam algoritmů a související pokyny najdete v tématu [Zásady IPsec/IKE pro připojení typu Site-to-Site nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Přidání dalšího připojení VPN typu Site-to-Site

Přidejte další připojení S2S VPN ke stejné bráně VPN, vytvořte jinou bránu místní sítě a vytvořte nové připojení mezi novou bránou místní sítě a bránou VPN. Použijte následující příklady a nezapomeňte upravit proměnné tak, aby odrážely vlastní konfiguraci sítě.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

K vaší bráně VPN Azure teď existují dvě připojení VPN typu Site-to-Site.

![Připojení VPN mezi více lokalitami](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Odstranění připojení VPN typu Site-to-Site

Odstraňte připojení S2S VPN pomocí [programu Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Pokud už ji nepotřebujete, odstraňte bránu místní sítě. Bránu místní sítě nejde odstranit, pokud jsou k ní přidružená další připojení.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je tato konfigurace součástí prototypu, testu nebo testování konceptu nasazení, můžete pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků, bránu VPN a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s vytvářením a správou připojení VPN typu Site-to-Site a s následujícími postupy:

> [!div class="checklist"]
> * Vytvoření připojení VPN typu Site-to-Site
> * Aktualizace vlastnosti připojení: předsdílený klíč, BGP, zásady IPsec/IKE
> * Přidání dalších připojení VPN
> * Odstranění připojení VPN

V následujících kurzech se seznámíte s připojeními typu Site-to-Site, VNet-to-VNet a Point-to-Site.

> [!div class="nextstepaction"]
> * [Vytváření připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Vytváření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
