---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622090"
---
| Resource | Výchozí/maximální limit |
| --- | --- |
| Okruhy ExpressRoute podle předplatného |10 |
| Okruhy ExpressRoute na oblast a předplatné, pomocí Azure Resource Manageru |10 |
| Maximální počet trasy inzerované do soukromého partnerského vztahu Azure pomocí ExpressRoute Standard |4,000 |
| Maximální počet trasy inzerované na soukromý partnerský vztah Azure s doplňkem ExpressRoute Premium |10,000 |
| Maximální počet trasy inzerované soukromého partnerského vztahu Azure z adresního prostoru virtuální sítě pro připojení k ExpressRoute |200 |
| Maximální počet trasy inzerované na Microsoft partnerský vztah ExpressRoute Standard |200 |
| Maximální počet trasy inzerované na Microsoft partnerský vztah s doplňkem ExpressRoute Premium |200 |
| Maximální počet okruhů ExpressRoute propojené ke stejné virtuální síti ve stejném umístění partnerského vztahu |4 |
| Maximální počet okruhů ExpressRoute, které jsou propojeny do stejné virtuální sítě v různých umístění partnerského vztahu |4 |
| Počet propojení virtuální sítě povolené jeden okruh ExpressRoute |Zobrazit [počet virtuálních sítí na jeden okruh ExpressRoute](#vnetpercircuit) tabulky.  |

#### <a name="vnetpercircuit"></a> Počet virtuálních sítí na jeden okruh ExpressRoute
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

**Pouze přímé ExpressRoute 100 GB/s*
