---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c025c431d826d3a2951a9eb5c09308695e172887
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170730"
---
| Resource | Výchozí/maximální limit |
| --- | --- |
| Okruhy ExpressRoute podle předplatného |10 |
| Okruhy ExpressRoute na oblast a předplatné, pomocí Azure Resource Manageru |10 |
| Maximální počet tras pro soukromý partnerský vztah Azure pomocí ExpressRoute Standard |4,000 |
| Maximální počet tras pro soukromý partnerský vztah Azure s doplňkem ExpressRoute Premium |10,000 |
| Maximální počet tras pro soukromý partnerský vztah Azure z adresního prostoru virtuální sítě pro připojení k ExpressRoute |200 | 
| Maximální počet směrování pro partnerský vztah Microsoft Azure pomocí ExpressRoute Standard |200 |
| Maximální počet směrování pro partnerský vztah Microsoft Azure s doplňkem ExpressRoute Premium |200 |
| Maximální počet okruhů ExpressRoute propojené ke stejné virtuální síti ve stejném umístění partnerského vztahu |4 |
| Maximální počet okruhů ExpressRoute, které jsou propojeny do stejné virtuální sítě v různých umístění partnerského vztahu |4 |
| Počet propojení virtuální sítě povolené jeden okruh ExpressRoute |V následující tabulce. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Počet virtuálních sítí na jeden okruh ExpressRoute
| **Velikosti okruhu** | **Počet propojení virtuální sítě pro úroveň Standard** | **Počet propojení virtuální sítě s doplňkem Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

* Pouze přímé ExpressRoute
