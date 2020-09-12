---
title: Připojení Azure k veřejným cloudům | Microsoft Docs
description: Popisuje různé způsoby, jak připojit Azure k ostatním veřejným cloudům.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: f413b6100fb2a7d1c8bc06d787468cc3e79c0169
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397470"
---
# <a name="connecting-azure-with-public-clouds"></a>Propojení Azure s veřejnými cloudy

Řada podniků provádí strategii pro více cloudů z důvodu obchodních a technických cílů. Mezi ně patří náklady, flexibilita, dostupnost funkcí, redundance, suverenita dat atd. Tato strategie pomáhá využít nejlepší z obou cloudů. 

Tento přístup také představuje problémy pro podnik z pohledu architektury sítě a aplikace. Některé z těchto problémů jsou latencí a propustnost dat. Pro vyřešení těchto výzev se zákazníci chtějí připojit k několika cloudům přímo. Někteří poskytovatelé služeb poskytují řešení pro připojení více poskytovatelů cloudu pro zákazníky. V jiných případech může zákazník nasadit svůj vlastní směrovač pro připojení více veřejných cloudů.
## <a name="connectivity-via-expressroute"></a>Připojení prostřednictvím ExpressRoute
ExpressRoute umožňuje zákazníkům, aby své místní sítě rozšířili do cloudu Microsoftu přes soukromé připojení, které usnadňuje poskytovatel připojení. S ExpressRoute můžou zákazníci navázat připojení ke cloudovým službám Microsoftu.

Existují tři způsoby, jak se připojit přes ExpressRoute.

1. Poskytovatel Layer3
2. Poskytovatel layer2
3. Přímé připojení

### <a name="layer3-provider"></a>Poskytovatel Layer3

Poskytovatelé Layer3 se běžně označují jako poskytovatelé IP adres VPN nebo MPLS VPN. Zákazníci využívají tyto poskytovatele pro připojení k systému MultiPoint mezi datovými centry, pobočkami a cloudem. Zákazníci se připojují k poskytovateli L3 prostřednictvím protokolu BGP nebo prostřednictvím statické výchozí trasy. Poskytovatel služeb inzeruje trasy mezi zákaznickými weby, datacentry a veřejným cloudem. 
 
Při připojování prostřednictvím poskytovatele Layer3 společnost Microsoft prostřednictvím protokolu BGP inzeruje trasy virtuální sítě zákazníka poskytovateli služeb. Poskytovatel může mít dvě různé implementace.

![Diagram, který zobrazuje poskytovatele Layer3](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Poskytovatel může vyčlenit každého poskytovatele cloudu v samostatném VRF, pokud přenosy ze všech poskytovatelů cloudu budou dostupné ve směrovači zákazníka. Pokud zákazník používá protokol BGP s poskytovatelem služeb, pak se tyto trasy ve výchozím nastavení znovu inzerují jiným poskytovatelům cloudu. 

Pokud poskytovatel služeb vychází ze všech poskytovatelů cloudu ve stejném VRF, trasy budou od poskytovatele služeb inzerovány přímo poskytovatelům cloudu. To se předpokládá jako standardní operace protokolu BGP, kde jsou trasy eBGP inzerovány jiným sousedním sousedům eBGP ve výchozím nastavení.

V každém veřejném cloudu je limit prefixu jiný, takže při distribuci tras by měl dohlížet i poskytovatel služeb směrování.

### <a name="layer2-provider-and-direct-connection"></a>Poskytovatel layer2 a přímé připojení

I když se fyzické připojení v obou modelech liší, ale v Layer3 BGP se naváže přímo mezi MSEE a směrovačem zákazníka. Pro přímé zákazníky ExpressRoute se připojuje přímo k MSEE. V případě layer2 poskytovatel služeb rozšiřuje síť VLAN od zákaznických objektů do cloudu. Zákazníci spouštějí protokol BGP na layer2 síti pro připojení svých řadičů domény ke cloudu.
![Diagram, který zobrazuje poskytovatele layer2 a přímé připojení.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
V obou případech bude mít zákazník připojení typu Point-to-Point ke každému veřejnému cloudu. Zákazník vytvoří samostatné připojení protokolu BGP ke každému veřejnému cloudu. Trasy přijaté jedním poskytovatelem cloudu budou ve výchozím nastavení inzerovány jinému poskytovateli cloudu. Každý poskytovatel cloudu má jiný limit předpon, takže během inzerce by zákazník měli tyto limity postarat. Zákazník může při inzerci tras z jiných veřejných cloudů použít obvyklé ovladače protokolu BGP u Microsoftu.

## <a name="direct-connection-with-expressroute"></a>Přímé připojení pomocí ExpressRoute

Zákazníci se můžou rozhodnout připojit ExpressRoute přímo k nabídce přímého připojení poskytovatele cloudu. Dva poskytovatelé cloudu budou připojeni zpátky a protokol BGP se naváže přímo mezi svými směrovači. Tento typ připojení je k dispozici pro Oracle ještě dnes.

## <a name="site-to-site-vpn"></a>Site-to-site VPN

Zákazníci můžou využít Internet k připojení svých instancí v Azure k ostatním veřejným cloudům. Téměř všichni poskytovatelé cloudu nabízejí možnosti sítě VPN typu Site-to-site. Z důvodu nedostatku určitých variant ale může dojít k nekompatibilitě. Například někteří poskytovatelé cloudu podporují jenom IKEv1, takže v tomto cloudu je vyžadován koncový bod ukončení sítě VPN. Pro cloudové poskytovatele podporující IKEv2 se dá vytvořit přímé tunelové propojení mezi bránami VPN v obou poskytovatelích cloudu.

Síť Site-to-Site VPN není považována za vysoce propustnost a řešení s nízkou latencí. Dá se ale použít jako záložní pro fyzické připojení.

## <a name="next-steps"></a>Další kroky
Další otázky k připojení ExpressRoute a virtuální sítě najdete v tématu [ExpressRoute – nejčastější][ER-FAQ] dotazy.

Další informace najdete v tématu [Nastavení přímého připojení mezi Azure a Oracle cloudem][ER-OCI] pro připojení mezi Azure a Oracle.

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



