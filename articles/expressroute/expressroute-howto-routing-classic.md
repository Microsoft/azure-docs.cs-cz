---
title: 'Azure ExpressRoute: konfigurace partnerského vztahu: klasický'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: a4a3bad1e868fa0e75611630ffb5db5ba13126b6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395549"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute (Classic)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejný partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Tento článek vás provede kroky k vytvoření a správě konfigurace partnerského a směrování pro okruh ExpressRoute pomocí PowerShellu a modelu nasazení Classic. Dál uvedené kroky také ukazují, jak kontrolovat stav partnerských vztahů pro okruh ExpressRoute, aktualizovat je nebo je odstranit a zrušit jejich zřízení. Pro okruh ExpressRoute můžete nakonfigurovat jednu, dvě nebo všechny tři partnerské vztahy (privátní Azure, veřejný Azure a Microsoft). Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. 

Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby pro připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), poskytovatel připojení bude konfigurovat a spravovat směrování za vás.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, postupujte podle pokynů pro [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md) a požádejte ho o povolený okruh poskytovatele připojení. Abyste mohli spouštět rutiny popsané dál, musí být okruh ExpressRoute zřízený a povolený.

### <a name="download-the-latest-powershell-cmdlets"></a>Stáhnout nejnovější rutiny PowerShellu

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Privátní partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci soukromého partnerského vztahu Azure pro okruh ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. **Vytvořte okruh ExpressRoute.**

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený.**
   
   Zkontrolujte, jestli je okruh ExpressRoute zřízený a taky povolený.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Vrací:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ujistěte se, že se okruh zobrazuje jako zřízený a povolený. Pokud tomu tak není, spolupracujte se svým poskytovatelem připojení, abyste získali svůj okruh do požadovaného stavu a stavu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Nakonfigurujte soukromý partnerský vztah Azure pro okruh.**

   Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:
   
   * Podsíť /30 pro primární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu nepoužívá stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Ověřte, že nepoužíváte 65515.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné**.
     
   K nakonfigurování privátního partnerského vztahu Azure pro váš okruh můžete použít následující příklad:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Pokud chcete použít hodnotu hash MD5, použijte následující příklad ke konfiguraci privátního partnerského vztahu pro váš okruh:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Ověřte, že jste zadali své číslo AS partnerského vztahu ASN, ne ASN zákazníka.
   > 

### <a name="to-view-azure-private-peering-details"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Podrobnosti o konfiguraci můžete zobrazit pomocí následující rutiny:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Vrací:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V následujícím příkladu je ID sítě VLAN okruhu Aktualizováno z 100 na 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny. Před spuštěním této rutiny je nutné zajistit, aby všechny virtuální sítě byly z okruhu ExpressRoute odpojování.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Veřejný partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci veřejného partnerského vztahu Azure pro okruh ExpressRoute.

> [!NOTE]
> Veřejné partnerské vztahy Azure se pro nové okruhy zastaraly.
>

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. **Vytvoření okruhu ExpressRoute**

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený.**

   Nejdřív musíte zkontrolovat, že stav okruhu ExpressRoute je Zřízený a také Povolený.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Vrací:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ověřte, že se okruh zobrazuje jako zřízený a povolený. Pokud tomu tak není, spolupracujte se svým poskytovatelem připojení, abyste získali svůj okruh do požadovaného stavu a stavu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Konfigurace veřejného partnerského vztahu Azure pro okruh**
   
   Než budete pokračovat, ujistěte se, že máte následující informace:
   
   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu nepoužívá stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné**.

   > [!IMPORTANT]
   > Ujistěte se, že jste zadali číslo AS partnerského vztahu ASN a nikoli číslo ASN zákazníka.
   >  
     
   Pomocí následujícího příkladu můžete nakonfigurovat veřejný partnerský vztah Azure pro váš okruh:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Pokud chcete použít hodnotu hash MD5, použijte následující příklad ke konfiguraci okruhu:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Chcete-li zobrazit podrobnosti o konfiguraci, použijte následující rutinu:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Vrací:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V tomto příkladu je ID sítě VLAN okruhu Aktualizováno z 200 na 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Ověřte, že se okruh zobrazuje jako zřízený a povolený. 
### <a name="to-delete-azure-public-peering"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. **Vytvoření okruhu ExpressRoute**
  
   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený.**

   Ověřte, že se okruh zobrazuje jako zřízený a povolený. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Vrací:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ověřte, že se okruh zobrazuje jako zřízený a povolený. Pokud tomu tak není, spolupracujte se svým poskytovatelem připojení, abyste získali svůj okruh do požadovaného stavu a stavu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurace partnerského vztahu Microsoftu pro okruh**
   
    Před pokračováním se ujistěte, že máte k dispozici následující informace.
   
   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu nepoužívá stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované. **Volitelné**.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné.**
     
   Spusťte následující rutinu pro konfiguraci partnerského vztahu Microsoftu pro váš okruh:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete zobrazit pomocí následující rutiny:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Vrací:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Další kroky

Dále [propojte virtuální síť s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Další informace o pracovních postupech najdete v tématu [pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
