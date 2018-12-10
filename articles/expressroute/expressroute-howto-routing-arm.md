---
title: 'Konfigurace partnerského vztahu pro okruh ExpressRoute -: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: b8e9aadc63af563c47d42ed52445afbc270d98ec
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141534"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí prostředí PowerShell

Tento článek pomůže při vytváření a správě konfigurace směrování pro okruh ExpressRoute v modelu nasazení Resource Manageru pomocí prostředí PowerShell. Můžete také zkontrolovat stav, update nebo delete a zrušit jejich zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejné partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 


Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), se svého poskytovatele připojení, konfiguraci a správu směrování za vás.

> [!IMPORTANT]
> Aktuálně prostřednictvím portálu pro správu služeb neinzerujeme partnerské vztahy nakonfigurované poskytovateli služeb. Pracujeme na tom, aby tato možnost brzy fungovala. Před konfigurací partnerských vztahů protokolu BGP, obraťte se na svého poskytovatele služeb.
> 
> 

Můžete nakonfigurovat jeden, dva nebo všechny tři partnerské vztahy (soukromý Azure, veřejný Azure a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrování domény a vztahy, naleznete v tématu [domény směrování ExpressRoute](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený pro, abyste mohli spouštět rutiny v tomto článku.

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Partnerský vztah Microsoftu

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu. Další informace najdete v tématu [Konfigurovat filtr tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Přihlaste se a vyberte své předplatné.

  Pokud jste nainstalovali PowerShell místně, přihlaste se. Pokud používáte Azure Cloud Shell, můžete tento krok přeskočit.

  ```azurepowershell
  Connect-AzureRmAccount
  ```

  Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

  ```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute.

  Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. f poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení partnerského vztahu Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

  * / 30 nebo/126 podsíť pro primární propojení. Musí se jednat o platné veřejné IPv4 nebo IPv6 předponu vlastníte a registrované u RIR / IRR.
  * / 30 nebo/126 podsítě pro sekundární propojení. Musí se jednat o platné veřejné IPv4 nebo IPv6 předponu vlastníte a registrované u RIR / IRR.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás. Relace protokolu BGP IPv4 vyžadovat že IPv4 inzerované předpony a relace protokolu BGP IPv6 vyžadující, že inzerované předpony protokolu IPv6. 
  * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
  * Volitelné:
    * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované.
    * Hodnota hash MD5, pokud se ji rozhodnete použít.

  Konfigurace partnerského vztahu Microsoftu pro váš okruh, použijte následující příklad:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

### <a name="getmsft"></a>K získání podrobností partnerského vztahu Microsoftu

Můžete získat podrobnosti o konfiguraci pomocí následujícího příkladu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Libovolnou část konfigurace podle následujícího příkladu lze aktualizovat:

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Naimportujte modul PowerShellu pro ExpressRoute.

  Abyste mohli začít používat rutiny pro ExpressRoute, musíte nainstalovat nejnovější verzi instalačního programu PowerShellu z [Galerie prostředí PowerShell](http://www.powershellgallery.com/) a naimportovat moduly Azure Resource Manageru do relace PowerShellu. Musíte spustit PowerShell jako správce.

  ```azurepowershell-interactive
  Install-Module AzureRM
  Install-AzureRM
  ```

  Naimportujte všechny moduly AzureRM.* v rozsahu známé sémantické verze.

  ```azurepowershell-interactive
  Import-AzureRM
  ```

  Je můžete také naimportovat jenom modul select v rozsahu známé sémantické verze.

  ```azurepowershell-interactive
  Import-Module AzureRM.Network 
  ```

  Přihlaste se ke svému účtu.

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```

  Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute.

  Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

  * Podsíť /30 pro primární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Podsíť /30 pro sekundární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
  * Volitelné:
    * Hodnota hash MD5, pokud se ji rozhodnete použít.

  Můžete nakonfigurovat soukromý partnerský vztah Azure pro váš okruh v následujícím příkladu:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
  > 
  >

### <a name="getprivate"></a>Chcete-li získat podrobností soukromého partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace podle následujícího příkladu můžete aktualizovat. V tomto příkladu je ID sítě VLAN okruhu aktualizované ze 100 na 500.

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

> [!WARNING]
> Ujistěte se, že před spuštěním tohoto příkladu se odeberou všechny virtuální sítě a připojení ExpressRoute globální dosah. 
> 
> 

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Veřejný partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci veřejného partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. Naimportujte modul PowerShellu pro ExpressRoute.

  Abyste mohli začít používat rutiny pro ExpressRoute, musíte nainstalovat nejnovější verzi instalačního programu PowerShellu z [Galerie prostředí PowerShell](http://www.powershellgallery.com/) a naimportovat moduly Azure Resource Manageru do relace PowerShellu. Musíte spustit PowerShell jako správce.

  ```azurepowershell-interactive
  Install-Module AzureRM

  Install-AzureRM
```

  Naimportujte všechny moduly AzureRM.* v rozsahu známé sémantické verze.

  ```azurepowershell-interactive
  Import-AzureRM
  ```

  Je můžete také naimportovat jenom modul select v rozsahu známé sémantické verze.

  ```azurepowershell-interactive
  Import-Module AzureRM.Network
```

  Přihlaste se ke svému účtu.

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```

  Vyberte předplatné, které chcete vytvořit okruh ExpressRoute.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute.

  Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

3. Zkontrolujte okruh ExpressRoute a ověřte je zřízený a také povolený. Použijte následující příklad:

  ```azurepowershell-interactive
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
4. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Ujistěte se, že máte následující informace předtím, než budete pokračovat dál.

  * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * Volitelné:
    * Hodnota hash MD5, pokud se ji rozhodnete použít.

  Spustit podle následujícího příkladu lze nakonfigurovat veřejný partnerský vztah Azure pro váš okruh

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

  ```azurepowershell-interactive
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
  > 
  >

### <a name="getpublic"></a>Chcete-li získat podrobností veřejného partnerského vztahu Azure

Můžete získat podrobnosti o konfiguraci pomocí následující rutiny:

```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Chcete-li aktualizovat konfiguraci veřejného partnerského vztahu Azure

Libovolnou část konfigurace podle následujícího příkladu můžete aktualizovat. V tomto příkladu je ID sítě VLAN okruhu aktualizované z hodnoty 200 na hodnotu 600.

```azurepowershell-interactive
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Další postup

Dalším krokem je [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
