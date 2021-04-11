---
title: 'Azure ExpressRoute: pracovní postup konfigurace okruhu'
description: Tato stránka zobrazuje pracovní postup pro konfiguraci okruhů ExpressRoute a partnerských vztahů.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721731"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Pracovní postupy ExpressRoute pro zřizování a stavy okruhů

Tento článek vás provede jednotlivými pracovními postupy pro zřizování a konfiguraci směrování na vysoké úrovni. V následujících částech najdete přehled úloh pro komplexní a ExpressRoute okruh.

## <a name="workflow-steps"></a>Kroky pracovního postupu

### <a name="1-prerequisites"></a>1. požadavky

Zajistěte splnění požadavků. Úplný seznam najdete v tématu [požadavky a kontrolní seznam](expressroute-prerequisites.md).

* Bylo vytvořeno předplatné Azure.
* Bylo navázáno fyzické připojení s partnerem ExpressRoute nebo nakonfigurované prostřednictvím ExpressRoute Direct. Zkontrolovat umístění, podívejte se na [umístění a partneři](expressroute-locations-providers.md#partners) , kde můžete zobrazit partnera ExpressRoute a přímé připojení ExpressRoute mezi umístěními partnerských vztahů.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. Přizpůsobte si připojení nebo nakonfigurujte ExpressRoute Direct

Přizpůsobte připojení od poskytovatele služeb nebo nakonfigurujte ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>Model partnera ExpressRoute

Objednat připojení od poskytovatele služeb. Tento proces se liší. Podrobnější informace o tom, jak objednat připojení, získáte od svého poskytovatele připojení.

* Vybrat partnera ExpressRoute
* Vyberte umístění partnerského vztahu.
* Vyberte šířku pásma.
* Výběr modelu fakturace
* Vybrat doplněk Standard nebo Premium

#### <a name="expressroute-direct-model"></a>ExpressRoute přímý model

* Zobrazte dostupnou ExpressRoute přímou kapacitu napříč partnerskými umístěními.
* Vyhradit porty vytvořením prostředku ExpressRoute Direct v předplatném Azure.
* Vyžádat a přijmout schválení a objednat fyzické vzájemné připojení od poskytovatele umístění partnerského vztahu.
* Povolit stav správce a zobrazit úrovně světla a fyzické propojení pomocí [Azure monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics).

### <a name="3-create-an-expressroute-circuit"></a>3. vytvoření okruhu ExpressRoute

#### <a name="expressroute-partner-model"></a>Model partnera ExpressRoute

Ověřte, že je partner ExpressRoute připravený zřídit připojení. Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. K vytvoření okruhu použijte pokyny v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) .

#### <a name="expressroute-direct-model"></a>ExpressRoute přímý model

Ujistěte se, že je fyzický odkaz a stav správce povolený v obou odkazech. Pokyny najdete v tématu [Konfigurace ExpressRoute s přímým přístupem](how-to-expressroute-direct-portal.md) . Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. K vytvoření okruhu použijte pokyny v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) .

### <a name="4-service-provider-provisions-connectivity"></a>4. služba Service Provider zřizuje připojení

Tato část se vztahuje jenom na model připojení partner ExpressRoute:

* Zadejte klíč služby (s-Key) k poskytovateli připojení.
* Zadejte další informace vyžadované poskytovatelem připojení (například VPN ID).
* Pokud zprostředkovatel spravuje konfiguraci směrování, zadejte potřebné podrobnosti.

Úspěšné zřízení okruhu můžete zajistit tak, že ověříte stav zřizování okruhu ExpressRoute pomocí PowerShellu, Azure Portal nebo CLI.

### <a name="5-configure-routing-domains"></a>5. konfigurace domén směrování

Nakonfigurujte domény směrování. Pokud poskytovatel připojení spravuje konfiguraci vrstvy 3, nakonfiguruje směrování pro váš okruh. Pokud poskytovatel připojení nabízí jenom služby úrovně 2 nebo pokud používáte ExpressRoute Direct, musíte nakonfigurovat směrování podle pokynů popsaných v článcích [požadavky směrování](expressroute-routing.md) a [Konfigurace směrování](expressroute-howto-routing-classic.md) .

#### <a name="for-azure-private-peering"></a>Pro soukromý partnerský vztah Azure

Povolte privátní partnerské vztahy pro připojení k virtuálním počítačům a cloudovým službám nasazeným v rámci služby Azure Virtual Network.

* Podsítě IPv4:
    * Podsíť partnerského vztahu pro cestu 1 (/30)
    * Podsíť partnerského vztahu pro cestu 2 (/30)
* Podsítě IPv6 (volitelné):
    * Podsíť partnerského vztahu pro cestu 1 (/126)
    * Podsíť partnerského vztahu pro cestu 2 (/126)
* ID sítě VLAN pro partnerský vztah
* ASN pro partnerský vztah
* ExpressRoute ASN = 12076
* Hash MD5 (volitelné)

#### <a name="for-microsoft-peering"></a>Pro partnerský vztah Microsoftu

Tuto možnost povolte pro přístup k Microsoft online služby, jako je například Microsoft 365. Kromě toho jsou všechny služby Azure PaaS přístupné prostřednictvím partnerského vztahu Microsoftu. Abyste se mohli připojit k Microsoftu, než je ten, který používáte pro Internet, musíte zajistit, abyste použili samostatný proxy server nebo Edge. Použití stejné hrany pro ExpressRoute i Internet způsobí asymetrické směrování a způsobí výpadky připojení pro vaši síť.

* Podsítě IPv4:
    * Podsíť partnerského vztahu pro cestu 1 (/30) – musí být veřejná IP adresa.
    * Podsíť partnerského vztahu pro cestu 2 (/30) – musí být veřejná IP adresa.
* Podsítě IPv6 (volitelné):
    * Podsíť partnerského vztahu pro cestu 1 (/126) – musí být veřejná IP adresa.
    * Podsíť partnerského vztahu pro cestu 2 (/126) – musí být veřejná IP adresa.
* ID sítě VLAN pro partnerský vztah
* ASN pro partnerský vztah
* Inzerované předpony – musí být předpony veřejných IP adres.
* ASN zákazníka (volitelné, pokud se liší od ASN partnerského vztahu)
* RIR/IRR pro ověření IP a ASN
* ExpressRoute ASN = 12076
* Hash MD5 (volitelné)

### <a name="6-start-using-the-expressroute-circuit"></a>6. Začněte používat okruh ExpressRoute

* Virtuální sítě Azure můžete propojit se svým okruhem ExpressRoute a umožnit tak připojení z místního prostředí k virtuální síti Azure. Pokyny najdete v článku [propojení virtuální sítě s okruhem](expressroute-howto-linkvnet-arm.md) . Tyto virtuální sítě můžou být buď ve stejném předplatném Azure jako okruh ExpressRoute, nebo můžou být v jiném předplatném.
* Připojte se ke službám Azure a cloudovým službám Microsoftu prostřednictvím partnerského vztahu Microsoftu.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Stavy zřizování partnerského okruhu ExpressRoute

Následující část popisuje různé stavy okruhů ExpressRoute pro model připojení partner ExpressRoute.
Každý partnerský okruh ExpressRoute má dva stavy:

* **ServiceProviderProvisioningState** představuje stav na straně poskytovatele připojení. Může to být buď *NotProvisioned*, *zřizování*, nebo *zřízené*. Aby bylo možné konfigurovat partnerský vztah, musí být okruh ExpressRoute ve zřízeném stavu. **Tento stav se vztahuje pouze na partnerské okruhy ExpressRoute a není zobrazen ve vlastnostech okruhu ExpressRoute Direct**.

* **Stav** představuje stav zřizování společnosti Microsoft. Tato vlastnost je nastavená na povoleno při vytváření okruhu ExpressRoute.

### <a name="possible-states-of-an-expressroute-circuit"></a>Možné stavy okruhu ExpressRoute

Tato část popisuje možné stavy okruhu ExpressRoute vytvořeného v rámci modelu připojení partner ExpressRoute.

**V okamžiku vytvoření**

Okruh ExpressRoute bude při vytváření prostředku hlásit následující stavy.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Když poskytovatel připojení zaznamená proces zřizování okruhu**

Okruh ExpressRoute oznámí následující stavy, zatímco poskytovatel připojení pracuje na zřízení okruhu.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Když poskytovatel připojení dokončí proces zajišťování**

Po úspěšném zřízení okruhu poskytovatel připojení oznámí následující stavy ExpressRoute.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Když poskytovatel připojení ruší zřízení okruhu**

Pokud je potřeba zrušit zřízení okruhu ExpressRoute, okruh oznámí následující stavy, jakmile poskytovatel služby dokončí proces zrušení zřízení.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

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
