---
title: Koncepty – Network vzájemné propojení
description: Přečtěte si o klíčových aspektech a případech použití sítě a vzájemné propojení v řešení Azure VMware.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 9369c0fff31de4c6575d8e4903420cfa01461bf4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062116"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Sítě řešení Azure VMware a koncepty vzájemné propojení

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Užitečnou perspektivou pro vzájemné propojení je zvážit dva typy implementace privátního cloudu řešení Azure VMware:

1. [**Basic Azure jenom vzájemné propojení**](#azure-virtual-network-interconnectivity) umožňuje spravovat a používat privátní cloud jenom s jednou virtuální sítí v Azure. Tato implementace je nejlépe vhodná pro vyhodnocení a implementace řešení Azure VMware, které nevyžadují přístup z místních prostředí.

1. [**Úplné místní implementace do privátního cloudu vzájemné propojení**](#on-premises-interconnectivity) rozšiřuje základní implementaci jenom pro Azure tak, aby zahrnovala vzájemné propojení mezi místními a privátními cloudy řešení Azure VMware.
 
V tomto článku se podíváme na několik klíčových konceptů, které zajišťují vytváření sítí a vzájemné propojení, včetně požadavků a omezení. Podrobnější informace také najdete v těchto dvou typech implementace privátního cloudu řešení Azure VMware vzájemné propojení. V tomto článku najdete informace, které potřebujete znát ke správnému fungování sítě pro práci s řešeními Azure VMware.

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

V rámci virtuální sítě do implementace privátního cloudu můžete spravovat privátní cloud řešení Azure VMware, využívat úlohy v privátním cloudu a přistupovat ke službám Azure přes připojení ExpressRoute. 

Následující diagram znázorňuje základní vzájemné propojení sítě, která se vytvořila v době nasazení privátního cloudu. Zobrazuje logické sítě založené na ExpressRoute mezi virtuální sítí v Azure a privátním cloudem. Vzájemné propojení splňuje tři z hlavních případů použití:
* Příchozí přístup k serveru vCenter a správce NSX-T, který je přístupný z virtuálních počítačů ve vašem předplatném Azure, a ne z vašich místních systémů. 
* Odchozí přístup z virtuálních počítačů do služeb Azure. 
* Příchozí přístup a spotřeba úloh využívajících privátní cloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Základní připojení virtuální sítě k privátnímu cloudu" border="false":::

## <a name="on-premises-interconnectivity"></a>Místní vzájemné propojení

V rámci virtuální sítě a do úplného nasazení privátního cloudu můžete přistupovat k privátním cloudům řešení Azure VMware z místních prostředí. Tato implementace je rozšířením základní implementace popsané v předchozí části. Podobně jako u základní implementace se vyžaduje okruh ExpressRoute, ale s touto implementací se používá pro připojení z místních prostředí k privátnímu cloudu v Azure. 

V následujícím diagramu vidíte místní vzájemné propojení privátního cloudu, které umožňují následující případy použití:
* Horká a studená vMotiona přes vCenter
* Přístup ke správě privátního cloudu z místního prostředí do Azure VMware

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Připojení k virtuální síti a místnímu úplnému privátnímu cloudu" border="false":::

V případě úplného vzájemné propojení do privátního cloudu povolte ExpressRoute Global Reach a pak si vyžádejte autorizační klíč a ID privátního partnerského vztahu pro Global Reach v Azure Portal. Autorizační klíč a ID partnerského vztahu se používají k navázání Global Reach mezi okruhem ExpressRoute v rámci vašeho předplatného a okruhem ExpressRoute pro nový privátní cloud. Po propojení dva okruhy ExpressRoute směrují síťový provoz mezi místními prostředími do privátního cloudu.  Další informace o postupech vyžádání a používání autorizačního klíče a ID partnerského vztahu najdete v [kurzu Vytvoření ExpressRoute Global REACH partnerských vztahů k privátnímu cloudu](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="next-steps"></a>Další kroky 
Přečtěte si o [konceptech úložiště privátního cloudu](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

