---
title: 'Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Analýza RP roviny dat | Dokumentace Microsoftu'
description: Tento článek obsahuje analýza roviny dat z nastavení testu, které lze použít k analýze vzájemná funkční spolupráce mezi ExpressRoute, site-to-site VPN a virtuální síť vytvoření partnerského vztahu v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e648c1f1b051d7b65d9b007a69aece2d99d9df4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176178"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Vzájemná funkční spolupráce v Azure připojení k back-end funkce: Analýza roviny dat

Tento článek popisuje analýzu roviny dat [nastavení testu][Setup]. Můžete také zkontrolovat [konfiguraci nastavení testu] [ Configuration] a [analýzy rovina řízení] [ Control-Analysis] nastavení testu.

Analýza roviny dat prozkoumá cestu přijatých paketů, které překračují z jedné místní sítě (LAN nebo virtuální sítě) do jiného v rámci topologie. Není nutně symetrické na cestu k datům mezi dvě místní sítě. Proto v tomto článku budeme analyzovat cestu přesměrování z místní sítě k jiné síti, která je oddělená od Obrátit cestu.

## <a name="data-path-from-the-hub-vnet"></a>Cesta k datům z virtuální síti centra

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Partnerské vztahy virtuálních sítí (VNet) emuluje funkčnost sítě most mezi dvěma virtuálními sítěmi, které jsou v partnerském vztahu. Traceroute výstup z centra virtuální sítě k virtuálnímu počítači v paprsku, virtuální síť je zobrazena zde:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Následující obrázek znázorňuje připojení grafické zobrazení virtuální síti centra a paprsku z hlediska Azure Network Watcher:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Cesta k větev virtuální sítě

Traceroute výstup z centra virtuální sítě k virtuálnímu počítači na větev, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

V tomto traceroute je první segment směrování VPN gateway ve službě Azure VPN Gateway o virtuální síti centra. Druhý segment směrování je brána VPN větve virtuální sítě. IP adresa brány VPN větve virtuální sítě se neinzeruje ve virtuální síti centra. Třetí segment směrování je virtuální počítač na větev virtuální sítě.

Následující obrázek znázorňuje připojení grafické zobrazení virtuální síti centra a větve virtuální sítě z perspektivy sledovacího procesu sítě:

[![2]][2]

Pro stejné připojení následující obrázek znázorňuje zobrazení mřížky ve službě Network Watcher:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z centra virtuální sítě k virtuálnímu počítači v místním umístění 1 je znázorněna zde:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby gateway tunelového připojení Azure ExpressRoute pro Microsoft Enterprise Edge směrovač (MSEE). Druhý a třetí segmentů směrování se (CE) hraniční směrovač zákazníka a IP adres LAN v místním umístění 1. Tyto IP adresy nejsou inzerované ve virtuální síti centra. Čtvrtý segment směrování je virtuální počítač v 1 v místním umístění.


### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z centra virtuální sítě k virtuálnímu počítači v místním umístění 2 je znázorněna zde:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby ExpressRoute gateway tunel ke směrovači MSEE. Druhý a třetí segmentů směrování se CE směrovače a IP adres LAN v místním umístění 2. Tyto IP adresy nejsou inzerované ve virtuální síti centra. Čtvrtý segment směrování je virtuální počítač na místní umístění 2.

### <a name="path-to-the-remote-vnet"></a>Cesta k vzdálené virtuální sítě

Traceroute výstup z centra virtuální sítě do virtuálního počítače ve vzdálené virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby ExpressRoute gateway tunel ke směrovači MSEE. Druhý segment směrování je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres pro směrování se neinzeruje ve virtuální síti centra. Třetí segment směrování je virtuální počítač na vzdálené virtuální sítě.

## <a name="data-path-from-the-spoke-vnet"></a>Cesta k datům z virtuální sítě paprsků

Virtuální sítě paprsků sdílí zobrazení sítě o virtuální síti centra. Prostřednictvím partnerského vztahu virtuální sítě, virtuální sítě paprsků využívá Brána vzdálené připojení k virtuální síti centra jako v případě, že je přímo připojený do virtuální sítě paprsků.

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z virtuální sítě paprsků k virtuálnímu počítači, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Cesta k větev virtuální sítě

Traceroute výstup z virtuální sítě paprsků k virtuálnímu počítači na větev, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

V tomto traceroute první segment směrování je bránu VPN virtuální síti centra. Druhý segment směrování je brána VPN větve virtuální sítě. IP adresa brány VPN větve virtuální sítě se neinzeruje v rámci virtuální sítě centra/paprsků. Třetí segment směrování je virtuální počítač na větev virtuální sítě.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě paprsků k virtuálnímu počítači v místním umístění 1 je znázorněna zde:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby virtuální síť centra ExpressRoute gateway tunel ke směrovači MSEE. Druhý a třetí segmentů směrování se CE směrovače a IP adres LAN v místním umístění 1. Tyto IP adresy nejsou inzerované v centru/paprsku virtuální sítě. Čtvrtý segment směrování je virtuální počítač v 1 v místním umístění.

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z virtuální sítě paprsků k virtuálnímu počítači v místním umístění 2 je znázorněna zde:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby virtuální síť centra ExpressRoute gateway tunel ke směrovači MSEE. Druhý a třetí segmentů směrování se CE směrovače a IP adres LAN v místním umístění 2. Tyto IP adresy nejsou inzerované v centru/paprsku virtuální sítě. Čtvrtý segment směrování je virtuální počítač v místním umístění 2.

### <a name="path-to-the-remote-vnet"></a>Cesta k vzdálené virtuální sítě

Traceroute výstup z virtuální sítě paprsků do virtuálního počítače ve vzdálené virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

V tomto traceroute první segment směrování je koncový bod služby virtuální síť centra ExpressRoute gateway tunel ke směrovači MSEE. Druhý segment směrování je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres pro směrování se neinzeruje v centru/paprsku virtuální sítě. Třetí segment směrování je virtuální počítač na vzdálené virtuální sítě.

## <a name="data-path-from-the-branch-vnet"></a>Cesta k datům z větve virtuální sítě

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z větve virtuální sítě k virtuálnímu počítači, kterou virtuální sítě je znázorněna zde:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

V tomto traceroute je první segment směrování VPN gateway větve virtuální sítě. Druhý segment směrování je bránu VPN virtuální síti centra. IP adresa brány VPN virtuální síti centra se neinzeruje v vzdálené virtuální sítě. Třetí segment směrování je virtuální počítač ve virtuální síti centra.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Traceroute výstup z větve virtuální sítě k virtuálnímu počítači v paprsku, virtuální síť je zobrazena zde:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

V tomto traceroute je první segment směrování VPN gateway větve virtuální sítě. Druhý segment směrování je bránu VPN virtuální síti centra. IP adresa brány VPN virtuální síti centra se neinzeruje v vzdálené virtuální sítě. Třetí segment směrování je virtuální počítač na virtuální sítě paprsků.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z větve virtuální sítě do virtuálního počítače v místním umístění 1 je znázorněna zde:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

V tomto traceroute je první segment směrování VPN gateway větve virtuální sítě. Druhý segment směrování je bránu VPN virtuální síti centra. IP adresa brány VPN virtuální síti centra se neinzeruje v vzdálené virtuální sítě. Třetí segment směrování je ukončení tunelu VPN ve primárního CE směrovači. Čtvrtý segment směrování je interní IP adresa 1 v místním umístění. Tato síť LAN IP adresa se neinzeruje mimo CE směrovače. Pátý segment směrování je cílový virtuální počítač v 1 v místním umístění.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Cesta k umístění 2 místní a vzdálené virtuální sítě

Jak jsme probírali v analýze rovina řízení, větev virtuální síť nemá žádné viditelnost 2 umístění místní nebo vzdálené virtuální sítě na konfiguraci sítě. Následující výsledky příkazu ping zkontrolujte: 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>Cesta k datům z místních umístění 1

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z 1 v místním umístění k virtuálnímu počítači, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

V tomto traceroute jsou prvními dvěma segmenty směrování součástí v místní síti. Třetí segment směrování je primární MSEE rozhraní, který směřuje směrovače CE. Čtvrtý segment směrování je bránu ExpressRoute virtuální síti centra. Rozsah IP bránu ExpressRoute virtuální síti centra se neinzeruje k místní síti. Pátý segment směrování je cílový virtuální počítač.

Network Watcher představuje pouze zobrazení zaměřené na Azure. Pro v místním perspektivu používáme Azure Network Performance monitoru. Network Performance Monitor poskytuje agenty, které můžete nainstalovat na servery v sítích mimo Azure pro analýzu dat cestu.

Následující obrázek znázorňuje topologii zobrazení místní virtuální počítač 1 umístění připojení k virtuálnímu počítači ve virtuální síti přes ExpressRoute centra:

[![4]][4]

Jak je uvedeno výše, použije instalační program testovací VPN typu site-to-site jako záložní připojení expressroute mezi místní umístění 1 a virtuální síti centra. K otestování cesta zálohovaná data, můžeme vyvolat chybu spojení ExpressRoute mezi místní umístění 1 primární CE směrovač a odpovídající směrovači MSEE. Chcete-li vyvolat chybu spojení ExpressRoute, vypnete rozhraní CE, která směřuje směrovači MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Následující obrázek znázorňuje zobrazení topologie připojení k místní virtuální počítač 1 umístění k virtuálnímu počítači ve virtuální síti centra prostřednictvím připojení site-to-site VPN při připojení ExpressRoute je mimo provoz:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Traceroute výstup z 1 v místním umístění k virtuálnímu počítači v paprsku, virtuální síť je zobrazena zde:

Pojďme vrácení primární připojení ExpressRoute jak provádět analýzu dat cesta směrem paprsek virtuální sítě:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Vyvolali primární připojení ExpressRoute 1 pro zbytek analýzy dat cestu.

### <a name="path-to-the-branch-vnet"></a>Cesta k větev virtuální sítě

Traceroute výstup z 1 v místním umístění k virtuálnímu počítači na větev, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Probereme v [analýzy rovina řízení][Control-Analysis], v místním umístění 1 nemá žádné viditelnosti v místním umístění 2 za konfiguraci sítě. Následující výsledky příkazu ping zkontrolujte: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Cesta k vzdálené virtuální sítě

Traceroute výstup z 1 v místním umístění do virtuálního počítače ve vzdálené virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Cesta k datům z místních umístění 2

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z místních umístění 2 k virtuálnímu počítači, kterou virtuální sítě je znázorněna zde:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Traceroute výstup z místních umístění 2 k virtuálnímu počítači v paprsku, virtuální síť je zobrazena zde:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Cesta k větev virtuální síť, v místním umístění 1 a vzdálené virtuální sítě

Probereme v [analýzy rovina řízení][Control-Analysis], v místním umístění 1 nemá žádné viditelnost větev virtuální sítě, 1 umístění místní nebo vzdálené virtuální sítě podle konfigurace sítě. 

## <a name="data-path-from-the-remote-vnet"></a>Cesta k datům ze vzdálené virtuální sítě

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z vzdálené virtuální sítě k virtuálnímu počítači, kterou virtuální sítě je znázorněna zde:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Traceroute výstup z vzdálené virtuální sítě k virtuálnímu počítači v paprsku, virtuální síť je zobrazena zde:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Cesta na větev virtuální síť a v místním umístění 2

Probereme v [analýzy rovina řízení][Control-Analysis], Vzdálená virtuální síť nemá žádné viditelnost větev virtuální síti nebo v místním umístění 2 za konfiguraci sítě. 

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Výstup Traceroute ze vzdálené virtuální sítě do virtuálního počítače v místním umístění 1 je znázorněna zde:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Připojení VPN typu site-to-site a ExpressRoute při vytvoření celostní

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-site přes ExpressRoute

Můžete nakonfigurovat VPN typu site-to-site s využitím partnerského vztahu pro soukromě výměnu dat mezi vaší místní sítí a virtuální sítě Azure ExpressRoute Microsoftu. V této konfiguraci může vyměňovat data s důvěrnost, pravosti a integrita. Výměna dat je taky zneužitím. Další informace o konfiguraci sítě VPN site-to-site protokolu IPsec v režimu tunelového propojení s využitím partnerského vztahu Microsoftu ExpressRoute najdete v části [VPN typu Site-to-site přes partnerský vztah ExpressRoute Microsoftu][S2S-Over-ExR]. 

Omezení na primární konfigurace site-to-site VPN, který používá partnerského vztahu Microsoftu je propustnost. Propustnost přes tunel IPsec je omezená kapacita brány VPN. Propustnost brány sítě VPN je nižší než propustnost pro ExpressRoute. V tomto scénáři použití tunelu IPsec pro vysoce zabezpečená provozu a používání soukromý partnerský vztah pro veškerý ostatní provoz optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN typu Site-to-site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako dvojici redundantních okruh k zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Také jak je ukázáno v našich nastavení testu, v rámci oblasti Azure, můžete použít VPN typu site-to-site vytvoříte cestu převzetí služeb při selhání pro připojení k ExpressRoute. Pokud jsou stejné předpony inzerované přes VPN typu site-to-site a ExpressRoute, upřednostňuje Azure ExpressRoute. Aby se zabránilo asymetrické směrování mezi ExpressRoute a VPN typu site-to-site, pro místní konfigurace sítě by měl také oplátku pomocí připojení ExpressRoute, použije připojení VPN typu site-to-site.

Další informace o tom, jak konfigurace současně existujících připojení pro ExpressRoute a VPN typu site-to-site najdete v tématu [ExpressRoute a site-to-site koexistence][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření back-end připojení k virtuální sítě paprsků a místech firemních poboček

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě paprsků pomocí VNet peering

Hvězdicová Architektura virtuální sítě se běžně používá. Centrum je virtuální sítě v Azure, která funguje jako ústřední bod připojení mezi vaší virtuální sítě paprsků a v místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s rozbočovači, a který můžete použít k izolaci úloh. Data jsou přenášena mezi místním datovým centrem a centra prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře, najdete v části [implementace síťové topologie centra s rameny v Azure][Hub-n-Spoke].

V partnerského vztahu v rámci oblasti virtuální sítě můžete použít s virtuálními sítěmi paprsků brány virtuální sítě centra (brány sítě VPN a ExpressRoute) ke komunikaci se vzdálenými sítěmi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Větev připojení k virtuální síti pomocí sítě site-to-site VPN

Může být vhodné větve virtuální sítě, které jsou v různých oblastech a místními sítěmi mezi sebou komunikovat přes virtuální síti centra. Nativní řešení Azure pro tuto konfiguraci je připojení VPN typu site-to-site pomocí sítě VPN. Alternativou je použití síťové virtuální zařízení (NVA) pro směrování v centru.

Další informace najdete v tématu [co je VPN Gateway?] [ VPN] a [nasazení vysoce dostupné síťové virtuální zařízení][Deploy-NVA].


## <a name="next-steps"></a>Další postup

Zobrazit [ExpressRoute – nejčastější dotazy] [ ExR-FAQ] na:
-   Přečtěte si, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute.
-   Přečtěte si, kolik brány ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Další informace o dalších limity škálování služby ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "network Watcher zobrazení připojení z virtuální síti centra pro paprsek virtuální sítě"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "network Watcher zobrazení připojení z virtuální síti centra na větev virtuální sítě"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "zobrazení mřížky network Watcher připojení z virtuální síti centra na větev virtuální sítě"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "network Performance Monitor zobrazení připojení z virtuálního počítače 1 umístění k virtuální síti přes ExpressRoute 1 centra"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "network Performance Monitor zobrazení připojení z virtuálního počítače 1 umístění k virtuální síti prostřednictvím sítě site-to-site VPN centra"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


