---
title: 'Azure ExpressRoute: Konfigurace partnerského vztahu: klasický'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 05602538f206032d924b39a7dd8f4325c48a5224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931375"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute (klasický)
> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - Soukromý partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Veřejný partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Tento článek vás provede kroky k vytvoření a správě konfigurace partnerského vztahu a směrování pro okruh ExpressRoute pomocí prostředí PowerShell a klasického modelu nasazení. Dál uvedené kroky také ukazují, jak kontrolovat stav partnerských vztahů pro okruh ExpressRoute, aktualizovat je nebo je odstranit a zrušit jejich zřízení. Můžete nakonfigurovat jeden, dva nebo všechny tři partnerské vztahy (Azure private, Azure public a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. 

Tyto pokyny platí pouze pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Postupujte podle pokynů k [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md) a mít okruh povolen poskytovatelem připojení, než budete pokračovat. Abyste mohli spouštět rutiny popsané dál, musí být okruh ExpressRoute zřízený a povolený.

### <a name="download-the-latest-powershell-cmdlets"></a>Stažení nejnovějších rutin prostředí PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Soukromý partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci soukromého partnerského vztahu Azure pro okruh ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. **Vytvořte okruh ExpressRoute.**

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena.**
   
   Zkontrolujte, zda je okruh ExpressRoute zřízena a také povoleno.

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
   
   Ujistěte se, že obvod zobrazuje jako zřízená a povolená. Pokud tomu tak není, spolupracujte s poskytovatelem připojení a získejte okruh do požadovaného stavu a stavu.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Nakonfigurujte soukromý partnerský vztah Azure pro okruh.**

   Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:
   
   * Podsíť /30 pro primární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu nepoužívá stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Ověřte, zda nepoužíváte 65515.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Nepovinné**.
     
   Následující příklad můžete použít ke konfiguraci privátnípartnerský vztah Azure pro váš okruh:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Pokud chcete použít hash MD5, použijte následující příklad konfigurace soukromého partnerského vztahu pro váš okruh:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Ověřte, zda zadáte číslo AS jako číslo partnerského vztahu ASN, nikoli číslo ASN zákazníka.
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

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V následujícím příkladu je ID sítě VLAN okruhu aktualizováno ze 100 na 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny. Před spuštěním této rutiny je nutné se ujistit, že všechny virtuální sítě jsou odpojeny od okruhu ExpressRoute.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Veřejný partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci veřejného partnerského vztahu Azure pro okruh ExpressRoute.

> [!NOTE]
> Veřejný partnerský vztah Azure se pro nové okruhy nezastaral.
>

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. **Vytvoření okruhu ExpressRoute**

   Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ověřte, zda je zřízen**

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
   
   Ověřte, zda se okruh zobrazuje jako Zřízený a Povolený. Pokud tomu tak není, spolupracujte s poskytovatelem připojení a získejte okruh do požadovaného stavu a stavu.

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
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Nepovinné**.

   > [!IMPORTANT]
   > Ujistěte se, že zadáte číslo AS jako partnerský asn a nikoli zákazník ASN.
   >  
     
   Následující příklad můžete použít ke konfiguraci veřejného partnerského vztahu Azure pro váš okruh:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Pokud chcete použít hash MD5, použijte následující příklad konfigurace okruhu:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Chcete-li zobrazit podrobnosti konfigurace, použijte následující rutinu:

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

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V tomto příkladu je ID sítě VLAN okruhu aktualizováno z 200 na 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Ověřte, zda se okruh zobrazuje jako Zřízený a Povolený. 
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
2. **Zkontrolujte okruh ExpressRoute a ověřte, zda je zřízen**

   Ověřte, zda se okruh zobrazuje jako Zřízený a Povolený. 
   
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
   
   Ověřte, zda se okruh zobrazuje jako Zřízený a Povolený. Pokud tomu tak není, spolupracujte s poskytovatelem připojení a získejte okruh do požadovaného stavu a stavu.

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
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované. **Nepovinné**.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné.**
     
   Spusťte následující rutinu a nakonfigurujte partnerský vztah Microsoftu pro váš okruh:
 
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

Dále [propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Další informace o pracovních postupech naleznete v [tématu Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
