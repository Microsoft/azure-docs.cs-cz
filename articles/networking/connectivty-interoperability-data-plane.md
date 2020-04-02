---
title: 'Interoperabilita v Azure: Analýza roviny dat'
description: Tento článek obsahuje analýzu roviny dat nastavení testu, které můžete použít k analýze interoperability mezi ExpressRoute, VPN mezi lokalitami a partnerským vztahem virtuální sítě v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: fe7b74b0d4d065d4f222fefbbdc4a1d434d1163b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518252"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilita v Azure: Analýza roviny dat

Tento článek popisuje analýzu roviny dat [nastavení testu][Setup]. Můžete také zkontrolovat [konfiguraci nastavení testu][Configuration] a [analýzu roviny řízení][Control-Analysis] nastavení testu.

Analýza roviny dat zkoumá cestu přijatých pakety, které procházejí z jedné místní sítě (LAN nebo virtuální sítě) do jiné v rámci topologie. Cesta k datům mezi dvěma místními sítěmi nemusí být nutně symetrická. Proto v tomto článku analyzujeme cestu pro předávání z místní sítě do jiné sítě, která je oddělená od zpětné cesty.

## <a name="data-path-from-the-hub-vnet"></a>Cesta k datům z virtuální sítě rozbočovače

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsku

Partnerský vztah virtuální sítě (VNet) emuluje funkce síťového mostu mezi dvěma virtuálními sítěmi, které jsou partnerské hodované. Traceroute výstup z virtuální sítě rozbočovače do virtuálního virtuálního virtuálního zařízení ve virtuální síti s paprskem se zobrazí tady:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Následující obrázek znázorňuje grafické zobrazení připojení virtuální sítě rozbočovače a virtuální sítě s paprskem z pohledu Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti větve

Traceroute výstup z virtuální sítě rozbočovače do virtuálního virtuálního virtuálního virtuálního virtuálního zařízení ve virtuální síti pobočky se zobrazí tady:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

V tomto traceroute první směrování je brána VPN v bráně Azure VPN v centrální virtuální síti. Druhý směrování je brána VPN pobočkové virtuální sítě. IP adresa brány VPN pobočkové virtuální sítě není inzerovaná ve virtuální síti rozbočovače. Třetí směrování je virtuální počítač na virtuální síti větve.

Následující obrázek znázorňuje grafické zobrazení připojení virtuální sítě rozbočovače a pobočky virtuální sítě z hlediska sledování sítě:

![2][2]

Pro stejné připojení je zobrazeno následující obrázek zobrazení mřížky v modulu sledování sítě:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě rozbočovače do virtuálního počítači v místním umístění 1 se zobrazí tady:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

V tomto traceroute první směrování je koncový bod tunelového propojení brány Azure ExpressRoute na Směrovač Microsoft Enterprise Edge (MSEE). Druhým a třetím směrováním jsou směrovač e-w.o. hraniční (CE) a místní ip adresy LOK1 LAN. Tyto IP adresy nejsou inzerované ve virtuální síti rozbočovače. Čtvrtý směrování je virtuální počítač v místní lokaci 1.


### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z virtuální sítě rozbočovače do virtuálního počítači v místním lokaci 2 se zobrazí tady:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

V tomto traceroute první směrování je ExpressRoute koncový bod tunelového propojení brány msee. Druhým a třetím směrováním jsou směrovač CE a místní ip adresy Lokace 2 LAN. Tyto IP adresy nejsou inzerované ve virtuální síti rozbočovače. Čtvrtý směrování je virtuální počítač v místní lokaci 2.

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z virtuální sítě rozbočovače do virtuálního virtuálního virtuálního zařízení ve vzdálené virtuální síti se zobrazí tady:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

V tomto traceroute první směrování je ExpressRoute koncový bod tunelového propojení brány msee. Druhý směrování je ip adresy brány vzdálené virtuální sítě. Druhý rozsah IP směrování není inzerovaný ve virtuální síti rozbočovače. Třetí směrování je virtuální počítač na vzdálené virtuální síti.

## <a name="data-path-from-the-spoke-vnet"></a>Cesta k datům z virtuální sítě paprsku

Virtuální síť pro paprsky sdílí síťové zobrazení virtuální sítě rozbočovače. Prostřednictvím partnerského vztahu virtuální sítě virtuální síť používá připojení vzdálené brány virtuální sítě rozbočovače, jako by byla přímo připojená k virtuální síti s paprskem.

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti rozbočovače

Traceroute výstup z virtuální sítě s paprskem do virtuálního virtuálního virtuálního zařízení v rozbočovači virtuální sítě se zobrazí tady:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti větve

Traceroute výstup z virtuální sítě s paprskem do virtuálního virtuálního virtuálního virtuálního zařízení ve virtuální síti pobočky se zobrazí zde:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

V tomto traceroute první směrování je brána VPN virtuální sítě rozbočovače. Druhý směrování je brána VPN pobočkové virtuální sítě. IP adresa brány VPN pobočkové virtuální sítě není inzerovaná v rámci virtuální sítě hub/spoke. Třetí směrování je virtuální počítač na virtuální síti větve.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě s paprskem na virtuální počítač v místním umístění 1 se zobrazí zde:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

V tomto traceroute první směrování je koncový bod tunelového propojení brány ExpressRoute virtuální sítě virtuální sítě na MSEE. Druhým a třetím směrováním jsou směrovač CE a místní ip adresy Lokace 1 LAN. Tyto IP adresy nejsou inzerované ve virtuální síti hub/spoke. Čtvrtý směrování je virtuální počítač v místní lokaci 1.

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z virtuální sítě s paprskem na virtuální počítač v místním lokaci 2 se zobrazí zde:


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

V tomto traceroute první směrování je koncový bod tunelového propojení brány ExpressRoute virtuální sítě virtuální sítě na MSEE. Druhým a třetím směrováním jsou směrovač CE a místní ip adresy Lokace 2 LAN. Tyto IP adresy nejsou inzerované ve virtuálních sítích hub/spoke. Čtvrtý směrování je virtuální počítač v místní lokaci 2.

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z virtuální sítě s paprskem na virtuální ms ve vzdálené virtuální síti se zobrazí tady:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

V tomto traceroute první směrování je koncový bod tunelového propojení brány ExpressRoute virtuální sítě virtuální sítě na MSEE. Druhý směrování je ip adresy brány vzdálené virtuální sítě. Druhý rozsah IP směrování není inzerovaný ve virtuální síti hub/spoke. Třetí směrování je virtuální počítač na vzdálené virtuální síti.

## <a name="data-path-from-the-branch-vnet"></a>Cesta k datům z virtuální sítě větve

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti rozbočovače

Traceroute výstup z virtuální sítě větve do virtuálního virtuálního virtuálního virtuálního zařízení v centrální virtuální síti se zobrazí tady:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

V tomto traceroute první směrování je brána VPN pobočky virtuální sítě. Druhý směrování je brána VPN virtuální sítě rozbočovače. IP adresa brány VPN virtuální sítě rozbočovače není inzerovaná ve vzdálené virtuální síti. Třetí směrování je virtuální počítač na virtuální síti rozbočovače.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsku

Traceroute výstup z virtuální sítě pobočky do virtuálního virtuálního virtuálního zařízení ve virtuální síti paprsku je zobrazena tady:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

V tomto traceroute první směrování je brána VPN pobočky virtuální sítě. Druhý směrování je brána VPN virtuální sítě rozbočovače. IP adresa brány VPN virtuální sítě rozbočovače není inzerovaná ve vzdálené virtuální síti. Třetí směrování je virtuální počítač na virtuální síti s paprskem.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě pobočky do virtuálního počítači v místním lokaci 1 se zobrazí tady:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

V tomto traceroute první směrování je brána VPN pobočky virtuální sítě. Druhý směrování je brána VPN virtuální sítě rozbočovače. IP adresa brány VPN virtuální sítě rozbočovače není inzerovaná ve vzdálené virtuální síti. Třetí směrování je koncový bod tunelového propojení VPN na primárním směrovači CE. Čtvrtý směrování je interní IP adresa místní lokace 1. Tato IP adresa LAN není inzerována mimo ce router. Pátý směrování je cílový virtuální počítač v místní lokaci 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Cesta k místní lokaci 2 a vzdálené virtuální síti

Jak jsme diskutovali v analýze roviny řízení, virtuální síť pobočky nemá žádnou viditelnost buď na místní lokaci 2 nebo do vzdálené virtuální sítě podle konfigurace sítě. Následující výsledky příkazu ping potvrzují: 

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

## <a name="data-path-from-on-premises-location-1"></a>Cesta k datům z místní holokace 1

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti rozbočovače

Traceroute výstup z místní lokace 1 do virtuálního počítače ve virtuální síti rozbočovače se zobrazí tady:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

V tomto traceroute první dva směrování jsou součástí místní sítě. Třetí směrování je primární rozhraní MSEE, které směřuje k směrovači CE. Čtvrtý směrování je brána ExpressRoute virtuální sítě rozbočovače. Rozsah IP adres brány ExpressRoute virtuální sítě rozbočovače není inzerovaný v místní síti. Pátý směrování je cílový virtuální ms.

Network Watcher poskytuje pouze zobrazení zaměřené na Azure. Pro místní perspektivu používáme Azure Network Performance Monitor. Sledování výkonu sítě poskytuje agenty, které můžete nainstalovat na servery v sítích mimo Azure pro analýzu datových cest.

Následující obrázek znázorňuje zobrazení topologie místnípřipojení virtuálního počítače Lokcování 1 k virtuálnímu počítači na virtuální síti rozbočovače přes ExpressRoute:

![4][4]

Jak již bylo popsáno dříve, testovací nastavení používá vpn mezi lokalitami jako připojení k zálohování pro ExpressRoute mezi místní lokací 1 a virtuální sítí rozbočovače. Chcete-li otestovat cestu záložních dat, vyvoláme selhání spojení ExpressRoute mezi místním primárním ce směrovačem location 1 a odpovídajícím msee. Chcete-li vyvolat selhání spojení ExpressRoute, vypněte rozhraní CE, které čelí msee:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Následující obrázek znázorňuje zobrazení topologie místního připojení virtuálního počítače Lokcování 1 k virtuálnímu počítači na virtuální síti rozbočovače prostřednictvím připojení VPN mezi lokalitami, když je připojení ExpressRoute vypnuté:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsku

Traceroute výstup z místní umístění 1 do virtuálního počítači ve virtuální síti paprsku se zobrazí zde:

Pojďme přivést zpět primární připojení ExpressRoute k analýze cesty dat směrem k virtuální síti paprsku:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Vyvolat primární připojení ExpressRoute 1 pro zbytek analýzy cesty k datům.

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti větve

Traceroute výstup z místní umístění 1 do virtuálního počítači ve virtuální síti pobočky je zobrazen a tady:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Jak jsme diskutovat v [analýze roviny řízení][Control-Analysis], místní lokace 1 nemá žádnou viditelnost na místní umístění 2 na konfiguraci sítě. Následující výsledky příkazu ping potvrzují: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z místní umístění 1 do virtuálního počítači ve vzdálené virtuální síti se zobrazí tady:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Cesta k datům z místní holokace 2

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti rozbočovače

Traceroute výstup z místní lokace 2 do virtuálního počítače ve virtuální síti rozbočovače se zobrazí tady:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsku

Traceroute výstup z místní umístění 2 do virtuálního počítači ve virtuální síti paprsku se zobrazí zde:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Cesta k virtuální síti pobočky, místní lokaci 1 a vzdálené virtuální síti

Jak jsme diskutovat v [analýze roviny řízení][Control-Analysis], místní Lokace 1 nemá žádnou viditelnost na pobočku virtuální sítě, místní umístění 1 nebo vzdálené virtuální sítě podle konfigurace sítě. 

## <a name="data-path-from-the-remote-vnet"></a>Cesta k datům ze vzdálené virtuální sítě

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti rozbočovače

Traceroute výstup ze vzdálené virtuální sítě do virtuálního virtuálního virtuálního zařízení v centrální virtuální síti se zobrazí tady:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsku

Traceroute výstup ze vzdálené virtuální sítě do virtuálního virtuálního zařízení ve virtuální síti paprsku je zobrazena tady:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Cesta k pobočkové virtuální síti a místní lokaci 2

Jak jsme diskutovat v [analýze roviny řízení][Control-Analysis], vzdálená virtuální síť nemá žádnou viditelnost na pobočku Virtuální síť nebo místní Location 2 na konfiguraci sítě. 

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup ze vzdálené virtuální sítě do virtuálního počítači v místním umístění 1 se zobrazí tady:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Připojení ExpressRoute a vpn site-to-site v tandemu

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN mezi lokalitami přes ExpressRoute

Síť VPN typu site-to-site můžete nakonfigurovat pomocí partnerského vztahu Microsoftu expressroute k soukromé výměně dat mezi místní sítí a virtuálními sítěmi Azure. Pomocí této konfigurace si můžete vyměňovat data s důvěrností, pravostí a integritou. Výměna dat je také anti-replay. Další informace o konfiguraci sítě IPsec VPN sítě na webu v režimu tunelového propojení pomocí partnerského vztahu Microsoft u ExpressRoute naleznete v [tématu Síť VPN nad programem ExpressRoute Microsoft][S2S-Over-ExR]. 

Primární maješku konfigurace sítě VPN typu site-to-site, která používá partnerský vztah Microsoftu, je propustnost. Propustnost tunelového propojení IPsec je omezena kapacitou brány VPN. Propustnost brány VPN je nižší než propustnost ExpressRoute. V tomto scénáři pomocí tunelu IPsec pro vysoce zabezpečené přenosy a pomocí soukromého partnerského vztahu pro všechny ostatní přenosy pomáhá optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site-to-site jako bezpečná cesta převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako redundantní okruh pár pro zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Jak je také ukázáno v našem testovacím nastavení, v rámci oblasti Azure můžete použít vpn mezi lokalitami k vytvoření cesty převzetí služeb při selhání pro připojení ExpressRoute. Když jsou stejné předpony inzerovány přes ExpressRoute i VPN site-to-site, Azure upřednostňuje ExpressRoute. Chcete-li se vyhnout asymetrickému směrování mezi ExpressRoute a sítí VPN mezi lokalitami, měla by místní konfigurace sítě také oplatit pomocí připojení ExpressRoute před použitím připojení VPN mezi lokalitami.

Další informace o konfiguraci koexistujících připojení pro ExpressRoute a síť VPN mezi lokalitami naleznete v [tématu ExpressRoute a koexistence mezi lokalitami][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření back-endového připojení na virtuální sítě s paprsky a pobočky

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení virtuální sítě Paprsku pomocí partnerského vztahu virtuální sítě

Architektura virtuální sítě rozbočovače a paprsku se používá široce. Rozbočovač je virtuální síť v Azure, která funguje jako centrální bod připojení mezi virtuálními sítěmi pro paprsky a s vaší místní sítí. Paprsky jsou virtuální sítě, které se sítovky s rozbočovačem a které můžete použít k izoluje úlohy. Přenosy mezi místním datovým centrem a centrem prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře najdete v [tématu Implementace topologie sítě s rozbočovačem v Azure][Hub-n-Spoke].

V partnerském vztahu virtuální sítě v rámci oblasti můžou virtuální sítě pro paprsky ke komunikaci se vzdálenými sítěmi používat brány virtuální sítě hubu (brány VPN i ExpressRoute).

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Připojení virtuální sítě pobočky pomocí sítě VPN mezi lokalitami

Můžete chtít pobočkové virtuální sítě, které jsou v různých oblastech a místní sítě komunikovat mezi sebou prostřednictvím virtuální sítě rozbočovače. Nativní řešení Azure pro tuto konfiguraci je připojení VPN mezi lokalitami pomocí sítě VPN. Alternativou je použití síťového virtuálního zařízení (NVA) pro směrování v rozbočovači.

Další informace naleznete v tématu [Co je brána VPN?][VPN] a [nasazení vysoce dostupného zařízení NVA][Deploy-NVA].


## <a name="next-steps"></a>Další kroky

Podívejte se na [nejčastější dotazy k Expresní trase:][ExR-FAQ]
-   Zjistěte, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Zjistěte, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Seznamte se s dalšími omezeními škálování ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Zobrazení sledování sítě připojení z virtuální sítě rozbočovače k virtuální síti s paprskem"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Zobrazení sledování sítě připojení z virtuální sítě rozbočovače k virtuální síti pobočky"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Zobrazení mřížky Sledování sítě připojení z virtuální sítě rozbočovače k virtuální síti pobočky"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Zobrazení sledování výkonu sítě připojení z virtuálního zařízení lokace 1 k virtuální síti rozbočovače přes ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Zobrazení sledování výkonu sítě připojení z virtuálního zařízení lokace 1 k virtuální síti rozbočovače prostřednictvím sítě VPN"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


