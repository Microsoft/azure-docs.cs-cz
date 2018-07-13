---
title: Vytváření a správa připojení VPN Azure typu Site-to-Site pomocí PowerShellu | Microsoft Docs
description: Kurz – Vytváření a správa připojení VPN typu Site-to-Site pomocí modulu Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452460"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Vytváření a správa připojení VPN typu Site-to-Site pomocí modulu Azure PowerShell

Připojení VPN Azure typu Site-to-Site poskytují zabezpečené propojení různých míst mezi zákazníkem a Azure. Tento kurz vás provede životními cykly připojení VPN typu Site-to-Site s protokolem IPsec, například vytvořením a správou připojení VPN typu Site-to-Site. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření připojení VPN typu Site-to-Site
> * Aktualizace vlastnosti připojení: předsdílený klíč, BGP, zásady IPsec/IKE
> * Přidání dalších připojení VPN
> * Odstranění připojení VPN

Následující diagram ukazuje topologii pro tento kurz:

![Diagram připojení VPN typu Site-to-Site](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.3 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="requirements"></a>Požadavky

Dokončete první kurz [Vytvoření brány VPN pomocí Azure PowerShellu](vpn-gateway-tutorial-create-gateway-powershell.md) a vytvořte následující prostředky:

1. Skupina prostředků (TestRG1), virtuální síť (VNet1) a podsíť brány GatewaySubnet
2. Brána VPN (VNet1GW)

Hodnoty parametrů virtuální sítě jsou uvedené níže. Všimněte si dalších hodnot pro bránu místní sítě, které reprezentují vaši místní síť. Upravte hodnoty na základě vašeho prostředí a nastavení sítě.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

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

Vytvořte bránu místní sítě pomocí příkazu [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Vytvoření připojení VPN typu Site-to-Site

Dále vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a zařízením VPN pomocí příkazu [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Všimněte si, že hodnota -ConnectionType pro VPN typu Site-to-Site je *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Pokud používáte BGP a chcete pro připojení povolit BGP, přidejte volitelnou vlastnost **-EnableBGP $True**. Ve výchozím nastavení je zakázaná.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aktualizace předsdíleného klíče, BGP a zásad IPsec/IKE pro připojení VPN

### <a name="view-and-update-your-pre-shared-key"></a>Zobrazení a aktualizace předsdíleného klíče

Připojení VPN Azure typu Site-to-Site využívá předsdílený klíč (tajný klíč) k ověřování mezi místním zařízením VPN a bránou VPN Azure. Předsdílený klíč pro připojení můžete zobrazit a aktualizovat pomocí příkazů [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) a [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Předsdílený klíč je řetězec **tisknutelných znaků ASCII** o maximální délce 128 znaků.

Tento příkaz zobrazí předsdílený klíč pro připojení:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Výstup bude **Azure@!b2C3** jako ve výše uvedeném příkladu. Pomocí následujícího příkazu změňte hodnotu předsdíleného klíče na **Azure@!_b2=C3**:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Povolení BGP pro připojení VPN

Brána VPN Azure podporuje protokol dynamického směrování BGP. Pro jednotlivá připojení můžete BGP povolit v závislosti na tom, jestli používáte BGP v místních sítích a zařízeních. Před povolením BGP pro připojení zadejte následující vlastnosti BGP:

* Číslo autonomního systému (ASN) VPN Azure
* ASN místní brány místní sítě
* IP adresa partnera BGP místní brány místní sítě

Pokud jste nenakonfigurovali vlastnosti BGP, pomocí následujících příkazů přidejte tyto vlastnosti do vaší brány VPN a brány místní sítě: [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) a [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Povolte BGP pomocí příkazu [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

BGP můžete zakázat změnou hodnoty vlastnosti -EnableBGP na **$False**. Podrobnější popis BGP v branách VPN Azure najdete v tématu [BGP v branách VPN Azure](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Použití vlastních zásad IPsec/IKE pro připojení

Místo použití [výchozích návrhů](vpn-gateway-about-vpn-devices.md#ipsec) můžete použít volitelné zásady IPsec/IKE a zadat pro připojení přesnou kombinaci kryptografických algoritmů IPsec/IKE a síly klíče. Následující ukázkový skript vytvoří jinou zásadu IPsec/IKE s následujícími algoritmy a parametry:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, životnost SA 14 400 sekund a 102 400 000 kB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Úplný seznam algoritmů a související pokyny najdete v tématu [Zásady IPsec/IKE pro připojení typu Site-to-Site nebo VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md).

## <a name="add-another-s2s-vpn-connection"></a>Přidání dalšího připojení VPN typu Site-to-Site

Pokud chcete přidat další připojení VPN typu Site-to-Site ke stejné bráně VPN, vytvořte další bránu místní sítě a nové připojení mezi novou bránou místní sítě a bránou VPN. Postupujte podle příkladu v tomto článku.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

K vaší bráně VPN Azure teď existují dvě připojení VPN typu Site-to-Site.

![Připojení VPN mezi více lokalitami](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Odstranění připojení VPN typu Site-to-Site

Odstraňte připojení VPN typu Site-to-Site pomocí příkazu [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Pokud už ji nepotřebujete, odstraňte bránu místní sítě. Bránu místní sítě nejde odstranit, pokud jsou k ní přidružená další připojení.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
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
