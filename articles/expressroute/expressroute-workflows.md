---
title: Pracovní postupy pro konfiguraci okruh ExpressRoute | Microsoft Docs
description: Tato stránka vás provede pracovní postupy pro konfiguraci okruhu ExpressRoute a partnerských vztahů
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 55e0418c-e0bf-44a7-9aa1-720076df9297
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: cherylmc
ms.openlocfilehash: cba1b2cfee379e7d2b079bcb3089981ef1044d66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850774"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování a stavy okruhů
Tato stránka vás provede procesem služby zřizování a pracovní postupy konfigurace směrování na vysoké úrovni.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Následující obrázek a příslušné postupy ukazují úlohy, které je třeba provést, aby bylo možné používat okruhu ExpressRoute zřízeného začátku do konce. 

1. Nakonfigurujte okruh ExpressRoute pomocí prostředí PowerShell. Postupujte podle pokynů [okruhy ExpressRoute vytvořit](expressroute-howto-circuit-classic.md) další podrobnosti najdete v článku.
2. Pořadí připojení od poskytovatele služeb. Tento proces se liší. Další informace o tom, jak pořadí připojení, obraťte se na svého poskytovatele připojení.
3. Ujistěte se, že je okruh má úspěšně zřízený kontrolou stavu pomocí prostředí PowerShell zřizování okruh ExpressRoute. 
4. Konfigurace domény směrování. Pokud poskytovatel připojení vrstvy 3 spravuje za vás, nakonfiguruje směrování pro váš okruh. Pokud poskytovatel připojení nabízí pouze služby vrstvy 2, je nutné nakonfigurovat směrování podle pokynů popsaných v [požadavky na směrování](expressroute-routing.md) a [konfigurace směrování](expressroute-howto-routing-classic.md) stránky.
   
   * Povolení soukromého partnerského vztahu Azure – je nutné povolit tohoto partnerského vztahu se připojit k virtuálním počítačům nebo cloudových služeb nasadit v rámci virtuální sítě.
   * Povolit veřejný partnerský vztah Azure – je nutné povolit veřejný partnerský vztah Azure Pokud se chcete připojit ke službám Azure, které jsou hostované na veřejné IP adresy. Toto je požadavek pro přístup k prostředkům Azure, pokud jste vybrali k povolení výchozí směrování pro soukromý partnerský vztah Azure.
   * Povolit partnerského vztahu Microsoftu – to je nutné povolit přístup k Office 365 a Dynamics 365. 
     
     > [!IMPORTANT]
     > Ujistěte se, že používáte samostatné proxy server / edge připojovali k serverům Microsoftu než ten, který můžete použít pro Internet. Pomocí stejné hraniční pro ExpressRoute a Internetem se způsobit asymetrické směrování a připojení k výpadkům ve vaší síti.
     > 
     > 
     
     ![](./media/expressroute-workflows/routing-workflow.png)
5. Propojení virtuální sítě pro okruhy ExpressRoute - můžete propojit virtuální sítě k okruhu ExpressRoute. Postupujte podle pokynů [propojení virtuálních sítí](expressroute-howto-linkvnet-arm.md) pro váš okruh. Tyto virtuální sítě může být v rámci stejného předplatného Azure jako okruh ExpressRoute, nebo může být v jiném předplatném.

## <a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování okruh ExpressRoute
Každý okruh ExpressRoute má dva stavy:

* Stav zřizování poskytovatele služby
* Status

Stav představuje stav zřizování společnosti Microsoft. Tato vlastnost nastavena na hodnotu povoleno vytvoření okruhu Expressroute

Stav zřizování poskytovatele připojení představuje stav na straně poskytovatele připojení. Může to být *NotProvisioned*, *zřizování*, nebo *zajištěno*. Okruh ExpressRoute musí být ve stavu zajištěno, abyste mohli používat ho.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute
Tato část uvádí se možné stavy pro okruh ExpressRoute.

**V okamžiku vytvoření**

Okruh ExpressRoute v následujícím stavu se zobrazí při spuštění rutiny prostředí PowerShell k vytvoření okruhu ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Pokud probíhá zřizování okruhu poskytovatele připojení**

Okruh ExpressRoute v následujícím stavu se zobrazí, jakmile je předat klíč služby do poskytovatele připojení a zahájil proces zřizování.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po dokončení procesu zřizování poskytovatele připojení**

Okruh ExpressRoute v následujícím stavu se zobrazí při dokončení procesu zřizování poskytovatele připojení.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Zřízený a je povoleno pouze stav okruhu může být v, abyste mohli používat ho. Pokud používáte poskytovatele vrstvy 2, můžete nakonfigurovat směrování pro váš okruh, pouze pokud je v tomto stavu.

**Když je poskytovatel připojení zrušení zřízení okruhu**

Pokud jste požádali o poskytovateli služby zrušení zřízení okruh ExpressRoute, zobrazí se okruh nastavit následující stav po dokončení proces zrušení zřízení poskytovatele služeb.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Můžete znovu ji povolit, pokud je potřeba, nebo spusťte rutiny prostředí PowerShell odstranit okruh.  

> [!IMPORTANT]
> Pokud spustíte rutinu prostředí PowerShell odstranit okruh, když je zřizování ServiceProviderProvisioningState nebo zajištěno se operace nezdaří. Spojte se s poskytovatelem připojení nejprve zrušit jejich zřízení okruh ExpressRoute a pak odstraňte okruh. Microsoft bude účtovat pošle okruh, dokud nespustíte rutiny prostředí PowerShell odstranit okruh.
> 
> 

## <a name="routing-session-configuration-state"></a>Stav konfigurace směrování relace
Protokol BGP Stav zřizování umožňuje zjistit, zda byl povolen relaci protokolu BGP v Microsoft edge. Abyste mohli používat partnerského vztahu musí být povolen stav.

Je důležité zkontrolovat stav relace protokolu BGP hlavně pro partnerský vztah Microsoftu. Kromě BGP stavu zřizování, je jiný stav názvem *inzerované předpony veřejných stavu*. Inzerované předpony veřejných stavu musí být v *nakonfigurované* stavu, jak pro relaci protokolu BGP jako nahoru a směrování fungovat začátku do konce. 

Pokud je nastavena do stavu inzerovaný předponu veřejné *ověření potřeby* stavu relaci protokolu BGP není povolen, protože inzerované předpony neodpovídá číslo AS v některém z registrech směrování. 

> [!IMPORTANT]
> Pokud je stav ohlášeného předpony veřejných v *ruční ověřování* stavu, musíte otevřít lístek podpory s [podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a prokázání, že vlastníte inzerované IP adresy spolu s přidružená čísla autonomního systému.
> 
> 

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)

