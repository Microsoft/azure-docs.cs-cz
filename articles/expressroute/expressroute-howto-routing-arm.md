---
title: 'Kurz: konfigurace partnerského vztahu pro okruh ExpressRoute-Azure PowerShell'
description: V tomto kurzu se dozvíte, jak vytvořit a spravovat konfiguraci směrování pro okruh ExpressRoute v modelu nasazení Správce prostředků pomocí PowerShellu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 641d7eeef96af84f0f058aebd19d795083e3567f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855340"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Kurz: vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí prostředí PowerShell

Tento kurz vám pomůže vytvořit a spravovat konfiguraci směrování pro okruh ExpressRoute v modelu nasazení Správce prostředků pomocí PowerShellu. Můžete také kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejné partnerské vztahy](about-public-peering.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), poskytovatel připojení bude konfigurovat a spravovat směrování za vás.

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP se poraďte se svým poskytovatelem služeb.
> 

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy a partnerské vztahy Microsoftu (veřejný partnerský vztah Azure se pro nové okruhy už nepoužívá). Partnerské vztahy se dají konfigurovat v libovolném pořadí, ve kterém si zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu věnovaném [doménám směrování ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerském vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Konfigurace, aktualizace a odstranění partnerského vztahu Microsoftu pro okruh
> - Konfigurace, aktualizace a odstranění privátního partnerského vztahu Azure pro okruh

## <a name="prerequisites"></a>Požadavky

* Než začnete s konfigurací, ujistěte se, že jste prošli následující stránky:
    * [Požadavky](expressroute-prerequisites.md) 
    * [Požadavky směrování](expressroute-routing.md)
    * [Pracovní postupy](expressroute-workflows.md)
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, postupujte podle pokynů pro [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a před pokračováním dodržujte okruh povolený vaším poskytovatelem připojení. Aby bylo možné spustit rutiny v tomto článku, musí být okruh ExpressRoute ve zřízeném a povoleném stavu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras. Další informace najdete v tématu [Konfigurace filtru tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Přihlaste se a vyberte své předplatné.

   Pokud jste prostředí PowerShell nainstalovali místně, přihlaste se. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

   ```azurepowershell
   Connect-AzAccount
   ```

   Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute.

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil partnerský vztah Microsoftu. Nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zajištěný a také povolený. Použijte následující příklad:

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
4. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Než budete pokračovat, ujistěte se, že máte k dispozici následující informace.

   * Podsíť/30 nebo/126 pro primární propojení. Blok adres musí být platná veřejná předpona IPv4 nebo IPv6, kterou vlastníte a zaregistrovali v RIR/IRR.
   * Podsíť/30 nebo/126 pro sekundární propojení. Blok adres musí být platná veřejná předpona IPv4 nebo IPv6, kterou vlastníte a zaregistrovali v RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: zadáte seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás. Relace protokolu BGP IPv4 vyžadují předpony IPv4 a IPv6 adresy protokolu IPv6 vyžadují inzerované předpony IPv6. 
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Volitelné:
     * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované pro partnerský vztah jako číslo, můžete zadat číslo AS, na které jsou registrované.
     * Hodnota hash MD5, pokud se ji rozhodnete použít.

> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud se automatické ověření nezdaří, zobrazí se ve výstupu příkazu Get-AzExpressRouteCircuitPeeringConfig "AdvertisedPublicPrefixesState" jako "vyžadováno ověření" (viz část "získání podrobností partnerského vztahu Microsoftu" v následující části). 
> 
> Pokud se zobrazí zpráva požadovaná kontrola, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace, která je uvedená jako vlastník předpony v registru směrování, a odešlete tyto dokumenty pro ruční ověření otevřením lístku podpory. 
> 

   Pro konfiguraci partnerského vztahu Microsoftu pro váš okruh použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Získání podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

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

## <a name="azure-private-peering"></a><a name="private"></a>Privátní partnerský vztah Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Naimportujte modul PowerShellu pro ExpressRoute.

   Nainstalujte nejnovější instalační program PowerShellu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/). Pak importujte moduly Azure Resource Manager do relace prostředí PowerShell, aby bylo možné začít používat rutiny ExpressRoute. Bude nutné spustit PowerShell jako správce.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importujte všechna rozhraní AZ. \* moduly v rozsahu známé sémantické verze.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Můžete také importovat modul SELECT v rámci známého rozsahu sémantické verze.

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

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil privátní partnerské vztahy Azure. Nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zajištěný a také povolený. Použijte následující příklad:

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
4. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Než budete pokračovat v dalších krocích, ujistěte se, že máte následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Ujistěte se, že nepoužíváte 65515.
   * Volitelné:
     * Hodnota hash MD5, pokud se ji rozhodnete použít.

   Pro konfiguraci privátního partnerského vztahu Azure pro váš okruh použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Získání podrobností o privátním partnerském vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat pomocí následujícího příkladu. V tomto příkladu je ID sítě VLAN okruhu Aktualizováno z 100 na 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Další kroky

Po dokončení konfigurace privátního partnerského vztahu Azure můžete vytvořit bránu ExpressRoute pro propojení virtuální sítě s okruhem. 

> [!div class="nextstepaction"]
> [Konfigurace brány virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
