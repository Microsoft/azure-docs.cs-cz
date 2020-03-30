---
title: 'Azure ExpressRoute: Pracovní postupy konfigurace okruhů'
description: Na této stránce jsou zobrazeny pracovní postupy pro konfiguraci okruhu ExpressRoute a partnerských stran.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864362"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování a stavy okruhů
Tato stránka vás provede pracovní postupy konfigurace zřizování služeb a směrování na vysoké úrovni.

![pracovní postup okruhu](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Následující obrázek a odpovídající kroky popisují úkoly pro zřízení okruhu ExpressRoute od konce do konce. 

1. Ke konfiguraci okruhu ExpressRoute použijte prostředí PowerShell. Další podrobnosti naleznete v pokynech v článku [Vytvořit okruhy ExpressRoute.](expressroute-howto-circuit-classic.md)
2. Objednejte připojení od poskytovatele služeb. Tento proces se liší. Další informace o způsobu objednání připojení získáte od svého poskytovatele připojení.
3. Ujistěte se, že okruh byl úspěšně zřízen ověřením stavu zřizování okruhu ExpressRoute prostřednictvím prostředí PowerShell. 
4. Konfigurace směrovacích domén. Pokud váš poskytovatel připojení spravuje konfiguraci vrstvy 3, nakonfiguruje směrování pro váš okruh. Pokud váš poskytovatel připojení nabízí pouze služby vrstvy 2, je nutné nakonfigurovat směrování podle pokynů popsaných v [požadavcích na směrování](expressroute-routing.md) a [na konfiguračních](expressroute-howto-routing-classic.md) stránkách směrování.
   
   * Povolit privátní partnerský vztah Azure – povolit tento partnerský vztah pro připojení k virtuálním počítačům / cloudovým službám nasazeným v rámci virtuálních sítí.

   * Povolení partnerského vztahu Microsoftu – Povolte přístup k online službám Microsoftu, jako je Office 365. Všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
     
     > [!IMPORTANT]
     > Musíte zajistit, že používáte samostatný proxy / okraj pro připojení k Microsoftu, než ten, který používáte pro Internet. Použití stejné hrany pro ExpressRoute i Internet způsobí asymetrické směrování a způsobí výpadky připojení pro vaši síť.
     > 
     > 
     
     ![pracovní postupy směrování](./media/expressroute-workflows/routing-workflow.png)
5. Propojení virtuálních sítí s okruhy ExpressRoute – virtuální sítě můžete propojit s okruhem ExpressRoute. Postupujte podle pokynů [k propojení virtuálních sítí](expressroute-howto-linkvnet-arm.md) s okruhem. Tyto virtuální sítě může být buď ve stejném předplatném Azure jako okruh ExpressRoute, nebo může být v jiném předplatném.

## <a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování okruhů ExpressRoute
Každý okruh ExpressRoute má dva stavy:

* Stav zřizování poskytovatele služeb
* Status

Stav představuje stav zřizování společnosti Microsoft. Tato vlastnost je nastavena na povoleno při vytváření okruhu Expressroute

Stav zřizování zprostředkovatele připojení představuje stav na straně poskytovatele připojení. Může být buď *NotProvisioned*, *Provisioning*nebo *Provisioned*. Okruh ExpressRoute musí být ve stavu Zřízeno, aby bylo možné konfigurovat partnerský vztah.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute
V této části jsou uvedeny možné stavy okruhu ExpressRoute.

**V době vytvoření**

Okruh ExpressRoute bude při vytváření prostředků vykazovat následující stavy.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Když je poskytovatel připojení v procesu zřizování okruhu**

Okruh ExpressRoute bude hlásit následující stavy, zatímco poskytovatel připojení pracuje na zřízení okruhu.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po dokončení procesu zřizování zprostředkovatele připojení**

Okruh ExpressRoute ohlásí následující stavy, jakmile poskytovatel připojení úspěšně zřídí okruh.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Když poskytovatel připojení deprovisioning okruhu**

Pokud je třeba okruh ExpressRoute odložit, okruh ohlásí následující stavy, jakmile poskytovatel služeb dokončí proces zrušení zřízení.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


V případě potřeby jej můžete znovu povolit nebo spustit rutiny prostředí PowerShell, které obvod odstraníte.  

> [!IMPORTANT]
> Okruh nelze odstranit, pokud ServiceProviderProvisioningState je zřizování nebo zřizování. Poskytovatel připojení musí před odstraněním odstranit okruh. Microsoft bude pokračovat v účtování okruhu, dokud není v Azure odstraněn prostředek okruhu ExpressRoute.
> 

## <a name="routing-session-configuration-state"></a>Stav konfigurace relace směrování
Stav zřizování protokolu BGP hlásí, pokud byla na okraji Microsoftu povolena relace protokolu BGP. Stav musí být povolen pro použití soukromého partnerského vztahu nebo partnerského vztahu Microsoft.

Je důležité zkontrolovat stav relace Protokolu BGP, zejména pro partnerský vztah společnosti Microsoft. Kromě stavu zřizování Protokolu BGP existuje jiný stav nazývaný *stav inzerovaných veřejných předpon*. Stav inzerovaných veřejných předpon musí být ve *stavu nakonfigurované,* aby byla relace Protokolu BGP dokončena a směrování fungovalo od konce do konce. 

Pokud je inzerovaný stav veřejné předpony nastaven na *stav potřebný pro ověření,* relace Protokolu BGP není povolena, protože inzerované předpony neodpovídaly číslu AS v žádné z registrů směrování. 

> [!IMPORTANT]
> Pokud je stav inzerovaných veřejných předpon ve stavu *ručního ověřování,* je třeba otevřít lístek podpory s [podporou společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a poskytnout důkaz, že vlastníte inzerované IP adresy spolu s přidruženým číslem autonomního systému.
> 
> 

## <a name="next-steps"></a>Další kroky
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)

