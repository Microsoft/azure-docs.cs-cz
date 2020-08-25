---
title: Koncepty – Network vzájemné propojení
description: Přečtěte si o klíčových aspektech a případech použití sítě a vzájemné propojení v řešení Azure VMware.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750561"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Koncepty řešení Azure VMware Preview pro sítě a vzájemné propojení

Řešení Azure VMware (AVS) nabízí prostředí privátního cloudu VMware přístupné pro uživatele a aplikace z místních prostředí a prostředí a prostředků založených na platformě Azure. Připojení poskytují služby, jako je Azure ExpressRoute a VPN. Tyto služby vyžadují pro povolení služeb konkrétní rozsahy síťových adres a porty brány firewall.  

Při nasazení privátního cloudu se vytvoří privátní sítě pro správu, zřizování a vMotion. Používají se pro přístup k systému vCenter a NSX Manageru a vMotion nebo nasazení virtuálních počítačů. Všechny privátní sítě jsou přístupné z virtuální sítě v Azure nebo z místních prostředí. ExpressRoute Global Reach slouží k propojení privátních cloudů s místními prostředími a toto připojení vyžaduje virtuální síť s okruhem ExpressRoute v rámci vašeho předplatného.

Při nasazení privátního cloudu se taky zřídí a poskytuje přístup k Internetu a službám Azure, aby mohly virtuální počítače v produkčních sítích tyto služby spotřebovat.  Ve výchozím nastavení je přístup k Internetu zakázán pro nové privátní cloudy a kdykoli může být povolen nebo zakázán.

Užitečnou perspektivou pro vzájemné propojení je zvážit dva typy implementace privátního cloudu pro funkci AVS:

1. [**Basic Azure jenom vzájemné propojení**](#azure-virtual-network-interconnectivity) umožňuje spravovat a používat privátní cloud jenom s jednou virtuální sítí v Azure. Tato implementace je nejlépe vhodná pro vyhodnocení a implementace služby AVS, které nevyžadují přístup z místních prostředí.

1. [**Úplné místní implementace do privátního cloudu vzájemné propojení**](#on-premises-interconnectivity) rozšiřuje základní implementaci jenom pro Azure tak, aby zahrnovala vzájemné propojení mezi místními a soukromými cloudy služby AVS.
 
V tomto článku se podíváme na několik klíčových konceptů, které zajišťují vytváření sítí a vzájemné propojení, včetně požadavků a omezení. Podrobnější informace také najdete v následujících dvou typech implementací privátního cloudu pro funkci AVS vzájemné propojení. Tento článek poskytuje informace, které potřebujete znát ke konfiguraci sítě pro správnou práci s prostředím AVS.

## <a name="avs-private-cloud-use-cases"></a>Případy použití privátního cloudu pro funkci AVS

Případy použití pro privátní cloudy služby AVS zahrnují:
- Nové úlohy virtuálních počítačů VMware v cloudu
- Zatížení virtuálních počítačů do cloudu (jenom v místním prostředí až po funkci AVS)
- Migrace zatížení virtuálního počítače do cloudu (jenom místní až na službu AVS)
- Zotavení po havárii (AVS do AVS nebo místní pro funkci AVS)
- Spotřeba služeb Azure

> [!TIP]
> Všechny případy použití pro službu AVS jsou povolené s připojením z místního prostředí k privátnímu cloudu.

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

V případě úplného vzájemné propojení do privátního cloudu povolte ExpressRoute Global Reach a pak si vyžádejte autorizační klíč a ID privátního partnerského vztahu pro Global Reach v Azure Portal. Autorizační klíč a ID partnerského vztahu se používají k navázání Global Reach mezi okruhem ExpressRoute v rámci vašeho předplatného a okruhem ExpressRoute pro nový privátní cloud. Po propojení dva okruhy ExpressRoute směrují síťový provoz mezi místními prostředími do privátního cloudu.  Postupy pro vyžádání a používání autorizačního klíče a ID partnerského vztahu najdete v [kurzu vytvoření partnerského vztahu ExpressRoute Global REACH v privátním cloudu](tutorial-expressroute-global-reach-private-cloud.md) .

## <a name="next-steps"></a>Další kroky 

- Přečtěte si další informace o [požadavcích na připojení k síti a o požadavcích](tutorial-network-checklist.md). 
- Přečtěte si o [konceptech úložiště privátního cloudu](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

