---
title: Omezení řešení Azure VMware
description: Omezení řešení VMware Azure.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622290"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Následující tabulka popisuje maximální limity pro řešení Azure VMware.

| **Prostředek** | **Počtu** |
| :-- | :-- |
| Clustery na privátní cloud | 12 |
| Minimální počet uzlů na cluster | 3 |
| Maximální počet uzlů na cluster | 16 |
| Uzly na privátní cloud | 64 |
| vCenter na privátní cloud | 1  |
| Párování webů HCX | 3 s pokročilou edicí, 10 a Enterprise Edition |
| ExpressRoute maximální počet propojených SDDCs ve funkci AVS | 4 |
| ExpressRoute portspeed pro funkci AVS | 10 Gb/s<br />Použitá brána virtuální sítě určuje skutečnou šířku pásma. Další podrobnosti najdete v tématu [informace o branách virtuální sítě ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) . | 
| Veřejné IP adresy vystavené prostřednictvím vWAN | 100 |
| omezení kapacity síti vSAN | 75% z celkového množství použitelného (pro smlouvu SLA je dostupná 25%)  |

U ostatních omezení specifických pro VMware prosím použijte [Nástroj pro konfiguraci VMware pro maximum!](https://configmax.vmware.com/).
