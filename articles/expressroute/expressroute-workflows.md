---
title: 'Konfigurace – pracovní postupy ExpressRoute okruhů: Azure | Dokumentace Microsoftu'
description: Tato stránka zobrazuje pracovní postupy pro konfiguraci okruh ExpressRoute a partnerské vztahy
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ffcc5ac2193e607573ceb93717258f5349d1f15
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101683"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování a stavy okruhů
Tato stránka vás provede službu zřizování a pracovní postupy konfigurace směrování na vysoké úrovni.

![pracovní postup okruh](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Následující obrázek a příslušné postupy ukazují úlohy, které je třeba dodržovat, aby měla okruhu ExpressRoute zřízeného začátku do konce. 

1. Nakonfigurujte okruh ExpressRoute pomocí prostředí PowerShell. Postupujte podle pokynů [okruhy ExpressRoute vytvořit](expressroute-howto-circuit-classic.md) , kde najdete další podrobnosti.
2. Připojení k pořadí od poskytovatele služeb. Tento proces se liší. Obraťte se na svého poskytovatele připojení, další podrobnosti o tom, jak uspořádat připojení.
3. Ujistěte se, že okruh se úspěšně zřízený ověřením přes PowerShell Stav zřizování okruhu ExpressRoute. 
4. Konfigurace domény směrování. Pokud poskytovatel připojení vrstvy 3 spravuje za vás, nakonfiguruje směrování pro okruh. Pokud poskytovatel připojení nabízí jenom služby vrstvy 2, je nutné nakonfigurovat směrování podle pokynů popsaných v [požadavky směrování](expressroute-routing.md) a [konfigurace směrování](expressroute-howto-routing-classic.md) stránky.
   
   * Povolení soukromého partnerského vztahu Azure – povolit tento partnerský vztah pro připojení k virtuálním počítačům nebo službám nasazeným v rámci virtuální sítě v cloudu.

   * Povolit vytvoření partnerského vztahu Microsoft - povolit přístup k Office 365 a Dynamics 365. Kromě toho všechny služby Azure PaaS jsou přístupné prostřednictvím partnerského vztahu Microsoftu.
     
     > [!IMPORTANT]
     > Ujistěte se, že budete používat samostatný proxy server / edge pro připojení k Microsoft než ten, který můžete použít pro Internet. Pomocí stejného edge pro ExpressRoute a Internetem způsobí asymetrického směrování a způsobí výpadek připojení pro vaši síť.
     > 
     > 
     
     ![směrování pracovních postupů](./media/expressroute-workflows/routing-workflow.png)
5. Propojení virtuálních sítí s okruhy ExpressRoute – můžete propojit virtuální sítě pro váš okruh ExpressRoute. Postupujte podle pokynů [k propojení virtuálních sítí](expressroute-howto-linkvnet-arm.md) pro váš okruh. Tyto virtuální sítě může být ve stejném předplatném Azure jako okruh ExpressRoute, nebo může být v jiném předplatném.

## <a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování okruhu ExpressRoute
Každý okruh ExpressRoute má dva stavy:

* Stav zřizování poskytovatele služby
* Status

Stav představuje stav zřizování společnosti Microsoft. Tato vlastnost nastavena na hodnotu povoleno vytvoření okruhu Expressroute

Stav zřizování poskytovatele připojení představuje stav na straně poskytovatele připojení. Může jí být buď *NotProvisioned*, *zřizování*, nebo *zřízená*. Okruh ExpressRoute musí být ve stavu zřízená pro vás bude moct používat.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute
Tato část obsahuje seznam si možné stavy pro okruh ExpressRoute.

**V okamžiku vytvoření**

Okruh ExpressRoute v následujícím stavu se zobrazí, co nejdříve spusťte rutinu prostředí PowerShell k vytvoření okruhu ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Pokud poskytovatel připojení probíhá zřizování okruhu**

Okruh ExpressRoute v následujícím stavu se zobrazí, jakmile předáte klíč služby poskytovatele připojení a nebudou spuštěny během procesu zřizování.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Po dokončení procesu zřizování poskytovatele připojení**

Okruh ExpressRoute v následujícím stavu se zobrazí, jakmile zprostředkovatele připojení k dokončení procesu zřizování.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Zřízení a je povoleno pouze stav okruhu lze v pro vás bude moct používat. Pokud používáte poskytovatele vrstvy 2, můžete nakonfigurovat směrování pro váš okruh, pouze pokud je v tomto stavu.

**Pokud poskytovatel připojení ruší zřízení okruhu**

Pokud jste požádali o poskytovateli služeb zrušit zřízení okruhu ExpressRoute, zobrazí se okruh, nastavte na následující po dokončení procesu zrušení zřízení poskytovatele služeb.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Můžete ji znovu povolit Pokud potřeby, nebo spusťte rutiny prostředí PowerShell se odstranit okruh.  

> [!IMPORTANT]
> Pokud spustíte rutinu Powershellu a odstranění okruhu při zřizování serviceproviderprovisioningstate vzájemného propojení nebo zřízená se operace nezdaří. Prosím spolupracovat se svým poskytovatelem připojení pro nejprve zrušit zřízení okruhu ExpressRoute a pak odstranit okruh. Microsoft nadále fakturovat okruh, dokud nespustíte rutina prostředí PowerShell pro odstranění okruhu.
> 
> 

## <a name="routing-session-configuration-state"></a>Stav konfigurace směrování relace
Protokol BGP, Stav zřizování vám umožňuje vědět, pokud relace protokolu BGP je povolen v Microsoft edgi. Stav musí být povoleny, abyste mohli používat, partnerský vztah.

Je důležité zkontrolovat stav relace protokolu BGP zejména pro partnerský vztah Microsoftu. Kromě protokolu BGP Stav zřizování je jiný stav, volá *inzerované veřejné prefixy stavu*. Stav inzerované veřejné předpony musí být v *nakonfigurované* state, jak pro relace protokolu BGP dá zvládnout a směrování fungovat začátku do konce. 

Pokud státu inzerované veřejné předpony je nastavená na *vyžaduje se ověření* stavu relace protokolu BGP není povolená, protože předpony inzerované neodpovídá číslo AS v některém z registrech směrování. 

> [!IMPORTANT]
> Je-li stav inzerované veřejné předpony v *ruční ověření* stavu, je nutné otevřít lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a poskytnout důkazy, které vlastníte, IP adresy inzerované spolu s přidružené číslo autonomního systému.
> 
> 

## <a name="next-steps"></a>Další postup
* Nakonfigurujte připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)

