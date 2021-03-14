---
title: Koncepty – Network vzájemné propojení
description: Přečtěte si o klíčových aspektech a případech použití sítě a vzájemné propojení v řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: cd62949c13b1f12e635d8d7bf07518a94c4e8d4b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462575"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Sítě řešení Azure VMware a koncepty vzájemné propojení

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Existují dva způsoby, jak vzájemné propojení v privátním cloudu řešení Azure VMware:

1. [**Basic Azure jenom vzájemné propojení**](#azure-virtual-network-interconnectivity) umožňuje spravovat a používat privátní cloud jenom s jednou virtuální sítí v Azure. Tato implementace je nejlépe vhodná pro vyhodnocení a implementace řešení Azure VMware, které nevyžadují přístup z místních prostředí.

1. [**Úplné místní implementace do privátního cloudu vzájemné propojení**](#on-premises-interconnectivity) rozšiřuje základní implementaci jenom pro Azure tak, aby zahrnovala vzájemné propojení mezi místními a privátními cloudy řešení Azure VMware.
 
V tomto článku se zaměříme na klíčové koncepty, které vytvářejí sítě a vzájemné propojení, včetně požadavků a omezení. Tento článek poskytuje informace, které potřebujete znát ke konfiguraci sítě pro práci s řešením Azure VMware.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Případy použití privátního cloudu řešení Azure VMware

Případy použití pro privátní cloudy řešení Azure VMware zahrnují:
- Nové úlohy virtuálních počítačů VMware v cloudu
- Zatížení virtuálních počítačů do cloudu (jenom pro řešení VMware z místního prostředí do Azure)
- Migrace úloh virtuálních počítačů do cloudu (jenom pro řešení VMware z místního prostředí do Azure)
- Zotavení po havárii (řešení Azure VMware do Azure VMware nebo z místního řešení do Azure VMware)
- Spotřeba služeb Azure

> [!TIP]
> Všechny případy použití pro službu řešení Azure VMware jsou povolené s připojením z místního prostředí k privátnímu cloudu.

## <a name="azure-virtual-network-interconnectivity"></a>Vzájemné propojení virtuální sítě Azure

Virtuální síť Azure můžete propojit s implementací privátního cloudu řešení Azure VMware. Můžete spravovat privátní cloud řešení Azure VMware, využívat úlohy v privátním cloudu a přistupovat k dalším službám Azure.

Následující diagram znázorňuje základní vzájemné propojení sítě, která se vytvořila v době nasazení privátního cloudu. Zobrazuje logické sítě mezi virtuální sítí v Azure a privátním cloudem. Toto připojení se naváže přes back-end ExpressRoute, který je součástí služby řešení Azure VMware. Vzájemné propojení splňuje následující primární případy použití:

- Příchozí přístup k serveru vCenter a správce NSX-T, který je přístupný z virtuálních počítačů ve vašem předplatném Azure.
- Odchozí přístup z virtuálních počítačů v privátním cloudu do služeb Azure.
- Příchozí přístup k úlohám spuštěným v privátním cloudu.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Základní připojení virtuální sítě k privátnímu cloudu" border="false":::

## <a name="on-premises-interconnectivity"></a>Místní vzájemné propojení

V plně propojeném scénáři máte přístup k řešení Azure VMware z virtuálních sítí Azure a místního prostředí. Tato implementace je rozšířením základní implementace popsané v předchozí části. Pro připojení z místního prostředí k privátnímu cloudu řešení Azure VMware v Azure se vyžaduje okruh ExpressRoute.

V následujícím diagramu vidíte místní vzájemné propojení privátního cloudu, které umožňují následující případy použití:

- Horká a studená vMotiona vCenter mezi místním prostředím a řešením Azure VMware.
- Přístup pro správu privátního cloudu z místního prostředí do Azure VMware.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Připojení k virtuální síti a místnímu úplnému privátnímu cloudu" border="false":::

Pokud chcete plně vzájemné propojení do svého privátního cloudu, musíte povolit ExpressRoute Global Reach a potom si vyžádat autorizační klíč a ID privátního partnerského vztahu pro Global Reach v Azure Portal. Autorizační klíč a ID partnerského vztahu se používají k navázání Global Reach mezi okruhem ExpressRoute ve vašem předplatném a okruhem ExpressRoute pro váš privátní cloud. Po propojení dva okruhy ExpressRoute směrují síťový provoz mezi místními prostředími do privátního cloudu. Další informace o těchto postupech najdete v [kurzu vytvoření partnerského vztahu ExpressRoute Global REACH do privátního cloudu](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Omezení
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Další kroky 

Teď, když jste se seznámili s koncepty sítě řešení Azure VMware a vzájemné propojení, se můžete seznámit s těmito informacemi:

- [Koncepty úložiště řešení Azure VMware](concepts-storage.md).
- [Koncepty identity řešení Azure VMware](concepts-identity.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
