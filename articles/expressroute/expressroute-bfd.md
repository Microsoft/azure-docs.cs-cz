---
title: 'Azure ExpressRoute: Konfigurace BFD'
description: Tento článek poskytuje pokyny ke konfiguraci BFD (obousměrného předávání) prostřednictvím privátního partnerského vztahu okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: duau
ms.openlocfilehash: fd1cad4031d83fd0e17286bfaabb77aa746b646a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202323"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurace BFD přes ExpressRoute

ExpressRoute podporuje rozpoznávání obousměrného předávání (BFD) jak privátní, tak partnerské vztahy Microsoftu. Povolením BFD over ExpressRoute můžete urychlit propojení mezi zařízeními Microsoft Enterprise Edge (MSEE) a směrovači, na kterých jste ukončili okruh ExpressRoute (CE/PE). Můžete ukončit ExpressRoute zařízení pro směrování hraničních zařízení nebo zařízení pro směrování hraničního partnera (Pokud jste přešli se službou připojení ke spravované vrstvě 3). Tento dokument vás provede potřebou BFD a tím, jak povolit BFD přes ExpressRoute.

## <a name="need-for-bfd"></a>Potřeba pro BFD

Následující diagram znázorňuje výhody povolování BFD okruhu ExpressRoute: [![1]][1]

Okruh ExpressRoute můžete povolit buď prostřednictvím připojení vrstvy 2, nebo pomocí spravovaných připojení vrstvy 3. V obou případech platí, že pokud se v cestě připojení ExpressRoute nachází jedno nebo více zařízení vrstvy 2, zodpovídáte za detekci selhání propojení v cestě k překrývající se protokolu BGP.

V zařízeních MSEE se doba uchovávání protokolu BGP a doba uchování obvykle konfigurují jako 60 a 180 sekund v uvedeném pořadí. Proto po selhání propojení může trvat až tři minuty pro zjištění selhání propojení a přepnutí provozu na alternativní připojení.

Můžete řídit časovače protokolu BGP tak, že na zařízení hraničního vztahu zákazníka nakonfigurujete nižší dobu uchování a dobu uchovávání protokolu BGP. Pokud se mezi dvěma partnerskými zařízeními neshodují časovače protokolu BGP, bude relace protokolu BGP mezi partnerskými uzly používat nižší hodnotu časovače. Nastavení naživu protokolu BGP se dá nastavit tak, aby bylo nízké jako tři sekundy, a za čas v řádu desítk sekund. Nastavení časovačů protokolu BGP je ale méně žádoucí, protože protokol je náročný na zpracování.

V tomto scénáři vám může BFD pomáhat. BFD zajišťuje detekci selhání propojení s nízkou zátěží za sekundu v daném časovém intervalu. 


## <a name="enabling-bfd"></a>Povolení BFD

BFD je ve výchozím nastavení nakonfigurována ve všech nově vytvořených rozhraních privátního partnerského vztahu ExpressRoute na směrovači msee. Aby bylo možné povolit BFD, je třeba pouze nakonfigurovat BFD na vašem zařízení pro zápis a zápis na úrovni služby (na primární i sekundární zařízení). Konfigurace BFD proces se dvěma kroky: musíte nakonfigurovat BFD na rozhraní a pak ho propojit s relací protokolu BGP.

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

Mezi partnerskými BFDmi, pomalejším ze dvou partnerských uzlů určuje přenosovou rychlost. Intervaly přenosu a příjmu směrovači msee BFD jsou nastavené na 300 milisekund. V některých scénářích může být interval nastavený na vyšší hodnotu 750 milisekund. Konfigurací vyšších hodnot můžete vynutit, aby tyto intervaly byly delší; ale ne kratší.

>[!NOTE]
>Pokud jste nakonfigurovali geograficky redundantní okruhy ExpressRoute nebo jako zálohu používáte připojení VPN typu Site-to-site IPSec, povolením BFD pomůžete urychlit převzetí služeb při selhání po selhání připojení ExpressRoute. 
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