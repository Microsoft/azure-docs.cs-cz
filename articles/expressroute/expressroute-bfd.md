---
title: 'Azure ExpressRoute: Konfigurace BFD'
description: Tento článek poskytuje pokyny ke konfiguraci BFD (obousměrného předávání) prostřednictvím privátního partnerského vztahu okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511259"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurace BFD přes ExpressRoute

ExpressRoute podporuje rozpoznávání obousměrného předávání (BFD) jak privátní, tak partnerské vztahy Microsoftu. Pokud povolíte BFD prostřednictvím ExpressRoute, můžete urychlit detekci selhání propojení mezi zařízeními Microsoft Enterprise Edge (MSEE) a směrovači, které váš okruh ExpressRoute získá (CE/PE). ExpressRoute můžete nakonfigurovat na zařízeních pro směrování hraničních zařízení nebo na zařízeních pro směrování partnerských serverů (Pokud jste se se službou pro připojení spravované vrstvy 3). Tento dokument vás provede potřebou BFD a tím, jak povolit BFD přes ExpressRoute.

## <a name="need-for-bfd"></a>Potřeba pro BFD

Následující diagram znázorňuje výhody povolování BFD okruhu ExpressRoute: [![1]][1]

Okruh ExpressRoute můžete povolit buď prostřednictvím připojení vrstvy 2, nebo pomocí spravovaných připojení vrstvy 3. V obou případech platí, že pokud je v cestě připojení ExpressRoute více než jedno zařízení vrstvy 2, zodpovídá vám zjištění selhání propojení v cestě s nadřízenou relací protokolu BGP.

Na zařízeních MSEE jsou Keep-Alive a doba blokování protokolu BGP obvykle nakonfigurovány jako 60 a 180 sekund v uvedeném pořadí. Z tohoto důvodu může trvat až tři minuty, než se odhalí selhání propojení a přepne provoz na alternativní připojení.

Můžete řídit časovače protokolu BGP tak, že na zařízení hraničních partnerských zařízení nakonfigurujete nižší dobu uchování a udržování protokolu BGP. Pokud nejsou časovače protokolu BGP stejné mezi dvěma partnerskými zařízeními, relace protokolu BGP se vytvoří za použití nižší hodnoty času. Udržování připojení protokolem BGP může být nastavené na nižší dobu než tři sekundy a doba blokování, jak je uvedeno na hodnotu 10 sekund. Nastavení velmi agresivního časovače protokolu BGP se ale nedoporučuje, protože protokol je náročný na zpracování.

V tomto scénáři vám může BFD pomáhat. BFD zajišťuje detekci selhání propojení s nízkou zátěží za sekundu v daném časovém intervalu. 


## <a name="enabling-bfd"></a>Povolení BFD

BFD je ve výchozím nastavení nakonfigurována ve všech nově vytvořených rozhraních privátního partnerského vztahu ExpressRoute na směrovači msee. Aby bylo možné povolit BFD, stačí pouze nakonfigurovat BFD na primární i sekundární zařízení. Konfigurace BFD je proces se dvěma kroky. Nakonfigurujete BFD na rozhraní a pak ho propojíte s relací protokolu BGP.

Příklad konfigurace CE/PE (s použitím Cisco IOS XE) je uvedený níže. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Povolení BFD v rámci již existujícího privátního partnerského vztahu; je potřeba resetovat partnerský vztah. Viz [resetování partnerských vztahů ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Vyjednávání časovače BFD

Mezi partnerskými BFDmi, pomalejším ze dvou partnerských uzlů určuje přenosovou rychlost. Intervaly přenosu a příjmu směrovači msee BFD jsou nastavené na 300 milisekund. V některých scénářích může být interval nastavený na vyšší hodnotu 750 milisekund. Konfigurací vyšší hodnoty můžete vynutit, aby tyto intervaly byly delší, ale není možné je zkrátit.

>[!NOTE]
>Pokud jste nakonfigurovali geograficky redundantní okruhy ExpressRoute nebo jste jako zálohu použili připojení VPN typu Site-to-site IPSec. Povolením BFD pomůžete urychlit převzetí služeb při selhání po selhání připojení ExpressRoute. 
>

## <a name="next-steps"></a>Další kroky

Další informace nebo nápovědu najdete na následujících odkazech:

- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD zrychluje čas odpočty selhání propojení" .

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md