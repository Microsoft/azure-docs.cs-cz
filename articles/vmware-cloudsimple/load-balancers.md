---
title: Řešení Azure VMware (AVS) – volba řešení vyrovnávání zatížení pro privátní cloudy služby AVS
description: Popisuje možnosti vyrovnávání zatížení nasazení aplikace v privátním cloudu služby AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014874"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Volba řešení vyrovnávání zatížení pro privátní cloudy služby AVS

Při nasazování aplikace v privátním cloudu služby AVS můžete zvolit některou z několika možností pro vyrovnávání zatížení.

Můžete zvolit virtuální nebo softwarový nástroj pro vyrovnávání zatížení v privátním cloudu služby AVS nebo dokonce použít nástroj pro vyrovnávání zatížení Azure L7 spuštěný ve vašem předplatném Azure k front-endu virtuálních počítačů webové vrstvy spuštěných v privátním cloudu služby AVS. Tady je seznam několika možností:

## <a name="virtual-load-balancers"></a>Virtuální nástroje pro vyrovnávání zatížení

Virtuální zařízení nástroje pro vyrovnávání zatížení můžete nasadit v prostředí VMware přes rozhraní vCenter a nakonfigurovat je tak, aby byly přenosy aplikace front-endu.

Někteří oblíbená dodavatelé jsou: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-Big-IP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure L7

Pokud používáte Azure Application Gateway jako nástroj pro vyrovnávání zatížení L7 pro vaši aplikaci spuštěnou v privátním cloudu služby AVS, nemusíte spravovat software nástroje pro vyrovnávání zatížení. Software pro vyrovnávání zatížení se spravuje v Azure. Všechny virtuální počítače webové vrstvy v privátním cloudu služby AVS používají privátní IP adresy a k překladu názvů nevyžadují další pravidla překladu adres (NAT) ani veřejné IP adresy. Virtuální počítače webové vrstvy komunikují s Application Gateway Azure přes privátní připojení s nízkou latencí a velkou šířkou pásma.

Další informace o tom, jak nakonfigurovat toto řešení, najdete v Průvodci řešením použití Azure Application Gateway jako nástroje pro vyrovnávání zatížení L7.

## <a name="azure-internal-load-balancer"></a>Interní nástroj pro vyrovnávání zatížení Azure

Pokud se rozhodnete aplikaci spustit v hybridním nasazení, kde webová front-end je spuštěná ve virtuální síti Azure v rámci vašeho předplatného Azure a vrstva databáze aplikace běží na virtuálních počítačích VMware v privátním cloudu služby AVS, můžete použít interní nástroj pro vyrovnávání zatížení Azure (L 4 Nástroj pro vyrovnávání zatížení) před virtuálními počítači vrstvy databáze pro správu provozu.

Další informace najdete v dokumentaci k [interním Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) Azure.

## <a name="global-server-load-balancer"></a>Globální nástroj pro vyrovnávání zatížení serveru

Pokud hledáte Nástroj pro vyrovnávání zatížení založený na DNS, můžete buď využít řešení třetích stran, která jsou k dispozici v Azure Marketplace, nebo přejít s nativním řešením Azure.

Azure Traffic Manager je nástroj pro vyrovnávání zatížení založený na DNS, který umožňuje distribuci provozu optimálně do služeb napříč globálními oblastmi Azure a v místním prostředí a zároveň zajišťuje vysokou dostupnost a rychlost odezvy. Další informace najdete v dokumentaci k Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) .
