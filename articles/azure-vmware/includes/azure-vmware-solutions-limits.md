---
title: Omezení řešení Azure VMware
description: Omezení řešení VMware Azure.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582564"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Následující tabulka popisuje maximální limity pro řešení Azure VMware.

| **Prostředek** | **Počtu** |
| :-- | :-- |
| Clustery na privátní cloud | 12 |
| Minimální počet uzlů na cluster | 3 |
| Maximální počet uzlů na cluster | 16 |
| Uzly na privátní cloud | 96 |
| vCenter na privátní cloud | 1  |
| Párování webů HCX | 3 s pokročilou edicí, 10 a Enterprise Edition |
| ExpressRoute maximální počet propojených SDDCs ve funkci AVS | 4 |
| ExpressRoute portspeed pro funkci AVS | 10 Gb/s<br />Použitá brána virtuální sítě určuje skutečnou šířku pásma. Další podrobnosti najdete v tématu [informace o branách virtuální sítě ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) . | 
| Veřejné IP adresy vystavené prostřednictvím vWAN | 100 |
| omezení kapacity síti vSAN | 75% z celkového množství použitelného (pro smlouvu SLA je dostupná 25%)  |

U ostatních omezení specifických pro VMware prosím použijte [Nástroj pro konfiguraci VMware pro maximum!](https://configmax.vmware.com/).
