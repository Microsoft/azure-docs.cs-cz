---
title: 'Postup konfigurace směrování (partnerský vztah) pro ExpressRoute okruh: Azure: classic | Microsoft Docs'
description: Tento článek vás provede kroky pro vytváření a zřizování soukromého a veřejného partnerského vztahu a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 9cebb196bd91da704798fb001763a76e6d090472
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Vytvářet a upravovat partnerský vztah pro okruh ExpressRoute (klasické)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - soukromého partnerského vztahu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - veřejného partnerského vztahu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - partnerského vztahu Microsoftu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

Tento článek vás provede kroky k vytvoření a správě konfigurace směrování pro okruhu ExpressRoute pomocí Powershellu a modelu nasazení classic. Dál uvedené kroky také ukazují, jak kontrolovat stav partnerských vztahů pro okruh ExpressRoute, aktualizovat je nebo je odstranit a zrušit jejich zřízení.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Předpoklady konfigurace
* Budete potřebovat nejnovější verzi rutin prostředí PowerShell Azure Service Management (SM). Další informace najdete v tématu [Začínáme s rutinami prostředí Azure PowerShell](/powershell/azure/overview).  
* Před zahájením konfigurace se ujistěte, že jste si přečetli stránku s [předpoklady](expressroute-prerequisites.md), stránku s [požadavky směrování](expressroute-routing.md) a stránku s [pracovními postupy](expressroute-workflows.md).
* Musí mít aktivní okruh ExpressRoute. Postupujte podle pokynů a [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho povolený vaším poskytovatelem připojení, než budete pokračovat. Abyste mohli spouštět rutiny popsané dál, musí být okruh ExpressRoute zřízený a povolený.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb nabízejícího spravované služby vrstvy 3 (obvykle IPVPN, např. MPLS), poskytovatel připojení provede konfiguraci a správu směrování za vás.
> 
> 

Můžete nakonfigurovat jeden, dva nebo všechny tři partnerské vztahy (soukromý Azure, veřejný Azure a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Přihlaste se k účtu Azure a vybrat odběr
1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

        Connect-AzureRmAccount

2. Zkontrolujte předplatná pro příslušný účet.

        Get-AzureRmSubscription

3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Potom použijte následující rutinu k předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Soukromý partnerský vztah Azure
Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci soukromého partnerského vztahu Azure pro okruh ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure
1. **Naimportujte modul Powershellu pro ExpressRoute.**
   
    Pokud chcete začít používat rutiny pro ExpressRoute, je nutné naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Spusťte následující příkazy a naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Verze se může lišit.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Vytvoření okruhu ExpressRoute.**
   
    Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál. 
3. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený.**
   
    Nejdřív musíte zkontrolovat, že stav okruhu ExpressRoute je Zřízený a také Povolený. Viz následující příklad.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Ujistěte se, že je okruh zobrazuje jako zajištěno a povoleno. Pokud tomu tak není, fungovat u svého poskytovatele připojení získat váš okruh. požadovaný stav a stav.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Nakonfigurujte soukromý partnerský vztah Azure pro okruh.**
   
    Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:
   
   * Podsíť /30 pro primární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Tato položka je nepovinná.**
     
    Spuštěním následující rutiny můžete nakonfigurovat soukromý partnerský vztah Azure pro váš okruh.
     
        Nové AzureBGPPeering - AccessType privátní - klíč ServiceKey "***" - PrimaryPeerSubnet "10.0.0.0/30" - SecondaryPeerSubnet "10.0.0.4/30" - PeerAsn 1234 - VlanId 100
     
    Pokud se rozhodnete použít hodnotu hash MD5, můžete použít následující rutinu.
     
        Nové AzureBGPPeering - AccessType privátní - klíč ServiceKey "***" - PrimaryPeerSubnet "10.0.0.0/30" - SecondaryPeerSubnet "10.0.0.4/30" - PeerAsn 1234 - VlanId 100 - SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Zobrazení podrobností soukromého partnerského vztahu Azure
Můžete získat podrobnosti o konfiguraci pomocí následující rutiny.

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

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


### <a name="to-update-azure-private-peering-configuration"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure
Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny. V následujícím příkladu je ID sítě VLAN okruhu aktualizováno z hodnoty 100 na hodnotu 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Odstranění soukromého partnerského vztahu Azure
Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny.

> [!WARNING]
> Před spuštěním této rutiny je nutné zajistit, že všechny virtuální sítě jsou od okruhu ExpressRoute odpojené. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Veřejný partnerský vztah Azure
Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci veřejného partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure
1. **Naimportujte modul Powershellu pro ExpressRoute.**
   
    Pokud chcete začít používat rutiny pro ExpressRoute, je nutné naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Spusťte následující příkazy a naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Verze se může lišit.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Vytvoření okruhu ExpressRoute**
   
    Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
3. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený**
   
    Nejdřív musíte zkontrolovat, že stav okruhu ExpressRoute je Zřízený a také Povolený. Viz následující příklad.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Ujistěte se, že je okruh zobrazuje jako zajištěno a povoleno. Pokud tomu tak není, fungovat u svého poskytovatele připojení získat váš okruh. požadovaný stav a stav.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Nakonfigurujte veřejný partnerský vztah Azure pro okruh**
   
    Před pokračováním se ujistěte, že máte k dispozici následující informace.
   
   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Tato položka je nepovinná.**
     
    Spuštěním následující rutiny můžete nakonfigurovat veřejný partnerský vztah Azure pro váš okruh.
     
        Nové AzureBGPPeering - AccessType veřejný - klíč ServiceKey "***" - PrimaryPeerSubnet "131.107.0.0/30" - SecondaryPeerSubnet "131.107.0.4/30" - PeerAsn 1234 - VlanId 200
     
    Pokud se rozhodnete použít hodnotu hash MD5, můžete použít následující rutinu.
     
        Nové AzureBGPPeering - AccessType veřejný - klíč ServiceKey "***" - PrimaryPeerSubnet "131.107.0.0/30" - SecondaryPeerSubnet "131.107.0.4/30" - PeerAsn 1234 - VlanId 200 - SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, a ne zákaznické číslo ASN.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Zobrazení podrobností veřejného partnerského vztahu Azure
Můžete získat podrobnosti o konfiguraci pomocí následující rutiny.

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

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


### <a name="to-update-azure-public-peering-configuration"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure
Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny.

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

V předchozím příkladu je ID sítě VLAN okruhu aktualizováno z hodnoty 200 na hodnotu 600.

### <a name="to-delete-azure-public-peering"></a>Odstranění veřejného partnerského vztahu Azure
Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny.

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Partnerský vztah Microsoftu
Tato část obsahuje pokyny, jak vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu
1. **Naimportujte modul Powershellu pro ExpressRoute.**
   
    Pokud chcete začít používat rutiny pro ExpressRoute, je nutné naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Spusťte následující příkazy a naimportovat moduly Azure a ExpressRoute do relace prostředí PowerShell. Verze se může lišit.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Vytvoření okruhu ExpressRoute**
   
    Podle pokynů vytvořte [okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete poskytovatele připojení požádat, aby povolil soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování, po vytvoření okruhu postupujte podle pokynů dál.
3. **Zkontrolujte okruh ExpressRoute a ověřte, že je zřízený**
   
    Nejdřív musíte zkontrolovat, jestli okruh ExpressRoute je ve stavu zajištěno a povoleno.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Ujistěte se, že je okruh zobrazuje jako zajištěno a povoleno. Pokud tomu tak není, fungovat u svého poskytovatele připojení získat váš okruh. požadovaný stav a stav.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Nakonfigurujte partnerský vztah Microsoftu pro okruh**
   
    Před pokračováním se ujistěte, že máte k dispozici následující informace.
   
   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, na které jsou registrované. **Tato položka je nepovinná.**
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * Hodnota hash MD5, pokud se ji rozhodnete použít. **Tato položka je nepovinná.**
     
    Spuštěním následující rutiny můžete nakonfigurovat Microsoft pering pro váš okruh.
     
        Nové AzureBGPPeering - AccessType Microsoft - klíč ServiceKey "***" "131.107.0.0/30" - PrimaryPeerSubnet - SecondaryPeerSubnet "131.107.0.4/30" - VlanId 300 - PeerAsn 1234 - CustomerAsn. 2245 - AdvertisedPublicPrefixes " 123.0.0.0/30 "- RoutingRegistryName"ARIN"- SharedKey"A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Zobrazení podrobností partnerského vztahu Microsoftu
Můžete získat podrobnosti o konfiguraci pomocí následující rutiny.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

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


### <a name="to-update-microsoft-peering-configuration"></a>Aktualizace konfigurace partnerského vztahu Microsoftu
Libovolnou část konfigurace můžete aktualizovat pomocí následující rutiny.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Odstranění partnerského vztahu Microsoftu
Konfiguraci partnerského vztahu můžete odebrat spuštěním následující rutiny.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Další postup
Dále [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Další informace o pracovních postupech najdete v tématu [pracovních postupech](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).

