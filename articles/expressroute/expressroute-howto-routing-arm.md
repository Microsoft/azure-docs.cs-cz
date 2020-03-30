---
title: 'Azure ExpressRoute: Konfigurace partnerského vztahu: PowerShell'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 2c28df35eec862afb5b0078ca7693898e9b58533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264840"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí prostředí PowerShell

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování pro okruh ExpressRoute v modelu nasazení Správce prostředků pomocí prostředí PowerShell. Můžete také zkontrolovat stav, aktualizovat nebo odstranit a zrušit poskytování partnerských vztahu pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejný partnerský vztah](about-public-peering.md)
> * [Video - Soukromý partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 


Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských stran Protokolu BGP se obraťte na svého poskytovatele služeb.
> 
> 

Můžete nakonfigurovat privátní partnerský vztah a partnerský vztah Microsoftu pro okruh ExpressRoute (veřejný partnerský vztah Azure se nezastaral pro nové okruhy). Partnerských stran lze nakonfigurovat v libovolném pořadí, které zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrovacích doménách a partnerských serverech naleznete v tématu [Směrovací domény ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerského vztahu naleznete v tématu [ExpressRoute public peering](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být v zřízeném a povoleném stavu, abyste mohli spouštět rutiny v tomto článku.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před 1. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu. Další informace naleznete [v tématu Konfigurace filtru postupu pro partnerský vztah společnosti Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Přihlaste se a vyberte předplatné.

   Pokud jste powershell nainstalovali místně, přihlaste se. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

   ```azurepowershell
   Connect-AzAccount
   ```

   Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute.

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. f váš poskytovatel připojení nabízí spravované služby Vrstva 3, můžete požádat svého poskytovatele připojení, aby za vás povolil partnerský vztah Microsoftu. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena a také povolena. Použijte následující příklad:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

   * Podsíť /30 nebo /126 pro primární propojení. Musí se jednat o platnou veřejnou předponu IPv4 nebo IPv6, kterou vlastníte a která je registrována v RIR / IRR.
   * Podsíť /30 nebo /126 pro sekundární propojení. Musí se jednat o platnou veřejnou předponu IPv4 nebo IPv6, kterou vlastníte a která je registrována v RIR / IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás. Relace Protokolu BGP protokolu IPv4 vyžadují inzerované předpony Protokolu IPv4 a relace protokolu BGP protokolu IPv6 vyžadují inzerované předpony Protokolu IPv6. 
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Volitelné:
     * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované.
     * Hodnota hash MD5, pokud se ji rozhodnete použít.

> [!IMPORTANT]
> Společnost Microsoft ověří, zda jsou v registru směrování v Internetu přiřazeny zadané předpony inzerované veřejné a peer asn (nebo "ASN zákazníka". Pokud získáváte veřejné předpony z jiné entity a přiřazení není zaznamenáno v registru směrování, automatické ověření nebude dokončeno a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se příkaz AdvertisedPublicPrefixesState jako "Ověření potřebné" na výstupu příkazu "Get-AzExpressRouteCircuitPeeringConfig" (viz "Chcete-li získat podrobnosti partnerského vztahu Microsoftu" níže). 
> 
> Pokud se zobrazí zpráva "Ověření je potřeba", shromážděte dokumenty, které ukazují, že veřejné předpony jsou přiřazeny vaší organizaci entitou, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření otevření lístku podpory, jak je uvedeno níže. 
> 
>

   Ke konfiguraci partnerského vztahu Microsoftu pro okruh použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Získání podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete získat v následujícím příkladu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Libovolnou část konfigurace můžete aktualizovat pomocí následujícího příkladu:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Soukromý partnerský vztah Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Naimportujte modul PowerShellu pro ExpressRoute.

   Abyste mohli začít používat rutiny pro ExpressRoute, musíte nainstalovat nejnovější verzi instalačního programu PowerShellu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/) a naimportovat moduly Azure Resource Manageru do relace PowerShellu. Musíte spustit PowerShell jako správce.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importujte všechny moduly\* Az. v rozsahu známých sémantických verzí.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Můžete také pouze importovat vybraný modul v rámci známého rozsahu sémantických verzí.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Přihlaste se ke svému účtu.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute.

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. Pokud váš poskytovatel připojení nabízí spravované služby Vrstvy 3, můžete požádat svého poskytovatele připojení o povolení privátního partnerského vztahu Azure. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud však váš poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena a také povolena. Použijte následující příklad:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru vyhrazeného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
   * Volitelné:
     * Hodnota hash MD5, pokud se ji rozhodnete použít.

   Pomocí následujícího příkladu nakonfigurujte privátní partnerský vztah Azure pro váš okruh:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Pokud se rozhodnete použít hash MD5, použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Získání podrobností soukromého partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat v následujícím příkladu. V tomto příkladu je ID sítě VLAN okruhu aktualizováno ze 100 na 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


## <a name="next-steps"></a>Další kroky

Dalším krokem je [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
