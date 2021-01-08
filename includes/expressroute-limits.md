---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 54306b8bd8718a0a69f5b04ceaab15dff46d21a8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978865"
---
| Prostředek | Omezení |
| --- | --- |
| Okruhy ExpressRoute na předplatné |10 |
| Okruhy ExpressRoute na oblast na jedno předplatné s Azure Resource Manager |10 |
| Maximální počet tras inzerovaných privátním partnerským vztahem Azure s ExpressRoute standardem |4 000 |
| Maximální počet tras inzerovaných privátním partnerským vztahem Azure s doplňkem ExpressRoute Premium |10 000 |
| Maximální počet tras inzerovaných z privátního partnerského vztahu Azure z adresního prostoru virtuální sítě pro připojení ExpressRoute |1000 |
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
