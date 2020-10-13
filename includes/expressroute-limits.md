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
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147818"
---
| Prostředek | Omezení |
| --- | --- |
| Okruhy ExpressRoute na předplatné |10 |
| Okruhy ExpressRoute na oblast na jedno předplatné s Azure Resource Manager |10 |
| Maximální počet tras inzerovaných privátním partnerským vztahem Azure s ExpressRoute standardem |4 000 |
| Maximální počet tras inzerovaných privátním partnerským vztahem Azure s doplňkem ExpressRoute Premium |10 000 |
| Maximální počet tras inzerovaných z privátního partnerského vztahu Azure z adresního prostoru virtuální sítě pro připojení ExpressRoute |200 |
| Maximální počet tras inzerovaných pro partnerský vztah Microsoftu s ExpressRoute standardem |200 |
| Maximální počet tras inzerovaných pro partnerský vztah Microsoftu s doplňkem ExpressRoute Premium |200 |
| Maximální počet okruhů ExpressRoute propojených se stejnou virtuální sítí ve stejném umístění partnerského vztahu |4 |
| Maximální počet okruhů ExpressRoute propojených se stejnou virtuální sítí v různých umístěních partnerských vztahů |4 |
| Počet povolených propojení virtuálních sítí na okruh ExpressRoute |Podívejte se na [počet virtuálních sítí na jednu tabulku okruhů ExpressRoute](#vnetpercircuit) .  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Počet virtuálních sítí na okruh ExpressRoute
| **Velikost okruhu** | **Počet odkazů virtuální sítě pro Standard** | **Počet propojení virtuálních sítí pomocí doplňku Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

**100 GB/s ExpressRoute jenom pro přímý přenos*

> [!NOTE]
> Global Reach připojení se počítají na základě limitu připojení k virtuální síti na okruh ExpressRoute. Například okruh Premium s rychlostí 10 GB/s by umožňoval připojení 5 Global Reach a 95 připojení k bráně ExpressRoute nebo 95 Global Reach připojení a 5 připojení ke ExpressRoute bránám nebo jakékoli jiné kombinaci až po limit 100 připojení pro okruh.
