---
title: 'Azure ExpressRoute: pracovní postupy konfigurace okruhů'
description: Tato stránka zobrazuje pracovní postupy pro konfiguraci okruhu ExpressRoute a partnerských vztahů.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864362"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování a stavy okruhů
Tato stránka vás provede pracovními postupy pro zřizování a konfiguraci směrování na vysoké úrovni.

![pracovní postup okruhu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Následující obrázek a odpovídající kroky popisují úlohy pro zajištění komplexního okruhu ExpressRoute. 

1. Ke konfiguraci okruhu ExpressRoute použijte PowerShell. Další podrobnosti najdete v článku o [Vytvoření okruhů ExpressRoute](expressroute-howto-circuit-classic.md) .
2. Objednat připojení od poskytovatele služeb. Tento proces se liší. Podrobnější informace o tom, jak objednat připojení, získáte od svého poskytovatele připojení.
3. Ujistěte se, že okruh byl úspěšně zřízen tím, že ověřuje stav zřizování okruhu ExpressRoute prostřednictvím PowerShellu. 
4. Nakonfigurujte domény směrování. Pokud poskytovatel připojení spravuje konfiguraci vrstvy 3, nakonfiguruje směrování pro váš okruh. Pokud poskytovatel připojení nabízí pouze služby úrovně 2, je nutné nakonfigurovat směrování podle pokynů popsaných v tématu [požadavky směrování](expressroute-routing.md) a [Konfigurace směrování](expressroute-howto-routing-classic.md) .
   
   * Povolit privátní partnerské vztahy Azure – povolte tomuto partnerskému vztahu připojení k virtuálním počítačům/cloudovým službám nasazeným v rámci virtuálních sítí.

   * Povolit partnerský vztah Microsoftu – Povolte toto oprávnění pro přístup k Microsoft online služby, jako je třeba Office 365. Všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
     
     > [!IMPORTANT]
     > Abyste se mohli připojit k Microsoftu, než je ten, který používáte pro Internet, musíte zajistit, abyste použili samostatný proxy server nebo Edge. Použití stejné hrany pro ExpressRoute i Internet způsobí asymetrické směrování a způsobí výpadky připojení pro vaši síť.
     > 
     > 
     
     ![pracovní postupy směrování](./media/expressroute-workflows/routing-workflow.png)
5. Propojení virtuálních sítí s okruhy ExpressRoute – virtuální sítě můžete propojit se svým okruhem ExpressRoute. Podle pokynů [Připojte virtuální sítě](expressroute-howto-linkvnet-arm.md) k vašemu okruhu. Tyto virtuální sítě můžou být buď ve stejném předplatném Azure jako okruh ExpressRoute, nebo můžou být v jiném předplatném.

## <a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování okruhu ExpressRoute
Každý okruh ExpressRoute má dva stavy:

* Stav zřizování poskytovatele služeb
* Status

Stav představuje stav zřizování společnosti Microsoft. Tato vlastnost je nastavená na povoleno při vytváření okruhu ExpressRoute.

Stav zřizování poskytovatele připojení představuje stav na straně poskytovatele připojení. Může to být buď *NotProvisioned*, *zřizování*, nebo *zřízené*. Okruh ExpressRoute musí být v zřízeném stavu, aby bylo možné konfigurovat partnerské vztahy.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute
V této části najdete seznam možných stavů okruhu ExpressRoute.

**V okamžiku vytvoření**

Okruh ExpressRoute bude při vytváření prostředku hlásit následující stavy.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Když poskytovatel připojení zaznamená proces zřizování okruhu**

Okruh ExpressRoute oznámí následující stavy, zatímco poskytovatel připojení pracuje na zřízení okruhu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Když poskytovatel připojení dokončí proces zajišťování**

Po úspěšném zřízení okruhu poskytovatel připojení oznámí následující stavy ExpressRoute.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Když poskytovatel připojení ruší zřízení okruhu**

Pokud je potřeba zrušit zřízení okruhu ExpressRoute, okruh oznámí následující stavy, jakmile poskytovatel služby dokončí proces zrušení zřízení.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


V případě potřeby ho můžete v případě potřeby znovu povolit, nebo můžete k odstranění okruhu použít rutiny PowerShellu.  

> [!IMPORTANT]
> Okruh nelze odstranit při zřizování nebo zřizování ServiceProviderProvisioningState. Poskytovatel připojení musí zrušit zřízení okruhu, aby ho bylo možné odstranit. Společnost Microsoft bude nadále účtovat okruh, dokud nebude prostředek okruhu ExpressRoute odstraněn v Azure.
> 

## <a name="routing-session-configuration-state"></a>Stav konfigurace relace směrování
Stav zřizování protokolu BGP je v případě, že je relace protokolu BGP povolená na Microsoft Edge. Stav musí být povolen pro použití privátního partnerského vztahu nebo partnerského vztahu Microsoftu.

Je důležité, abyste zkontrolovali stav relace protokolu BGP zvlášť pro partnerský vztah Microsoftu. Kromě stavu zřizování protokolu BGP je k dispozici jiný stav nazvaný *advertised Public prefixs State*. Stav inzerovaných veřejných předpon musí být v *nakonfigurovaném* stavu, obě pro relaci protokolu BGP budou funkční a vaše směrování bude fungovat až po celý. 

Pokud je stav inzerované veřejné předpony nastavený na stav *vyžadováno ověření* , relace protokolu BGP není povolená, protože inzerované předpony neodpovídaly číslu as v žádné z registrů směrování. 

> [!IMPORTANT]
> Pokud stav inzerovaných veřejných předpon je ve stavu *ručního ověření* , budete muset otevřít lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a poskytnout důkaz, že vlastníte IP adresy, které jste si přizpůsobili, spolu s přidruženým číslem autonomního systému.
> 
> 

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)

