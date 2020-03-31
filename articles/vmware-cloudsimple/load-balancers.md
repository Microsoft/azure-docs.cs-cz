---
title: Řešení Azure VMware od CloudSimple – zvolte řešení vyrovnávání zatížení pro privátní cloudové cloudy
description: Popisuje možnosti vyrovnávání zatížení nasazení aplikace v privátním cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014874"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Zvolte řešení vyrovnávání zatížení pro cloudová privátní cloudová cloudová cloudová cloudová řešení

Při nasazování aplikace v cloudu CloudSimple Privátní cloud, můžete zvolit některou z několika možností pro vyrovnávání zatížení.

Můžete si vybrat virtuální nebo softwarově založené nástroje pro vyrovnávání zatížení ve vašem cloudu CloudSimple nebo dokonce použít Nástroj pro vyrovnávání zatížení Azure L7 spuštěný ve vašem předplatném Azure k frontendu virtuálních virtuálních aplikací webové úrovně spuštěných v privátním cloudu CloudSimple. Zde uvádíme několik možností:

## <a name="virtual-load-balancers"></a>Virtuální nástroje pro vyrovnávání zatížení

Můžete nasadit virtuální zařízení pro vyrovnávání zatížení ve vašem prostředí VMware prostřednictvím rozhraní vCenter a nakonfigurovat je tak, aby front-end provozu aplikace.

Některé populární prodejci jsou: http://nginx.org/en/docs/http/load_balancing.html NginX: F5- https://www.f5.com/products/big-ip-services/virtual-editions BigIP - Traffic Manager: Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 pro vyrovnávání zatížení

Když používáte Azure Application Gateway jako L7 nástroje pro vyrovnávání zatížení pro vaši aplikaci spuštěnou v privátním cloudu, nemusíte spravovat software nástroje pro vyrovnávání zatížení. Software nástroje pro vyrovnávání zatížení spravuje Azure. Všechny virtuální servery webové vrstvy v privátním cloudu používají privátní IP adresy a k překladu názvů nevyžadují další pravidla PŘEKLADU ani veřejné adresy IP adres. Virtuální počítače webové vrstvy komunikují s aplikační bránou Azure přes privátní připojení s nízkou latencí a vysokou šířkou pásma.

Další informace o tom, jak nakonfigurovat toto řešení, najdete v průvodci řešení na použití brány aplikace Azure jako L7 vyrovnávání zatížení.

## <a name="azure-internal-load-balancer"></a>Interní systém pro vyrovnávání zatížení Azure

Pokud se rozhodnete spustit aplikaci v hybridním nasazení, kde webová front-endová vrstva běží v rámci virtuální sítě Azure ve vašem předplatném Azure a úroveň DB aplikace běží ve virtuálních počítačích VMware v cloudu CloudSimple Private Cloud, můžete použít interní zatížení Azure vykladač (L4 balancer) před virtuálními počítači úrovně DB pro správu provozu.

Další informace najdete v tématu Dokumentace [k nástroje pro interní vyrovnávání zatížení](../load-balancer/concepts-limitations.md#internalloadbalancer) Azure.

## <a name="global-server-load-balancer"></a>Globální vyrovnávání zatížení serveru

Pokud hledáte nástroje pro vyrovnávání zatížení založeného na DNS, můžete buď použít řešení třetích stran dostupná na Azure Marketplace, nebo použít nativní řešení Azure.

Azure Traffic Manager je nástroj pro vyrovnávání zatížení založený na DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure a místně a zároveň poskytuje vysokou dostupnost a odezvu. Další informace najdete v dokumentaci k Azure [Traffic Manageru.](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)
