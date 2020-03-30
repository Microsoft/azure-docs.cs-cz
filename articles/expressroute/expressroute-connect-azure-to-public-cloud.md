---
title: Připojení Azure k veřejným cloudům | Dokumenty společnosti Microsoft
description: Popis různých způsobů připojení Azure k jiným veřejným cloudům
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889630"
---
# <a name="connecting-azure-with-public-clouds"></a>Propojení Azure s veřejnými cloudy

Mnoho podniků sleduje strategii s více cloudy kvůli obchodním a technickým cílům. Patří mezi ně náklady, flexibilita, dostupnost funkcí, redundance, suverenita dat atd. Tato strategie jim pomáhá využít to nejlepší z obou cloudů. 

Tento přístup také představuje výzvy pro podnik, pokud jde o síťovou architekturu a architekturu aplikací. Některé z těchto problémů jsou latence a propustnost dat. K řešení těchto problémů se zákazníci chtějí připojit přímo k více cloudům. Někteří poskytovatelé služeb poskytují řešení pro připojení více poskytovatelů cloudu pro zákazníky. V ostatních případech může zákazník nasadit vlastní směrovač pro připojení více veřejných cloudů.
## <a name="connectivity-via-expressroute"></a>Připojení přes ExpressRoute
ExpressRoute umožňuje zákazníkům rozšířit své místní sítě do cloudu Microsoftu prostřednictvím privátního připojení, které usnadňuje poskytovatel připojení. Pomocí expressroute mohou zákazníci navázat připojení ke cloudovým službám Microsoftu.

Existují tři způsoby, jak se připojit přes ExpressRoute.

1. Zprostředkovatel vrstvy3
2. Zprostředkovatel vrstvy2
3. Přímé připojení

### <a name="layer3-provider"></a>Zprostředkovatel vrstvy3

Poskytovatelé Layer3 jsou běžně známí jako ip VPN nebo MPLS VPN zprostředkovatelé. Zákazníci využívají tyto poskytovatele pro vícebodové připojení mezi svými datovými centry, pobočkami a cloudem. Zákazníci se připojují k poskytovateli L3 prostřednictvím protokolu BGP nebo prostřednictvím statické výchozí trasy. Poskytovatel služeb inzeruje trasy mezi zákaznickými weby, datovými centry a veřejným cloudem. 
 
Při připojování prostřednictvím zprostředkovatele Layer3 bude společnost Microsoft inzerovat trasy virtuální sítě zákazníků poskytovateli služeb přes protokol BGP. Zprostředkovatel může mít dvě různé implementace.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Poskytovatel může být přistání každého poskytovatele cloudu v samostatném VRF, pokud provoz ze všech poskytovatelů cloudu dosáhne na směrovačzákazníka. Pokud zákazník používá protokol BGP s poskytovatelem služeb, budou tyto trasy ve výchozím nastavení znovu inzerovány jiným poskytovatelům cloudu. 

Pokud poskytovatel služeb přistane se všemi poskytovateli cloudu ve stejném VRF, budou trasy inzerovány přímo jiným poskytovatelům cloudu od poskytovatele služeb. To předpokládá standardní operaci Protokolu BGP, kde jsou ve výchozím nastavení inzerovány trasy eBGP ostatním sousedům eBGP.

Každý veřejný cloud má jiný limit předpony, takže při distribuci tras by měl poskytovatel služeb při distribuci tras opatrnosti.

### <a name="layer2-provider-and-direct-connection"></a>Zprostředkovatel vrstvy2 a přímé připojení

Přestože fyzické připojení v obou modelech se liší, ale na vrstvě3 BGP je stanovena přímo mezi MSEE a směrovač zákazníka. Pro ExpressRoute Direct se zákazník připojuje přímo k MSEE. Zatímco v případě Layer2, poskytovatel služeb rozšiřuje VLAN z prostor zákazníka do cloudu. Zákazníci spouštějí protokol BGP v horní části sítě layer2 a připojují své řadiče domény ke cloudu.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
V obou případech bude mít zákazník připojení bod k bodu ke každému z veřejných cloudů. Zákazník vytvoří samostatné připojení Protokolu BGP ke každému veřejnému cloudu. Trasy přijaté jedním poskytovatelem cloudu budou ve výchozím nastavení inzerovány jinému poskytovateli cloudu. Každý poskytovatel cloudu má jiný limit předpony, takže při inzerci tras by se zákazník měl o tyto limity postarat. Zákazník může používat obvyklé knoflíky BGP se společností Microsoft při inzerci tras z jiných veřejných cloudů.

## <a name="direct-connection-with-expressroute"></a>Přímé spojení s ExpressRoute

Zákazníci se mohou rozhodnout připojit ExpressRoute přímo k nabídce přímého připojení poskytovatele cloudu. Dva poskytovatelé cloudu budou připojeni zády k závaru a BGP bude zřízenpřímo mezi jejich směrovači. Tento typ připojení je dnes k dispozici u společnosti Oracle.

## <a name="site-to-site-vpn"></a>Site-to-site VPN

Zákazníci můžou využít internet k propojení svých instancí v Azure s jinými veřejnými cloudy. Téměř všichni poskytovatelé cloudu nabízejí možnosti VPN site-to-site. Nicméně, tam by mohlo být nekompatibility kvůli nedostatku některých variant. Například někteří poskytovatelé cloudu podporují pouze IKEv1, takže je v tomto cloudu vyžadován koncový bod ukončení SÍTĚ VPN. Pro ty poskytovatele cloudu podporující IKEv2 lze vytvořit přímý tunel mezi bránami VPN u obou poskytovatelů cloudu.

Síť VPN site-to-site se nepovažuje za řešení s vysokou propustností a nízkou latencí. Lze jej však použít jako zálohu fyzického připojení.

## <a name="next-steps"></a>Další kroky
Další otázky týkající se ExpressRoute a připojení k virtuální síti najdete v nejčastějších dotazech k [ExpressRoute.][ER-FAQ]

Připojení mezi Azure a Oracle Cloud emitonuje [přímé připojení mezi Azure][ER-OCI] a Oracle.

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



