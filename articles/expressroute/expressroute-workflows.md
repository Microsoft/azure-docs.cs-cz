---
title: 'Azure ExpressRoute: pracovní postupy konfigurace okruhů'
description: Tato stránka zobrazuje pracovní postupy pro konfiguraci okruh ExpressRoute a partnerské vztahy
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864362"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování okruhů a stavy okruhů
Tato stránka vás provede službu zřizování a pracovní postupy konfigurace směrování na vysoké úrovni.

![pracovní postup okruh](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Následující obrázek a odpovídající kroky popisují úlohy pro zajištění komplexního okruhu ExpressRoute. 

1. Nakonfigurujte okruh ExpressRoute pomocí prostředí PowerShell. Postupujte podle pokynů [okruhy ExpressRoute vytvořit](expressroute-howto-circuit-classic.md) , kde najdete další podrobnosti.
2. Připojení k pořadí od poskytovatele služeb. Tento proces se liší. Obraťte se na svého poskytovatele připojení, další podrobnosti o tom, jak uspořádat připojení.
3. Ujistěte se, že okruh se úspěšně zřízený ověřením přes PowerShell Stav zřizování okruhu ExpressRoute. 
4. Konfigurace domény směrování. Pokud poskytovatel připojení spravuje konfiguraci vrstvy 3, nakonfiguruje směrování pro váš okruh. Pokud poskytovatel připojení nabízí pouze služby úrovně 2, je nutné nakonfigurovat směrování podle pokynů popsaných v tématu [požadavky směrování](expressroute-routing.md) a [Konfigurace směrování](expressroute-howto-routing-classic.md) .
   
   * Povolení soukromého partnerského vztahu Azure – povolit tento partnerský vztah pro připojení k virtuálním počítačům nebo službám nasazeným v rámci virtuální sítě v cloudu.

   * Povolit partnerský vztah Microsoftu – Povolte toto oprávnění pro přístup k Microsoft online služby, jako je třeba Office 365. Všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
     
     > [!IMPORTANT]
     > Ujistěte se, že budete používat samostatný proxy server / edge pro připojení k Microsoft než ten, který můžete použít pro Internet. Pomocí stejného edge pro ExpressRoute a Internetem způsobí asymetrického směrování a způsobí výpadek připojení pro vaši síť.
     > 
     > 
     
     ![směrování pracovních postupů](./media/expressroute-workflows/routing-workflow.png)
5. Propojení virtuálních sítí s okruhy ExpressRoute – můžete propojit virtuální sítě pro váš okruh ExpressRoute. Postupujte podle pokynů [k propojení virtuálních sítí](expressroute-howto-linkvnet-arm.md) pro váš okruh. Tyto virtuální sítě může být ve stejném předplatném Azure jako okruh ExpressRoute, nebo může být v jiném předplatném.

## <a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování okruhu ExpressRoute
Každý okruh ExpressRoute má dva stavy:

* Stav zřizování poskytovatele služby
* Stav

Stav představuje stav zřizování společnosti Microsoft. Tato vlastnost nastavena na hodnotu povoleno vytvoření okruhu Expressroute

Stav zřizování poskytovatele připojení představuje stav na straně poskytovatele připojení. Může jí být buď *NotProvisioned*, *zřizování*, nebo *zřízená*. Okruh ExpressRoute musí být v zřízeném stavu, aby bylo možné konfigurovat partnerské vztahy.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute
V této části najdete seznam možných stavů okruhu ExpressRoute.

**V okamžiku vytvoření**

Okruh ExpressRoute bude při vytváření prostředku hlásit následující stavy.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Pokud poskytovatel připojení probíhá zřizování okruhu**

Okruh ExpressRoute oznámí následující stavy, zatímco poskytovatel připojení pracuje na zřízení okruhu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po dokončení procesu zřizování poskytovatele připojení**

Po úspěšném zřízení okruhu poskytovatel připojení oznámí následující stavy ExpressRoute.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Pokud poskytovatel připojení ruší zřízení okruhu**

Pokud je potřeba zrušit zřízení okruhu ExpressRoute, okruh oznámí následující stavy, jakmile poskytovatel služby dokončí proces zrušení zřízení.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Můžete ji znovu povolit Pokud potřeby, nebo spusťte rutiny prostředí PowerShell se odstranit okruh.  

> [!IMPORTANT]
> Okruh nelze odstranit při zřizování nebo zřizování ServiceProviderProvisioningState. Poskytovatel připojení musí zrušit zřízení okruhu, aby ho bylo možné odstranit. Společnost Microsoft bude nadále účtovat okruh, dokud nebude prostředek okruhu ExpressRoute odstraněn v Azure.
> 

## <a name="routing-session-configuration-state"></a>Stav konfigurace směrování relace
Stav zřizování protokolu BGP je v případě, že je relace protokolu BGP povolená na Microsoft Edge. Stav musí být povolen pro použití privátního partnerského vztahu nebo partnerského vztahu Microsoftu.

Je důležité zkontrolovat stav relace protokolu BGP zejména pro partnerský vztah Microsoftu. Kromě protokolu BGP Stav zřizování je jiný stav, volá *inzerované veřejné prefixy stavu*. Stav inzerovaných veřejných předpon musí být v *nakonfigurovaném* stavu, obě pro relaci protokolu BGP budou funkční a vaše směrování bude fungovat až po celý. 

Pokud státu inzerované veřejné předpony je nastavená na *vyžaduje se ověření* stavu relace protokolu BGP není povolená, protože předpony inzerované neodpovídá číslo AS v některém z registrech směrování. 

> [!IMPORTANT]
> Pokud stav inzerovaných veřejných předpon je ve stavu *ručního ověření* , budete muset otevřít lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a poskytnout důkaz, že vlastníte IP adresy, které jste si přizpůsobili, spolu s přidruženým číslem autonomního systému.
> 
> 

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)

