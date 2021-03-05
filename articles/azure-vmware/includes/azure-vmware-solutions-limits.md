---
title: Omezení řešení Azure VMware
description: Omezení řešení VMware Azure.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193597"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Následující tabulka popisuje maximální limity pro řešení Azure VMware.

| **Prostředek** | **Počtu** |
| :-- | :-- |
| Clustery na privátní cloud | 4 |
| Minimální počet uzlů na cluster | 3 |
| Maximální počet uzlů na cluster | 16 |
| Uzly na privátní cloud | 64 |
| vCenter na privátní cloud | 1  |
| Párování webů HCX | 3 s pokročilou edicí, 10 a Enterprise Edition |
| ExpressRoute maximální počet propojených SDDCs ve funkci AVS | 4 |
| ExpressRoute portspeed pro funkci AVS | 10 Gb/s | 
| Veřejné IP adresy vystavené prostřednictvím vWAN | 100 |
| omezení kapacity síti vSAN | 75% z celkového množství použitelného (pro smlouvu SLA je dostupná 25%)  |

U ostatních omezení specifických pro VMware prosím použijte [Nástroj pro konfiguraci VMware pro maximum!](https://configmax.vmware.com/).
