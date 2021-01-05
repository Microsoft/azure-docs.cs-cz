---
title: Řešení Azure VMware podle CloudSimple – volba řešení vyrovnávání zatížení pro privátní cloudy CloudSimple
description: Popisuje možnosti vyrovnávání zatížení, které nasazují aplikaci v privátním cloudu.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06d2305c209011e4fb1b8ee05a016d2e15a49833
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898024"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Volba řešení vyrovnávání zatížení pro privátní cloudy CloudSimple

Když nasazujete aplikaci v privátním cloudu CloudSimple, můžete pro vyrovnávání zatížení zvolit některou z několika možností.

Můžete zvolit virtuální nebo softwarový nástroj pro vyrovnávání zatížení ve vašem privátním cloudu CloudSimple nebo dokonce použít nástroj pro vyrovnávání zatížení Azure L7 spuštěný ve vašem předplatném Azure ke front-endu virtuálních počítačů webové vrstvy, které běží v privátním cloudu CloudSimple. Tady je seznam několika možností:

## <a name="virtual-load-balancers"></a>Virtuální nástroje pro vyrovnávání zatížení

Virtuální zařízení nástroje pro vyrovnávání zatížení můžete nasadit v prostředí VMware přes rozhraní vCenter a nakonfigurovat je tak, aby byly přenosy aplikace front-endu.

Někteří oblíbená dodavatelé jsou: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-Big-IP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure L7

Pokud používáte Azure Application Gateway jako nástroj pro vyrovnávání zatížení L7 pro vaši aplikaci spuštěnou v privátním cloudu, nemusíte spravovat software nástroje pro vyrovnávání zatížení. Software pro vyrovnávání zatížení se spravuje v Azure. Všechny virtuální počítače webové vrstvy v privátním cloudu používají privátní IP adresy a k překladu názvů nevyžadují další pravidla překladu adres (NAT) ani veřejné IP adresy. Virtuální počítače webové vrstvy komunikují s Application Gateway Azure přes privátní připojení s nízkou latencí a velkou šířkou pásma.

Další informace o tom, jak nakonfigurovat toto řešení, najdete v Průvodci řešením použití Azure Application Gateway jako nástroje pro vyrovnávání zatížení L7.

## <a name="azure-internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení Azure

Pokud se rozhodnete aplikaci spustit v hybridním nasazení, kde webová front-end je spuštěná ve virtuální síti Azure v rámci vašeho předplatného Azure a vrstva DB aplikace je spuštěná ve virtuálních počítačích VMware v privátním cloudu CloudSimple, můžete před virtuálními počítači vrstvy databáze pro správu provozu použít interní nástroj pro vyrovnávání zatížení (L4 Load Balancer) Azure.

Další informace najdete v dokumentaci k [interním Load Balancer](../load-balancer/components.md#frontend-ip-configurations) Azure.

## <a name="global-server-load-balancer"></a>Globální nástroj pro vyrovnávání zatížení serveru

Pokud hledáte Nástroj pro vyrovnávání zatížení založený na DNS, můžete buď využít řešení třetích stran, která jsou k dispozici v Azure Marketplace, nebo přejít s nativním řešením Azure.

Azure Traffic Manager je nástroj pro vyrovnávání zatížení založený na DNS, který umožňuje distribuci provozu optimálně do služeb napříč globálními oblastmi Azure a v místním prostředí a zároveň zajišťuje vysokou dostupnost a rychlost odezvy. Další informace najdete v dokumentaci k Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) .
