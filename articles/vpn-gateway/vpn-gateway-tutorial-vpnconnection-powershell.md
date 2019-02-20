---
title: Vytváření a správa připojení VPN Azure typu Site-to-Site pomocí PowerShellu | Microsoft Docs
description: Kurz – Vytváření a správa připojení VPN typu Site-to-Site pomocí modulu Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: a9ca626ecf026736617ba495422ed957d03b2b37
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414596"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Kurz: Vytvoření a Správa připojení S2S VPN pomocí Powershellu

Připojení VPN Azure typu Site-to-Site poskytují zabezpečené propojení různých míst mezi zákazníkem a Azure. Tento kurz vás provede životními cykly připojení VPN typu Site-to-Site s protokolem IPsec, například vytvořením a správou připojení VPN typu Site-to-Site. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření připojení VPN typu Site-to-Site
> * Aktualizace vlastnosti připojení: předsdílený klíč, BGP, zásady IPsec/IKE
> * Přidání dalších připojení VPN
> * Odstranění připojení VPN

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující diagram ukazuje topologii pro tento kurz:

![Diagram připojení VPN typu Site-to-Site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="requirements"></a>Požadavky

Prvním kurzu: [Vytvoření brány VPN gateway pomocí Azure Powershellu](vpn-gateway-tutorial-create-gateway-powershell.md) vytvoříte následující prostředky:

1. Skupina prostředků (TestRG1), virtuální sítě (ze sítě VNet1) a podsítě GatewaySubnet
2. Brána VPN (VNet1GW)

Hodnoty parametrů virtuální sítě jsou uvedené níže. Všimněte si další hodnoty pro bránu místní sítě, které představují vaše místní sítě. Změňte podle vašeho prostředí a síťových nastavení, pak kopírování a vkládání k nastavení proměnných pro účely tohoto kurzu níže uvedené hodnoty. Pokud vyprší časový limit vaší relace Cloud Shellu, nebo budete muset použít jiné okno prostředí PowerShell, kopírování a vložení proměnné do nové relace a pokračujte kurz.

>[!NOTE]
> Pokud použijete toto připojení, nezapomeňte změnit hodnoty tak, aby odpovídaly vaší místní síti. Pokud používáte systém právě tyto kroky jako kurz, není nutné provádět změny, ale připojení nebude fungovat.
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

Vytvoření brány místní sítě s [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway?view=azurermps-6.8.1) příkazu.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Vytvoření připojení VPN typu Site-to-Site

Dále vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a zařízením VPN s [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1). Všimněte si, že hodnota -ConnectionType pro VPN typu Site-to-Site je *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Pokud používáte BGP a chcete pro připojení povolit BGP, přidejte volitelnou vlastnost **-EnableBGP $True**. Ve výchozím nastavení je zakázaná.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aktualizace předsdíleného klíče, BGP a zásad IPsec/IKE pro připojení VPN

### <a name="view-and-update-your-pre-shared-key"></a>Zobrazení a aktualizace předsdíleného klíče

Připojení VPN Azure typu Site-to-Site využívá předsdílený klíč (tajný klíč) k ověřování mezi místním zařízením VPN a bránou VPN Azure. Můžete zobrazit a aktualizovat předsdílený klíč pro připojení k [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1) a [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1).

> [!IMPORTANT]
> Předsdílený klíč je řetězec **tisknutelných znaků ASCII** o maximální délce 128 znaků.

Tento příkaz zobrazí předsdílený klíč pro připojení:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Výstup bude **Azure@!b2C3** jako ve výše uvedeném příkladu. Pomocí následujícího příkazu změňte hodnotu předsdíleného klíče na **Azure@!_b2=C3**:

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

Pokud jste nenakonfigurovali vlastnosti protokolu BGP, přidejte následující příkazy tyto vlastnosti k bráně VPN a bránu místní sítě: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway?view=azurermps-6.8.1) a [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway?view=azurermps-6.8.1).

Postup konfigurace vlastností protokolu BGP, použijte následující příklad:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Povolit protokol BGP s [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

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
* Protokol IPsec: Aes128, SHA1, PFS14, sekund 14 400 fakturovatelných životnost SA & 102,400,000 KB

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

Přidání dalšího připojení sítě VPN typu S2S ke stejné bráně VPN vytvořit jinou bránu místní sítě a vytvořit nové připojení mezi novou bránu místní sítě a brány VPN. Pomocí následujících příkladech nezapomeňte změnit proměnné tak, aby odrážely konfiguraci sítě.

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

Odstranit připojení k síti VPN S2S s [odebrat AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Pokud už ji nepotřebujete, odstraňte bránu místní sítě. Bránu místní sítě nejde odstranit, pokud jsou k ní přidružená další připojení.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tato konfigurace je součástí prototypu, testovací nebo testování konceptu nasazení, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, VPN gateway a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další postup

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
