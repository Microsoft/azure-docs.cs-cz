---
title: Co je Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Přehled funkcí, architektury a implementace Azure Load Balanceru. Zjistěte, jak funguje balancer a jak ho používat v cloudu.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 8596b435ffa02da7daf4ef98bfe0fe7995b9270a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768190"
---
# <a name="what-is-azure-load-balancer"></a>Co je Azure Load Balancer?

*Vyrovnávání zatížení* označuje rovnoměrné rozdělení zatížení (příchozí síťový provoz) mezi skupinou prostředků back-endu nebo serverů. 

Azure Load Balancer pracuje na úrovni čtyři modelu Open Systems Interconnection (OSI). Je to jediné kontaktní místo pro klienty. Balancer na vytížení distribuuje příchozí toky, které dorazí na front-end u vyrovnávání zatížení do back-endového fondu instancí. Tyto toky jsou podle nakonfigurovaných pravidel vyrovnávání zatížení a sond stavu. Back-endové instance fondu mohou být virtuální počítače Azure nebo instance ve škálovací sadě virtuálních počítačů.

**[Veřejný nástroj pro vyrovnávání zatížení](./concepts-limitations.md#publicloadbalancer)** může poskytovat odchozí připojení pro virtuální počítače (VM) uvnitř vaší virtuální sítě. Tato připojení jsou provedena překladem jejich privátní IP adresy na veřejné IP adresy. Veřejné vyrovnávání zatížení se používají k vyrovnávání zatížení internetového provozu na virtuální počítače.

**[Interní (nebo soukromé) vyrovnávání zatížení](./concepts-limitations.md#internalloadbalancer)** se používá, kde soukromé IP adresy jsou potřeba pouze v front-endu. Interní nástroje pro vyrovnávání zatížení se používají k vyrovnávání zatížení provozu uvnitř virtuální sítě. Front-end vykladače vyrovnávání zatížení lze přistupovat z místní sítě v hybridním scénáři.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Obrázek: Vyladění vícevrstvých aplikací pomocí veřejného i interního vykladače zatížení*

Další informace o jednotlivých součástech vyrovnávání zatížení najdete v [tématu součásti a omezení azure balancer](./concepts-limitations.md)

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud potřebujete vysoce výkonné vyrovnávání zatížení vrstvy 7 s nízkou latencí, přečtěte si část [Co je to aplikační brána Azure?](../application-gateway/overview.md) Pokud hledáte globální vyrovnávání zatížení DNS, přečtěte si část [Co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Kombinace těchto řešení může mít prospěch z jejich komplexních scénářů.
>
> Porovnání možností vyrovnávání zatížení Azure najdete [v tématu Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-load-balancer"></a>Proč používat Azure Load Balancer?
Se standardním balancerem zatížení můžete škálovat své aplikace a vytvářet vysoce dostupné služby. Vyrovnávání zatížení podporuje příchozí i odchozí scénáře. Vyrovnávání zatížení poskytuje nízkou latenci a vysokou propustnost a škáluje až miliony toků pro všechny aplikace TCP a UDP.

Mezi klíčové scénáře, které můžete provést pomocí standardního vykladače zatížení, patří:

- **[Vnitřní](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** a **[externí](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** provoz na virtuálních počítačích Azure pro vyrovnávání zatížení.

- Zvyšte dostupnost distribucí prostředků **[v rámci](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** a **[mezi](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zónami.

- Konfigurace **[odchozího připojení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** pro virtuální počítače Azure.

- Pomocí **[sond stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** můžete sledovat prostředky s vyrovnáváním zatížení.

- Pomocí **[předávání portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** pro přístup k virtuálním počítačům ve virtuální síti podle veřejné IP adresy a portu.

- Povolte podporu pro **[vyrovnávání zatížení](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** **[iPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- Standardní vyrovnávání zatížení poskytuje vícerozměrné metriky prostřednictvím [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Tyto metriky lze filtrovat, seskupit a rozdělit pro danou dimenzi.  Poskytují aktuální a historické pohledy na výkon a zdraví vašich služeb.  Zdroj zdraví je také podporována. Další podrobnosti naleznete v **[diagnostice standardního nástroje pro vyrovnávání zatížení.](load-balancer-standard-diagnostics.md)**

- Služby vyrovnávání zatížení na **[více portech, více IP adresách nebo obojí](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**.

- Přesuňte **[interní](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** a **[externí](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** prostředky pro vyrovnávání zatížení napříč oblastmi Azure.

- Tok tcp a UDP na všech portech současně pomocí **[portů HA](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** na všech portech tcp a UDP .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Zabezpečení ve výchozím nastavení

Standardní vyrovnávání zatížení je postavenna na modelu zabezpečení sítě s nulovým vztahem důvěryhodnosti v jeho jádru. Standardní nástroj pro vyrovnávání zatížení je ve výchozím nastavení zabezpečený a je součástí vaší virtuální sítě. Virtuální síť je privátní a izolované sítě.  To znamená, že standardní vyrovnávání zatížení a standardní veřejné IP adresy jsou uzavřeny pro příchozí toky, pokud nejsou otevřeny skupinami zabezpečení sítě. NsG se používají k výslovnému povolení provozu.  Pokud nemáte skupinu sítě sítě v podsíti nebo síťové karty prostředku virtuálního počítače, provoz není povoleno dosáhnout tohoto prostředku. Další informace o skupinách zabezpečení sítě a jejich použití pro váš scénář naleznete v [tématu Skupiny zabezpečení sítě](../virtual-network/security-overview.md).
Základní vyrovnávání zatížení je ve výchozím nastavení otevřeno pro internet.


## <a name="pricing-and-sla"></a>Ceny a SLA

Informace o cenách standardního vyvažovače zatížení naleznete v tématu [Ceny vykladačů zatížení](https://azure.microsoft.com/pricing/details/load-balancer/).
Load Balancer úrovně Basic se nabízí zdarma.
Viz [SLA pro vyrovnávání zatížení](https://aka.ms/lbsla). Základní vyrovnávání zatížení nemá žádnou sla.

## <a name="next-steps"></a>Další kroky
Viz [Upgrade základního vykladače zatížení](upgrade-basic-standard.md) a upgrade základního vykladače zatížení na standardní vyvažovač evidenční stav zatížení.

Informace o [vytváření standardního standardního vyvažovače zatížení](quickstart-load-balancer-standard-public-portal.md) najdete v tématu Vytvoření veřejného standardního vyvažovače zatížení.

Další informace o omezeních a součástech Azure Load Balancer najdete v tématu [Koncepty a omezení Azure Load Balancer](./concepts-limitations.md)
