---
title: Architektura sítě Azure
description: Tento článek poskytuje obecný popis Microsoft Azure sítě infrastruktury.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567859"
---
# <a name="azure-network-architecture"></a>Architektura sítě Azure
Architektura sítě Azure poskytuje připojení z Internetu k datacentrům Azure. Všechna nasazená zatížení (IaaS, PaaS a SaaS) v Azure využívají síť Azure Datacenter.

## <a name="network-topology"></a>Síťová topologie
Architektura sítě datového centra Azure se skládá z následujících součástí:

- Hraniční síť
- Síť WAN
- Síť regionálních bran
- Síť datacenter

![Diagram sítě Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Komponenty sítě
Stručný popis síťových součástí.

- Hraniční síť

   - Bod odpálenosti mezi sítěmi Microsoftu a jinými sítěmi (například Internet, podniková síť)
   - Poskytuje partnerský vztah k Internetu a [ExpressRoute](../../expressroute/expressroute-introduction.md) do Azure.

- Síť WAN

   - Inteligentní páteřní síť Microsoftu zahrnující glóbus
   - Poskytuje připojení mezi [oblastmi Azure](https://azure.microsoft.com/global-infrastructure/geographies/) .

- Místní brána

   - Bod agregace pro všechna datová centra v oblasti Azure
   - Poskytuje rozsáhlé možnosti připojení mezi datovými centry v oblasti Azure (například více než stovky terabits na datové centrum).

- Síť datacenter

   - Poskytuje připojení mezi servery v rámci datového centra s nízkou úrovní pásma, které se odebírá.

Výše uvedené síťové součásti jsou navržené tak, aby poskytovaly maximální dostupnost pro podporu vždy dostupného cloudového podnikání. Redundance je navržená a integrovaná do sítě od fyzického aspektu až po řízení protokolu.

## <a name="datacenter-network-resiliency"></a>Odolnost sítě Datacenter
Podívejme se na princip návrhu odolnosti pomocí sítě Datacenter.

Síť datacenter je upravená verze [sítí Clos sítě](https://en.wikipedia.org/wiki/Clos_network), která poskytuje vysokou mezigeografickou šířku pásma pro přenosy v cloudovém měřítku. Síť je vytvořená pomocí velkého počtu komoditních zařízení, aby se snížil dopad způsobený selháním jednotlivých hardwaru. Tato zařízení jsou strategicky umístěná v různých fyzických umístěních se samostatnou elektrickou a chladicí doménou, aby se snížil dopad události prostředí.  Na rovině ovládacího prvku jsou všechna síťová zařízení spuštěná jako režim směrování vrstvy modelů OSI 3, což eliminuje historický problém s cyklem provozu. Všechny cesty mezi různými úrovněmi jsou aktivní, aby poskytovaly vysokou redundanci a šířku pásma pomocí směrování Equal-Cost ECMP (multi-Path).

Následující diagram znázorňuje, že síť datacenter je vytvořená různými úrovněmi síťových zařízení. Pruhy v diagramu představují skupiny síťových zařízení, které poskytují redundanci a vysokou šířku pásma připojení.

![Síť datacenter](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
