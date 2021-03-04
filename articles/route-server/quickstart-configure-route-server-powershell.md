---
title: 'Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure PowerShell'
description: V tomto rychlém startu se dozvíte, jak vytvořit a nakonfigurovat směrovací server pomocí Azure PowerShell.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 5b40cfcde7aa1771c8a4b9025d35b2dc0c728676
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039780"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure PowerShell

Tento článek vám pomůže nakonfigurovat partnerský server Azure tak, aby se ve vaší virtuální síti používal virtuální síťové zařízení (síťové virtuální zařízení), a to pomocí PowerShellu. Azure Route Server se dozví o trasách z síťové virtuální zařízení a jejich programování na virtuálních počítačích ve virtuální síti. Směrovací server Azure taky inzeruje trasy virtuální sítě do síťové virtuální zařízení. Další informace najdete v tématu [Azure Route Server](overview.md).

> [!IMPORTANT]
> Služba Azure Route Server (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ujistěte se, že máte nejnovější moduly PowerShellu, nebo můžete na portálu použít Azure Cloud Shell.
* Zkontrolujte [omezení služby pro Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Vytvoření směrovacího serveru

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

Než budete moct vytvořit směrovací server Azure, budete k hostování nasazení potřebovat virtuální síť. Pomocí příkazu níže vytvořte skupinu prostředků a virtuální síť. Pokud již máte virtuální síť, můžete přejít k další části.

```azurepowershell-interactive
New-AzResourceGroup –Name “RouteServerRG” -Location “West US”
New-AzVirtualNetwork –ResourceGroupName “RouteServerRG -Location “West US” -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidání podsítě

1. Přidejte podsíť s názvem *RouteServerSubnet* , do které se nasadí Server směrování Azure. Tato podsíť je vyhrazená podsíť jenom pro server Azure Route. RouteServerSubnet musí být/27 nebo kratší předpona (například/26,/25) nebo se vám při přidávání serveru tras Azure zobrazí chybová zpráva.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “myVirtualNetwork” - ResourceGroupName “RouteServerRG”
    Add-AzVirtualNetworkSubnetConfig –Name “RouteServerSubnet” -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Získejte ID RouteServerSubnet. Pokud chcete zobrazit ID prostředku všech podsítí ve virtuální síti, použijte tento příkaz:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name “vnet_name” -ResourceGroupName “
    $vnet.Subnets
    ```

ID RouteServerSubnet vypadá takto:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Vytvoření směrovacího serveru

Vytvoření směrovacího serveru pomocí tohoto příkazu:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US” -HostedSubnet “RouteServerSubnet_ID”
```

Umístění musí odpovídat umístění vaší virtuální sítě. HostedSubnet je ID RouteServerSubnet, které jste získali v předchozí části.

## <a name="create-peering-with-an-nva"></a>Vytvoření partnerského vztahu s síťové virtuální zařízení

K navázání partnerského vztahu protokolu BGP ze serveru směrování do síťové virtuální zařízení použijte následující příkaz:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" je IP adresa virtuální sítě přiřazená k síťové virtuální zařízení. "nva_asn" je číslo autonomního systému (ASN), které je konfigurováno v síťové virtuální zařízení. ASN může být jakékoli 16bitové číslo jiné než v rozsahu 65515-65520. Tato škála čísla ASN je vyhrazena společností Microsoft.

K nastavení partnerského vztahu s různými síťové virtuální zařízení nebo jinou instancí stejného síťové virtuální zařízení pro redundanci použijte tento příkaz:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Dokončete konfiguraci na síťové virtuální zařízení

K dokončení konfigurace síťové virtuální zařízení a povolení relací protokolu BGP budete potřebovat IP adresu a ASN serveru tras Azure. Tyto informace můžete získat pomocí tohoto příkazu:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Výstup obsahuje následující informace:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurace výměny trasy

Pokud máte bránu ExpressRoute a bránu Azure VPN ve stejné virtuální síti a chcete, aby vyměňovat trasy, můžete na serveru tras Azure povolit směrování na trase.

1. Pokud chcete povolit výměnu tras mezi serverem tras Azure a branami, použijte tento příkaz:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Pokud chcete zakázat výměnu tras mezi serverem tras Azure a branami, použijte tento příkaz:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Řešení potíží

Pomocí tohoto příkazu můžete zobrazit trasy inzerované a přijímané serverem Azure Route:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up"></a>Vyčištění

Pokud už nepotřebujete Server tras Azure, pomocí těchto příkazů odeberte vytvoření partnerského vztahu protokolu BGP a pak odeberte server směrování. 

1. Pomocí tohoto příkazu odeberte vytvoření partnerského vztahu protokolu BGP mezi serverem Azure Route Server a síťové virtuální zařízení:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName “nva_name” -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Odeberte server služby Azure Route pomocí tohoto příkazu:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Další kroky

Po vytvoření serveru Směrování Azure si přečtěte informace o tom, jak server tras Azure komunikuje s ExpressRoute a branami VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute a podpora Azure VPN](expressroute-vpn-support.md)
