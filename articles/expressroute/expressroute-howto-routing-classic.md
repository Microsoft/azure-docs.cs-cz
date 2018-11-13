---
title: 'Postup konfigurace směrování (partnerského vztahu) pro ExpressRoute okruhu: Azure: classic | Dokumentace Microsoftu'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc;ganesr
ms.openlocfilehash: 6e099d0cdf659aa6ed2ccbef1381021ae55c72c2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261838"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute (classic)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – veřejné partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Tento článek vás provede kroky k vytvoření a správě konfigurace směrování pro okruh ExpressRoute pomocí Powershellu a modelu nasazení classic. Dál uvedené kroky také ukazují, jak kontrolovat stav partnerských vztahů pro okruh ExpressRoute, aktualizovat je nebo je odstranit a zrušit jejich zřízení. Můžete nakonfigurovat jeden, dva nebo všechny tři partnerské vztahy (Azure privátní, veřejný Azure a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. 

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb, které nabízejí služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), se svého poskytovatele připojení, konfiguraci a správu směrování za vás.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Postupujte podle pokynů a [vytvořit okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho povolený podle svého poskytovatele připojení, než budete pokračovat. Abyste mohli spouštět rutiny popsané dál, musí být okruh ExpressRoute zřízený a povolený.

### <a name="download-the-latest-powershell-cmdlets"></a>Stáhněte si nejnovější rutiny prostředí PowerShell

Nainstalujte nejnovější verze modulů Azure Service Management Powershellu a modul ExpressRoute. Při použití v následujícím příkladu, mějte na paměti, že číslo verze (v tomto příkladu 5.1.1) se změní vydané novější verze rutin.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Další informace najdete v tématu [Začínáme s rutinami Azure Powershellu](/powershell/azure/overview) podrobné pokyny o tom, jak nakonfigurovat počítač pomocí modulů Azure Powershellu.

### <a name="sign-in"></a>Přihlášení

Přihlásit se ke svému účtu Azure, použijte následující příklady:

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. V dalším kroku použijte následující rutinu k vašemu předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

  ```powershell
  Add-AzureAccount
  ```

## <a name="azure-private-peering"></a>Soukromý partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci soukromého partnerského vztahu Azure pro okruh ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. **Vytvoření okruhu ExpressRoute.**

  Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute, abyste měli jistotu, že je zřízený.**
   
  Zaškrtněte, pokud chcete zobrazit, pokud je okruh ExpressRoute zřízený a také povolený.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Návratová hodnota:

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
   
  Ujistěte se, že je okruh ukazovat zřízená a povolen. Pokud tomu tak není, fungovat u svého poskytovatele připojení, chcete-li získat váš okruh k požadovanému stavu a stavu.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Nakonfigurujte soukromý partnerský vztah Azure pro okruh.**

  Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:
   
  * Podsíť /30 pro primární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Podsíť /30 pro sekundární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu používá stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Ověřte, že nepoužíváte 65515.
  * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné**.
     
  V následujícím příkladu můžete použít ke konfiguraci soukromého partnerského vztahu Azure pro váš okruh:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
  ```    

  Pokud chcete použít hodnotu hash MD5, použijte ke konfiguraci soukromého partnerského vztahu pro váš okruh v následujícím příkladu:

  ```powershell
  New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
  ```
     
  > [!IMPORTANT]
  > Ověřte, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
  > 

### <a name="to-view-azure-private-peering-details"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Můžete zobrazit podrobnosti o konfiguraci pomocí následující rutiny:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Návratová hodnota:

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

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V následujícím příkladu je ID sítě VLAN okruhu aktualizované ze 100 na 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny. Musí se ujistěte, že všechny virtuální sítě se odpojit od okruhu ExpressRoute před spuštěním této rutiny.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Veřejný partnerský vztah Azure

Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci veřejného partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. **Vytvoření okruhu ExpressRoute**

  Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
2. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený**

  Nejdřív musíte zkontrolovat, že stav okruhu ExpressRoute je Zřízený a také Povolený.

  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Návratová hodnota:

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
   
  Ověřte, že je okruh ukazovat zřízená a povolen. Pokud tomu tak není, fungovat u svého poskytovatele připojení, chcete-li získat váš okruh k požadovanému stavu a stavu.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
4. **Nakonfigurujte veřejný partnerský vztah Azure pro okruh**
   
  Ujistěte se, že máte následující informace předtím, než budete pokračovat:
   
  * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu používá stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné**.

  > [!IMPORTANT]
  > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN a ne zákaznické číslo ASN.
  >  
     
  V následujícím příkladu můžete použít ke konfiguraci veřejného partnerského vztahu Azure pro váš okruh:

  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
  ```
     
  Pokud chcete použít hodnotu hash MD5, použijte ke konfiguraci váš okruh v následujícím příkladu:
     
  ```powershell
  New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
  ```
     
### <a name="to-view-azure-public-peering-details"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Chcete-li zobrazit podrobnosti o konfiguraci, použijte následující rutinu:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Návratová hodnota:

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

Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V tomto příkladu je ID sítě VLAN okruhu aktualizované z hodnoty 200 na hodnotu 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Ověřte, že je okruh ukazovat zřízená a povolen. 
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
2. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený**

  Ověřte, že je okruh ukazovat zřízená a povolen. 
   
  ```powershell
  Get-AzureDedicatedCircuit -ServiceKey "*********************************"
  ```

  Návratová hodnota:
   
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
   
  Ověřte, že je okruh ukazovat zřízená a povolen. Pokud tomu tak není, fungovat u svého poskytovatele připojení, chcete-li získat váš okruh k požadovanému stavu a stavu.

  ```powershell
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  ```
3. **Konfigurace partnerského vztahu Microsoftu pro okruh**
   
    Před pokračováním se ujistěte, že máte k dispozici následující informace.
   
   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Ověřte, že žádný jiný partnerský vztah v okruhu používá stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované. **Volitelné**.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Volitelné.**
     
  Spuštěním následující rutiny můžete nakonfigurovat partnerský vztah Microsoftu pro váš okruh:
 
  ```powershell
  New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
  ```

### <a name="to-view-microsoft-peering-details"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Můžete zobrazit podrobnosti o konfiguraci pomocí následující rutiny:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Návratová hodnota:

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

Libovolnou část konfigurace pomocí následující rutiny můžete aktualizovat:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Další postup

Dále [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Další informace o pracovních postupech najdete v tématu [pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).