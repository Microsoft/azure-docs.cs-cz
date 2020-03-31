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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335109"
---
| Prostředek | Omezení |
| --- | --- |
| Okruhy ExpressRoute na předplatné |10 |
| Okruhy ExpressRoute na oblast na předplatné, pomocí Azure Resource Manageru |10 |
| Maximální počet tras inzerovaných do privátního partnerského vztahu Azure se standardem ExpressRoute Standard |4 000 |
| Maximální počet tras inzerovaných do privátního partnerského vztahu Azure s doplňkem ExpressRoute Premium |10 000 |
| Maximální počet tras inzerovaných z privátního partnerského vztahu Azure z adresního prostoru virtuální sítě pro připojení ExpressRoute |200 |
| Maximální počet tras inzerovaných pro partnerský vztah Microsoftu pomocí služby ExpressRoute Standard |200 |
| Maximální počet tras inzerovaných do partnerského vztahu Microsoftu s doplňkem ExpressRoute Premium |200 |
| Maximální počet okruhů ExpressRoute propojených se stejnou virtuální sítí ve stejném umístění partnerského vztahu |4 |
| Maximální počet okruhů ExpressRoute propojených se stejnou virtuální sítí v různých umístěních partnerského vztahu |4 |
| Počet povolených propojení virtuální sítě na okruh ExpressRoute |Podívejte se na počet virtuálních sítí na tabulku [okruhů ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Počet virtuálních sítí na okruh ExpressRoute
| **Velikost obvodu** | **Počet propojení virtuálních sítí pro Standard** | **Počet propojení virtuálních sítí s doplňkem Premium** |
| --- | --- | --- |
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gb/s |10 |60 |
| 5 Gb/s |10 |75 |
| 10 Gb/s |10 |100 |
| 40 Gb/s* |10 |100 |
| 100 Gb/s* |10 |100 |

**Pouze 100 Gb/s ExpressRoute*

> [!NOTE]
> Připojení globálního dosahu se započítávají do limitu připojení virtuálnísítě na okruh ExpressRoute. Například prémiový okruh o rychlosti 10 Gb/s by umožňoval 5 připojení Global Reach a 95 připojení k bránám ExpressRoute nebo 95 připojení global reach a 5 připojení k bráně ExpressRoute nebo jiné kombinaci až do limitu 100 připojení. pro obvod.
