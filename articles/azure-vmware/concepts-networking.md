---
title: Koncepty – Network vzájemné propojení
description: Seznamte se s klíčovými aspekty a případy použití sítě a vzájemné propojení v řešení Azure VMware (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629212"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware Solution (AVS) Preview sítě a vzájemné propojení koncepty

Vzájemné propojení sítě mezi privátními cloudy Azure VMware (AVS) a místními prostředími nebo virtuálními sítěmi v Azure umožňuje přístup k privátnímu cloudu a jeho používání. V tomto článku jsou popsány některé klíčové koncepty sítě a vzájemné propojení, které tvoří základ pro vzájemné propojení.

Užitečnou perspektivou pro vzájemné propojení je zvážit dva typy implementace privátního cloudu služby AVS: implementace se základními vzájemné propojení pouze pro Azure a implementacemi s úplným místním prostředím a privátním cloudem vzájemné propojení.

Případy použití pro privátní cloudy služby AVS zahrnují:
- nové úlohy virtuálních počítačů VMware v cloudu
- Zatížení virtuálních počítačů do cloudu
- Migrace úloh virtuálních počítačů do cloudu
- zotavení po havárii
- spotřeba služeb Azure

 Všechny případy použití pro službu AVS jsou povolené s připojením z místního prostředí k privátnímu cloudu. Základní model vzájemné propojení se nejlépe hodí pro hodnocení a implementace služby AVS, které nevyžadují přístup z místních prostředí.

V následujících částech jsou popsány dva typy vzájemné propojení privátního cloudu pro funkci AVS.  Největší základní vzájemné propojení je "připojení k virtuální síti Azure"; umožňuje spravovat a používat privátní cloud jenom s jednou virtuální sítí v Azure. Vzájemné propojení popsané v části "místní připojení" rozšiřuje připojení k virtuální síti, a zahrnuje taky vzájemné propojení mezi místními prostředími a privátními cloudy služby AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Vzájemné propojení virtuální sítě Azure

Základní vzájemné propojení sítě, která je vytvořená v době nasazení privátního cloudu, se zobrazí v následujícím diagramu. Zobrazuje logické sítě založené na ExpressRoute mezi virtuální sítí v Azure a privátním cloudem. Vzájemné propojení splňuje tři z hlavních případů použití:
- Příchozí přístup k sítím pro správu, kde se nachází Server vCenter a správce NSX-T.
    - Dostupné z virtuálních počítačů v rámci předplatného Azure, nikoli z vašich místních systémů.
- Odchozí přístup z virtuálních počítačů do služeb Azure.
- Příchozí přístup a spotřeba úloh využívajících privátní cloud.

![Základní připojení virtuální sítě k privátnímu cloudu](./media/concepts/adjacency-overview-drawing-single.png)

Okruh ExpressRoute v této virtuální síti ke scénáři privátního cloudu se naváže při vytváření připojení z virtuální sítě ve vašem předplatném k okruhu ExpressRoute vašeho privátního cloudu. Partnerský vztah používá autorizační klíč a ID okruhu, které požadujete v Azure Portal. Připojení ExpressRoute vytvořené prostřednictvím partnerského vztahu je privátní připojení typu 1:1 mezi Vaším privátním cloudem a virtuální sítí. Můžete spravovat svůj privátní cloud, využívat úlohy v privátním cloudu a přistupovat ke službám Azure přes toto připojení ExpressRoute.

Při nasazení privátního cloudu služby AVS se vyžaduje jeden adresní prostor privátní sítě v/22. Tento adresní prostor se nesmí překrývat s adresními prostory používanými v jiných virtuálních sítích v rámci vašeho předplatného. V rámci tohoto adresního prostoru se sítě pro správu, zřizování a vMotion zřídí automaticky. Směrování je založené na protokolu BGP a ve výchozím nastavení je automaticky zřízené a povolené pro každé nasazení privátního cloudu.

Po nasazení privátního cloudu máte k dispozici IP adresy pro vCenter a správce NSX-T. Pro přístup k těmto rozhraním pro správu vytvoříte ve virtuální síti ve svém předplatném další prostředky. Postupy pro vytváření těchto prostředků a vytvoření privátního partnerského vztahu ExpressRoute jsou k dispozici v kurzech.

Budete navrhovat logické sítě privátního cloudu a implementujete je pomocí NSX-T. K privátnímu cloudu se dodává předem zřízené NSX-T. Brána-0 & brány – jedna brána je předem zřízená pro vás. Můžete vytvořit segment a připojit ho k existující bráně úrovně 1 nebo připojit k nové bráně 1, kterou můžete definovat. Logické síťové komponenty NSX-T poskytují konektivitu mezi úlohami v severozápadním a v západním rozsahu a také poskytují připojení k Internetu a službám Azure v oblasti USA – jih. 

## <a name="on-premises-interconnectivity"></a>Místní vzájemné propojení

K privátním cloudům služby AVS se taky můžete připojit k místním prostředím. Tento typ vzájemné propojení je rozšíření základního vzájemné propojení popsané v předchozí části.

![připojení k virtuální síti a místnímu úplnému privátnímu cloudu](./media/concepts/adjacency-overview-drawing-double.png)

K zajištění úplného vzájemné propojení privátního cloudu použijete Azure Portal k povolení ExpressRoute Global Reach mezi privátním cloudovým okruhem ExpressRoute a místním okruhem ExpressRoute. Tato konfigurace rozšiřuje základní připojení tak, aby zahrnovalo přístup k privátním cloudům z místních prostředí.

Pro připojení z místního prostředí k privátnímu cloudu v Azure se vyžaduje místní okruh ExpressRoute virtuální sítě do Azure. Tento okruh ExpressRoute je ve vašem předplatném a není součástí nasazení privátního cloudu. Místní okruh ExpressRoute překračuje rozsah tohoto dokumentu. Pokud vyžadujete místní připojení k privátnímu cloudu, můžete použít některý ze stávajících okruhů ExpressRoute nebo si ho koupit v Azure Portal.

Po propojení s Global Reach budou dva okruhy ExpressRoute směrovat síťový provoz mezi místními prostředími a Vaším privátním cloudem. V předchozím diagramu se zobrazí vzájemné propojení z místního prostředí do privátního cloudu. Vzájemné propojení reprezentovaný v diagramu umožňuje následující případy použití:

- Horká a studená vMotiona přes vCenter
- Místní přístup ke správě privátního cloudu z místního prostředí

Pokud chcete povolit úplné připojení, můžete v Azure Portal požádat o autorizační klíč a ID privátního partnerského vztahu pro Global Reach. Klíč a ID slouží k navázání Global Reach mezi okruhem ExpressRoute v předplatném a okruhem ExpressRoute pro váš nový privátní cloud. [Kurz pro vytvoření privátního cloudu](tutorial-create-private-cloud.md) vám poskytne postupy pro vyžádání a používání klíče a ID.

Požadavky na směrování řešení vyžadují, abyste naplánovali adresní prostory sítě privátního cloudu, abyste se vyhnuli překrytí s ostatními virtuálními sítěmi a místními sítěmi. Privátní cloudy služby AVS vyžadují minimálně `/22` blok síťových adres CIDR pro podsítě, které jsou uvedené níže. Tato síť doplňuje vaše místní sítě. Aby bylo možné se připojit k místním prostředím a virtuálním sítím, musí se jednat o blok síťových adres, který se nepřekrývá.

Příklad `/22` bloku síťových adres CIDR:`10.10.0.0/22`

Podsítě:

| Využití sítě             | Podsíť | Příklad        |
| ------------------------- | ------ | -------------- |
| Správa privátního cloudu            | `/24`    | `10.10.0.0/24`   |
| vMotion síť       | `/24`    | `10.10.1.0/24`   |
| Úlohy virtuálních počítačů | `/24`   | `10.10.2.0/24`   |
| Partnerský vztah ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Další kroky 

V dalším kroku se dozvíte o [konceptech úložiště privátního cloudu](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

