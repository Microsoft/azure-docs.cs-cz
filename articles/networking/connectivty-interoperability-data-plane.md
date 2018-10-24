---
title: 'Vzájemná funkční spolupráce ExpressRoute, VPN typu Site-to-site a VNet Peering – roviny dat analýzy: připojení k back-endu Azure funkce Interoperability | Dokumentace Microsoftu'
description: Tato stránka umožňuje analyzovat data roviny nastavení testu, který je vytvořen pro analýzu interoperability funkce ExpressRoute, VPN typu Site-to-site a VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947183"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Vzájemná funkční spolupráce ExpressRoute, Site-to-site VPN a partnerského vztahu – analýza roviny dat

V tomto článku Podívejme se analýza roviny dat z nastavení testu. Pokud chcete zkontrolovat nastavení testu, naleznete v tématu [nastavení testu][Setup]. Projděte si podrobnosti o konfiguraci nastavení testu, naleznete v tématu [konfiguraci nastavení testu][Configuration]. Analýza roviny řízení nastavení testu najdete v tématu [analýzy rovina řízení][Control-Analysis].

Analýza roviny dat prozkoumá cestu pakety procházení z jedné místní sítě (LAN/virtuální sítě) do jiného v rámci topologie. Na cestu k datům mezi dvě místní sítě nemusí být nutně symetrické. Proto v tomto článku umožňuje analyzovat cestu pro předávání z místní sítě do druhé odděleně od Obrátit cestu.

##<a name="data-path-from-hub-vnet"></a>Cesta k datům z virtuální sítě centra

###<a name="path-to-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Partnerský vztah virtuální sítě emuluje funkčnost síťový most mezi dvěma virtuálními sítěmi, které jsou v partnerském vztahu. Traceroute výstup z virtuální sítě centra k virtuálnímu počítači v paprsku virtuální sítě jsou uvedeny níže:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Následující obrazovku Galerie je zobrazení grafického připojení virtuální sítě centra a paprsku přednášející: Azure Network Watcher:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Cesta k virtuální síti větve

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Ve výše uvedené traceroute je první segment směrování VPN brány virtuální sítě centra. Druhý segment směrování je VPN brány virtuální sítě větev, jejichž IP adresa se neinzeruje v rámci virtuální sítě centra. Třetí segment směrování je virtuální počítač ve virtuální síti větve.

Následující obrazovku Galerie je zobrazení grafického připojení virtuální sítě centra a virtuální síť větev přednášející: Azure Network Watcher:

[![2]][2]

Následující obrazovku Galerie pro stejné připojení, je zobrazení tabulky přednášející: Azure Network Watcher:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Cesta na místní umístění-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunel ke směrovači MSEE. Druhý a třetí směrování jsou CE směrovače a IP adres LAN v místním umístění 1, tyto IP adresy nejsou inzerované v rámci virtuální sítě centra. Čtvrtý segment směrování je virtuální počítač na místní umístění-1.


###<a name="path-to-on-premises-location-2"></a>Cesta k umístění v místním-2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunel ke směrovači MSEE. Druhý a třetí směrování jsou CE směrovače a IP adres LAN v místním umístění 2, tyto IP adresy nejsou inzerované v rámci virtuální sítě centra. Čtvrtý segment směrování je virtuální počítač na místní umístění-2.

###<a name="path-to-remote-vnet"></a>Cesta k vzdálené virtuální sítě

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunel ke směrovači MSEE. Druhý segment směrování je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres pro směrování se neinzeruje v rámci virtuální sítě centra. Třetí segment směrování je virtuální počítač na vzdálené virtuální sítě.

##<a name="data-path-from-spoke-vnet"></a>Cesta k datům z virtuální sítě paprsků

Vzpomínáte, že virtuální sítě paprsků sdílet zobrazení sítě o virtuální síti centra. Prostřednictvím partnerského vztahu virtuální sítě, virtuální sítě paprsků používá Brána vzdálené připojení k virtuální síti centra, jako by byli přímo připojení k virtuální sítě paprsků.

###<a name="path-to-hub-vnet"></a>Cesta k virtuální síti centra

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Cesta k virtuální síti větve

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Ve výše uvedené traceroute je první segment směrování VPN brány virtuální sítě centra. Druhý segment směrování je VPN brány virtuální sítě větev, jejichž IP adresa se neinzeruje v rámci virtuální sítě centra/paprsky. Třetí segment směrování je virtuální počítač ve virtuální síti větve.

###<a name="path-to-on-premises-location-1"></a>Cesta na místní umístění-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunelového propojení virtuální sítě centra ke směrovači MSEE. Druhý a třetí směrování jsou CE směrovače a IP adres LAN v místním umístění 1, tyto IP adresy nejsou inzerované v rámci virtuální sítě centra/paprsky. Čtvrtý segment směrování je virtuální počítač na místní umístění-1.

###<a name="path-to-on-premises-location-2"></a>Cesta k umístění v místním-2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunelového propojení virtuální sítě centra ke směrovači MSEE. Druhý a třetí směrování jsou CE směrovače a IP adres LAN v místním umístění 2, tyto IP adresy nejsou inzerované v rámci virtuální sítě centra/paprsky. Čtvrtý segment směrování je virtuální počítač na místní umístění-2.

###<a name="path-to-remote-vnet"></a>Cesta k vzdálené virtuální sítě

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Ve výše uvedené traceroute první segment směrování je koncový bod brány ExpressRoute tunelového propojení virtuální sítě centra ke směrovači MSEE. Druhý segment směrování je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres pro směrování se neinzeruje v rámci virtuální sítě centra/paprsky. Třetí segment směrování je virtuální počítač na vzdálené virtuální sítě.

##<a name="data-path-from-branch-vnet"></a>Cesta k datům z větve virtuální sítě

###<a name="path-to-hub-vnet"></a>Cesta k virtuální síti centra

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Ve výše uvedené traceroute je první segment směrování VPN brány sítě vnet, větev. Druhý segment směrování je VPN brány virtuální sítě centra, jejichž IP adresa se neinzeruje v rámci vzdálené virtuální sítě. Třetí segment směrování je virtuální počítač ve virtuální síti centra.

###<a name="path-to-spoke-vnet"></a>Cesta k virtuální sítě paprsků

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Ve výše uvedené traceroute je první segment směrování VPN brány sítě vnet, větev. Druhý segment směrování je VPN brány virtuální sítě centra, jejichž IP adresa se neinzeruje v rámci vzdálené virtuální sítě, a třetí segment směrování je virtuální počítač na virtuální sítě paprsků.

###<a name="path-to-on-premises-location-1"></a>Cesta na místní umístění-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Ve výše uvedené traceroute je první segment směrování VPN brány sítě vnet, větev. Druhý segment směrování je VPN brány virtuální sítě centra, jejichž IP adresa se neinzeruje v rámci vzdálené virtuální sítě. Třetí segment směrování je ukončení tunelu VPN ve primárního CE směrovači. Čtvrtý segment směrování je interní IP adresu v místním umístění – 1 IP adresy sítě LAN, které se neinzeruje mimo CE směrovače. Pátý segment směrování je cílový virtuální počítač na místní umístění-1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Cesta k místní umístění-2 a vzdálené virtuální sítě

Jak jsme probírali před analýzou rovina řízení, větev virtuální síť nemá žádné viditelnost v místním umístění-2 nebo vzdálené virtuální sítě na konfiguraci sítě. Následující výsledky příkazu ping zkontrolujte skutečnost. 

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

##<a name="data-path-from-on-premises-location-1"></a>Cesta k datům z místních umístění-1

###<a name="path-to-hub-vnet"></a>Cesta k virtuální síti centra

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

Ve výše uvedené traceroute jsou prvními dvěma segmenty směrování části místní sítě. Třetí segment směrování je primární MSEE rozhraní směřující CE směrovače. Čtvrtý segment směrování je ExpressRoute G/W ve virtuální síti, jejichž rozsah IP adres se neinzeruje k místní síti centra. Pátý segment směrování je cílový virtuální počítač.

Azure Network Watcher poskytuje pouze zaměřené na Azure. Proto pro zobrazení na střed v místním jsme použili Azure Network Performance Monitor (NPM). NPM obsahuje agenty, které můžou být nainstalované serverů v síti mimo Azure a provádět analýzu dat path.

Následující obrazovku Galerie je zobrazení topologie v místním umístění 1 virtuální počítač připojení k virtuálnímu počítači ve virtuální síti přes ExpressRoute centra.

[![4]][4]

Vzpomínáte, nastavení testu používá VPN typu Site-to-Site jako záložní připojení expressroute mezi místní umístění-1 a virtuální síť centra. K otestování back datapath, Pojďme zahájit chybu spojení ExpressRoute mezi místní směrovač CE primární umístění-1 a odpovídající směrovači MSEE vypněte rozhraní CE směřující směrovači MSEE.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Následující obrazovku Galerie je topologie zobrazení místní umístění 1 virtuální počítač připojení k virtuálnímu počítači přes připojení VPN typu Site-to-Site k virtuální síti centra, když připojení ExpressRoute je mimo provoz.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Cesta k virtuální sítě paprsků

Dejte nám vrácení primární připojení ExpressRoute k analýzám datapath směrem k virtuální sítě paprsků.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Dejte nám vyvolali primární připojení ExpressRoute-1 pro zbytek analýzy datapath.

###<a name="path-to-branch-vnet"></a>Cesta k virtuální síti větve

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Cesta k umístění v místním-2

Jak jsme probírali před analýzou rovina řízení, v místním umístění-1 nemá žádné viditelnosti v místním umístění-2 za konfiguraci sítě. Následující výsledky příkazu ping zkontrolujte skutečnost. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Cesta k vzdálené virtuální sítě

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Cesta k datům z místních umístění-2

###<a name="path-to-hub-vnet"></a>Cesta k virtuální síti centra

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Cesta k virtuální sítě paprsků

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Cesta k virtuální síti, virtuální sítě v místním umístění-1 a vzdálené větve

Jak jsme probírali před analýzou rovina řízení, v místním umístění-1 nemá žádné viditelnost do větve virtuální síť, v místním umístění-1 a vzdálené virtuální sítě na konfiguraci sítě. 

##<a name="data-path-from-remote-vnet"></a>Cesta k datům ze vzdálené virtuální sítě

###<a name="path-to-hub-vnet"></a>Cesta k virtuální síti centra

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Cesta k virtuální sítě paprsků

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Cesta k větev virtuální síť a v místním umístění-2

Jak jsme probírali před analýzou rovina řízení, vzdálenou virtuální síť nemá žádné viditelnosti na větev virtuální sítě a v místním umístění-2 za konfiguraci sítě. 


### <a name="path-to-on-premises-location-1"></a>Cesta na místní umístění-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Další čtení

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Při vytvoření celostní pomocí připojení VPN typu Site-to-Site a ExpressRoute

####<a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-Site přes ExpressRoute

Je možné nakonfigurovat VPN typu Site-to-Site přes partnerský vztah ExpressRoute Microsoftu k soukromě výměně dat mezi vaší místní sítě a sítě Azure Vnet s důvěrnost, zneužitím, pravosti a integrita. Další informace týkající se konfigurace IPSec Site-to-Site VPN v tunelovém režimu přes partnerský vztah ExpressRoute Microsoftu najdete v tématu [VPN typu Site-to-site přes ExpressRoute-partnerský vztah Microsoftu][S2S-Over-ExR]. 

Konfigurace sítě VPN typu S2S prostřednictvím partnerského vztahu Microsoftu hlavním omezením je propustnost. Propustnost přes tunel IPSec je omezená kapacita brány VPN. Propustnost brány sítě VPN je že menší ve srovnání s ExpressRoute propustnosti. V takových scénářích použití tunelu IPSec pro vysoké zabezpečení provozu a soukromého partnerského vztahu pro všechny ostatní provoz by vám pomůžou optimalizovat využití šířky pásma ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN typu Site-to-Site jako cestu zabezpečené převzetí služeb při selhání pro ExpressRoute
ExpressRoute se nabízí jako dvojici redundantních okruh k zajištění vysoké dostupnosti. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Také jako hotové v našich nastavení testu v rámci dané oblasti Azure, pokud chcete cestu převzetí služeb při selhání pro připojení k ExpressRoute, můžete provést pomocí Site-to-Site VPN. Pokud jsou stejné předpony inzerované prostřednictvím ExpressRoute a S2S VPN, preferuje Azure ExpressRoute přes síť VPN typu S2S. Aby se zabránilo asymetrické směrování mezi ExpressRoute a S2S VPN pro místní konfigurace sítě by měl také oplátku preferují ExpressRoute přes připojení S2S VPN.

Další informace o tom, jak konfigurace současně existujících připojení ExpressRoute a VPN typu Site-to-Site najdete v tématu [koexistence Site-to-Site a ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšíření back-end připojení k paprsků virtuální sítě a větve umístění

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Připojení virtuálních sítí pomocí VNet peering paprsků

Architektura střed a paprsek virtuální sítě se běžně používá. Centrum je virtuální síť (VNet) v Azure, která funguje jako ústřední bod připojení mezi vaší virtuální sítě paprsků a v místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centra a slouží k izolaci úloh. Data jsou přenášena mezi místním datovým centrem a centra prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře, najdete v části [střed a paprsek architektury][Hub-n-Spoke]

Vytvoření partnerského vztahu v rámci oblasti virtuální sítě umožňuje virtuální sítě do brány virtuální sítě centra (brány sítě VPN a ExpressRoute) použít ke komunikaci se vzdálenými sítěmi paprsků.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Nepodmíněný skok připojení k virtuální síti pomocí sítě VPN typu Site-to-Site

Pokud chcete, aby větev virtuálními sítěmi (v různých oblastech) a místními sítěmi vzájemně komunikovat přes virtuální síť centra, nativní řešení Azure je připojení k síti VPN site-to-site pomocí sítě VPN. Alternativní možností je použít síťové virtuální zařízení pro směrování v centru.

Konfigurace bran VPN, naleznete v tématu [konfigurace brány VPN][VPN]. Nasazení vysoce dostupných síťových virtuálních zařízení, najdete v části [nasazení vysoce dostupné síťové virtuální zařízení][Deploy-NVA].

## <a name="next-steps"></a>Další postup

Zjistěte, kolik okruhy ExpressRoute, můžete se připojit k bránu ExpressRoute nebo kolik brány ExpressRoute můžete připojit k okruhu ExpressRoute, přečtěte si další limity škálování služby ExpressRoute, najdete v článku [ExpressRoute – nejčastější dotazy][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "network Watcher zobrazení připojení z virtuální sítě centra do virtuální sítě paprsků"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "network Watcher zobrazení připojení z virtuální sítě centra k virtuální síti větve"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "zobrazení mřížky network Watcher připojení z virtuální sítě centra k virtuální síti větve"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "network Performance Monitor zobrazení připojení z virtuálního počítače 1 umístění k virtuální síti centra prostřednictvím ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "network Performance Monitor zobrazení připojení z virtuálního počítače 1 umístění k virtuální síti centra přes síť VPN S2S"

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




