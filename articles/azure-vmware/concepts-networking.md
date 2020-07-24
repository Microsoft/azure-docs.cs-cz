---
title: Koncepty – Network vzájemné propojení
description: Seznamte se s klíčovými aspekty a případy použití sítě a vzájemné propojení v řešení Azure VMware (AVS).
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c0416da9c745ccf92970ff39f623a782d5784983
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062843"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution (AVS) Preview sítě a vzájemné propojení koncepty

Vzájemné propojení sítě mezi privátními a místními cloudy Azure VMware Solution (AVS) a místními prostředími nebo virtuálními sítěmi v Azure vám umožní přístup k privátnímu cloudu a jeho používání. V tomto článku se podíváme na několik klíčových konceptů, které zajišťují základ sítí a vzájemné propojení.

Užitečnou perspektivou pro vzájemné propojení je zvážit dva typy implementace privátního cloudu pro funkci AVS:

1. [**Basic Azure jenom vzájemné propojení**](#azure-virtual-network-interconnectivity) umožňuje spravovat a používat privátní cloud jenom s jednou virtuální sítí v Azure. Tato implementace je nejlépe vhodná pro vyhodnocení a implementace služby AVS, které nevyžadují přístup z místních prostředí.

1. [**Úplné místní implementace do privátního cloudu vzájemné propojení**](#on-premises-interconnectivity) rozšiřuje základní implementaci jenom pro Azure tak, aby zahrnovala vzájemné propojení mezi místními a soukromými cloudy služby AVS.
 
Další informace o požadavcích a dvou typech implementací privátního cloudu pro funkci AVS najdete v následujících částech.

## <a name="avs-private-cloud-use-cases"></a>Případy použití privátního cloudu pro funkci AVS

Případy použití pro privátní cloudy služby AVS zahrnují:
- nové úlohy virtuálních počítačů VMware v cloudu
- Zatížení virtuálních počítačů do cloudu (jenom v místním prostředí až po funkci AVS)
- Migrace zatížení virtuálního počítače do cloudu (jenom místní až na službu AVS)
- zotavení po havárii (AVS do AVS nebo místní pro funkci AVS)
- spotřeba služeb Azure

 Všechny případy použití pro službu AVS jsou povolené s připojením z místního prostředí k privátnímu cloudu. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Požadavky na virtuální síť a okruh ExpressRoute
 
Když ve svém předplatném vytvoříte připojení z virtuální sítě, okruh ExpressRoute se vytvoří prostřednictvím partnerského vztahu, použije autorizační klíč a ID partnerského vztahu, které požadujete v Azure Portal. Partnerský vztah je privátní a jedno připojení mezi Vaším privátním cloudem a virtuální sítí.

> [!NOTE] 
> Okruh ExpressRoute není součástí nasazení privátního cloudu. Místní okruh ExpressRoute překračuje rozsah tohoto dokumentu. Pokud vyžadujete místní připojení k privátnímu cloudu, můžete použít některý ze stávajících okruhů ExpressRoute nebo si ho koupit v Azure Portal.

Při nasazení privátního cloudu obdržíte IP adresy pro vCenter a správce NSX-T. Pro přístup k těmto rozhraním pro správu budete muset ve svém předplatném vytvořit další prostředky ve virtuální síti. Můžete najít postupy pro vytváření těchto prostředků a vytváření privátních partnerských vztahů ExpressRoute v kurzech.

Logické sítě privátního cloudu jsou dodávány s předem zřízeným NSX-T. Pro vás předběžně zřídí bránu 0 a bránu 1. Můžete vytvořit segment a připojit ho k existující bráně 1-1 nebo ji připojit k nové bráně, kterou definujete. Logické síťové komponenty NSX-T poskytují konektivitu mezi úlohami v severozápadním a v západním rozsahu a také poskytují připojení k Internetu a službám Azure v oblasti USA – jih. 

## <a name="routing-and-subnet-requirements"></a>Požadavky na směrování a podsíť

Směrování je založené na protokolu BGP (Routing Border Gateway Protocol), které se ve výchozím nastavení automaticky zřídí a povoluje pro každé nasazení privátního cloudu. U privátních cloudů služby AVS se vyžaduje plánování adresních prostorů sítě privátního cloudu s minimální délkou bloků síťových adres CIDR, které jsou v níže uvedené tabulce. Blok adres by neměl překrývat bloky adres používané v jiných virtuálních sítích, které jsou ve vašem předplatném a v místních sítích. V rámci tohoto bloku adres se automaticky zřídí Správa, zřizování a vMotion sítě.

Příklad `/22` bloku síťových adres CIDR:`10.10.0.0/22`

Podsítě:

| Využití sítě             | Podsíť | Příklad        |
| ------------------------- | ------ | -------------- |
| Správa privátního cloudu  | `/24`  | `10.10.0.0/24` |
| vMotion síť           | `/24`  | `10.10.1.0/24` |
| Úlohy virtuálních počítačů              | `/24`  | `10.10.2.0/24` |
| Partnerský vztah ExpressRoute      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Vzájemné propojení virtuální sítě Azure

V rámci virtuální sítě do implementace privátního cloudu můžete spravovat svůj privátní cloud služby AVS, využívat úlohy v privátním cloudu a přistupovat ke službám Azure přes připojení ExpressRoute. 

Následující diagram znázorňuje základní vzájemné propojení sítě, která se vytvořila v době nasazení privátního cloudu. Zobrazuje logické sítě založené na ExpressRoute mezi virtuální sítí v Azure a privátním cloudem. Vzájemné propojení splňuje tři z hlavních případů použití:
* Příchozí přístup k serveru vCenter a správce NSX-T, který je přístupný z virtuálních počítačů ve vašem předplatném Azure, a ne z vašich místních systémů. 
* Odchozí přístup z virtuálních počítačů do služeb Azure. 
* Příchozí přístup a spotřeba úloh využívajících privátní cloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Základní připojení virtuální sítě k privátnímu cloudu" border="false":::

## <a name="on-premises-interconnectivity"></a>Místní vzájemné propojení

V rámci virtuální sítě a do úplného nasazení privátního cloudu máte přístup k privátním cloudům služby AVS z místních prostředí. Tato implementace je rozšířením základní implementace popsané v předchozí části. Podobně jako u základní implementace se vyžaduje okruh ExpressRoute, ale s touto implementací se používá pro připojení z místních prostředí k privátnímu cloudu v Azure. 

V následujícím diagramu vidíte místní vzájemné propojení privátního cloudu, které umožňují následující případy použití:
* Horká a studená vMotiona přes vCenter
* Místní pro přístup ke správě privátního cloudu pro funkci AVS

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Připojení k virtuální síti a místnímu úplnému privátnímu cloudu" border="false":::

V případě úplného vzájemné propojení do privátního cloudu povolte ExpressRoute Global Reach a pak si vyžádejte autorizační klíč a ID privátního partnerského vztahu pro Global Reach v Azure Portal. Autorizační klíč a ID partnerského vztahu se používají k navázání Global Reach mezi okruhem ExpressRoute v rámci vašeho předplatného a okruhem ExpressRoute pro nový privátní cloud. Po propojení dva okruhy ExpressRoute směrují síťový provoz mezi místními prostředími do privátního cloudu.  Postupy pro vyžádání a používání autorizačního klíče a ID partnerského vztahu najdete v [kurzu vytvoření partnerského vztahu ExpressRoute Global REACH v privátním cloudu](tutorial-expressroute-global-reach-private-cloud.md) .


## <a name="next-steps"></a>Další kroky 

V dalším kroku se dozvíte o [konceptech úložiště privátního cloudu](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
