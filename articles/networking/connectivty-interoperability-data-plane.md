---
title: 'Interoperabilita v Azure: analýza roviny dat'
description: Tento článek popisuje analýzu roviny dat v nastavení testu, kterou můžete použít k analýze vzájemné funkční spolupráce mezi ExpressRoute, VPN typu Site-to-site a partnerské vztahy virtuálních sítí v Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 635a8fc5409e18da9529763b06e4a531a36d0156
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169200"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilita v Azure: analýza roviny dat

Tento článek popisuje analýzu roviny dat v [nastavení testu][Setup]. Můžete také zkontrolovat [konfiguraci nastavení testu][Configuration] a [analýzu roviny ovládacího prvku][Control-Analysis] pro nastavení testu.

Analýza roviny dat prověřuje cestu pořízenou pakety, které procházejí z jedné místní sítě (LAN nebo virtuální sítě) do druhé v rámci topologie. Cesta k datům mezi dvěma místními sítěmi není nutně symetrická. Proto je v tomto článku analyzována předávací cesta z místní sítě do jiné sítě, která je oddělená od zpětné cesty.

## <a name="data-path-from-the-hub-vnet"></a>Cesta k datům z virtuální sítě centra

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsků

Partnerský vztah virtuální sítě (VNet) emuluje funkčnost síťového mostu mezi dvěma virtuální sítě, která jsou v partnerském vztahu. Traceroute výstup z virtuální sítě rozbočovače k virtuálnímu počítači ve virtuální síti paprsků se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

Následující obrázek ukazuje zobrazení grafického připojení virtuální sítě centra a virtuální sítě rozbočovače z perspektivy Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti pobočky

Výstup traceroute z virtuální sítě rozbočovače k virtuálnímu počítači ve virtuální síti ve větvi se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

V tomto traceroute je první segment směrování bránou VPN v Azure VPN Gateway virtuální sítě rozbočovače. Druhým směrováním je Brána VPN virtuální sítě pobočky. IP adresa brány VPN pro virtuální síť pobočky není inzerována ve virtuální síti centra. Třetí segment směrování je virtuální počítač ve virtuální síti pobočky.

Následující obrázek znázorňuje grafické zobrazení připojení virtuální sítě centra a virtuální síť pobočky z perspektivy Network Watcher:

![2][2]

Pro stejné připojení ukazuje následující obrázek zobrazení mřížky v Network Watcher:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě rozbočovače na virtuální počítač v místním umístění 1 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

V tomto traceroute je prvním směrováním koncový bod tunelu služby Azure ExpressRoute Gateway do směrovače Microsoft Enterprise Edge (MSEE). Druhý a třetí segment směrování je směrovačem zákaznické hranice (CE) a místní IP adresa v místním umístění. Tyto IP adresy nejsou inzerované ve virtuální síti centra. Čtvrtý segment směrování je virtuální počítač v místním umístění 1.


### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z virtuální sítě rozbočovače na virtuální počítač v místním umístění 2 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

V tomto traceroute je první segment směrování koncovým bodem tunelu ExpressRoute Gateway na MSEE. Druhý a třetí segment směrování je směrovačem CE a místní IP adresa umístění 2 LAN. Tyto IP adresy nejsou inzerované ve virtuální síti centra. Čtvrtý segment směrování je virtuální počítač v místním umístění 2.

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z virtuální sítě rozbočovače do virtuálního počítače ve vzdálené virtuální síti se zobrazuje tady:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

V tomto traceroute je první segment směrování koncovým bodem tunelu ExpressRoute Gateway na MSEE. Druhým směrováním je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres směrování není inzerovaný ve virtuální síti centra. Třetí segment směrování je virtuální počítač ve vzdálené virtuální síti.

## <a name="data-path-from-the-spoke-vnet"></a>Cesta k datům z virtuální sítě paprsků

Síť VNet s paprsky sdílí zobrazení sítě virtuální sítě rozbočovače. Virtuální síť rozbočovače prostřednictvím partnerského vztahu virtuálních sítí používá připojení vzdálené brány pro virtuální síť rozbočovače, jako by byla přímo připojena k virtuální síti paprsků.

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z virtuální sítě rozbočovače k virtuálnímu počítači ve virtuální síti centra se zobrazuje tady:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti pobočky

Výstup traceroute z virtuální sítě rozbočovače k virtuálnímu počítači ve virtuální síti ve větvi se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

V tomto traceroute je první segment směrování bránou VPN virtuální sítě rozbočovače. Druhým směrováním je Brána VPN virtuální sítě pobočky. IP adresa brány VPN pro virtuální síť pobočky není inzerována v rámci sítě VNet centra/paprsků. Třetí segment směrování je virtuální počítač ve virtuální síti pobočky.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě rozbočovače na virtuální počítač v místním umístění 1 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

V tomto traceroute je prvním směrováním koncový bod tunelu ExpressRoute brány virtuální sítě rozbočovače na MSEE. Druhý a třetí segment směrování je směrovačem CE a místní IP adresa pro místní umístění 1. Tyto IP adresy nejsou inzerovány ve virtuální síti centra/paprsků. Čtvrtý segment směrování je virtuální počítač v místním umístění 1.

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Traceroute výstup z virtuální sítě rozbočovače na virtuální počítač v místním umístění 2 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

V tomto traceroute je prvním směrováním koncový bod tunelu ExpressRoute brány virtuální sítě rozbočovače na MSEE. Druhý a třetí segment směrování je směrovačem CE a místní IP adresa umístění 2 LAN. Tyto IP adresy nejsou inzerované v centru/paprskech virtuální sítě. Čtvrtý segment směrování je virtuální počítač v místním umístění 2.

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z virtuální sítě rozbočovače do virtuálního počítače ve vzdálené virtuální síti se zobrazuje tady:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

V tomto traceroute je prvním směrováním koncový bod tunelu ExpressRoute brány virtuální sítě rozbočovače na MSEE. Druhým směrováním je IP adresa brány vzdálené virtuální sítě. Druhý rozsah IP adres směrování není inzerovaný ve virtuální síti rozbočovače/paprsek. Třetí segment směrování je virtuální počítač ve vzdálené virtuální síti.

## <a name="data-path-from-the-branch-vnet"></a>Cesta k datům z virtuální sítě pobočky

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Traceroute výstup z virtuální sítě z pobočky do virtuálního počítače ve virtuální síti centra se zobrazuje tady:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

V tomto traceroute je první segment směrování bránou VPN virtuální sítě pobočky. Druhým směrováním je Brána VPN virtuální sítě rozbočovače. IP adresa brány VPN pro virtuální síť rozbočovače není inzerována ve vzdálené virtuální síti. Třetí segment směrování je virtuální počítač ve virtuální síti centra.

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsků

Traceroute výstup z virtuální sítě z pobočky na virtuální počítač ve virtuální síti rozbočovače se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

V tomto traceroute je první segment směrování bránou VPN virtuální sítě pobočky. Druhým směrováním je Brána VPN virtuální sítě rozbočovače. IP adresa brány VPN pro virtuální síť rozbočovače není inzerována ve vzdálené virtuální síti. Třetí segment směrování je virtuální počítač ve virtuální síti rozbočovače.

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Traceroute výstup z virtuální sítě z pobočky na virtuální počítač v místním umístění 1 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

V tomto traceroute je první segment směrování bránou VPN virtuální sítě pobočky. Druhým směrováním je Brána VPN virtuální sítě rozbočovače. IP adresa brány VPN pro virtuální síť rozbočovače není inzerována ve vzdálené virtuální síti. Třetí segment směrování je koncovým bodem tunelu VPN v primárním směrovači CE. Čtvrtý segment směrování je interní IP adresa místního umístění 1. Tato IP adresa sítě LAN není inzerována mimo směrovač CE. Pátý segment směrování je cílový virtuální počítač v místním umístění 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Cesta k místnímu umístění 2 a vzdálené virtuální síti

Jak jsme probrali v analýze roviny ovládacího prvku, virtuální síť pobočky nemá žádnou viditelnost ani pro místní umístění 2 nebo ke vzdálené virtuální síti na základě konfigurace sítě. Následující výsledky nástroje test potvrzení: 

```console
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
```

## <a name="data-path-from-on-premises-location-1"></a>Cesta k datům z místního umístění 1

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Výstup traceroute z místního umístění 1 k virtuálnímu počítači ve virtuální síti centra se zobrazuje tady:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

V tomto traceroute jsou první dva segmenty součástí místní sítě. Třetí segment směrování je primární rozhraní MSEE, které čelí směrovači CE. Čtvrtý segment směrování je ExpressRoute bránou virtuální sítě rozbočovače. Rozsah IP adres brány ExpressRoute brány virtuální sítě rozbočovače není ohlášený místní síti. Pátý segment směrování je cílový virtuální počítač.

Network Watcher poskytuje jenom zobrazení zaměřené na Azure. Pro místní perspektivu používáme Azure Network Performance Monitor. Network Performance Monitor poskytuje agenty, které můžete nainstalovat na servery v sítích mimo Azure pro analýzu cesty k datům.

Následující obrázek znázorňuje zobrazení topologie připojení virtuálního počítače typu místní umístění 1 k VIRTUÁLNÍmu počítači ve virtuální síti centra prostřednictvím ExpressRoute:

![4][4]

Jak je popsáno výše, nastavení testu používá síť Site-to-Site VPN jako konektivitu zálohování pro ExpressRoute mezi místním umístěním 1 a virtuální sítí centra. Pokud chcete otestovat cestu k záložním datům, vydejte nám ExpressRoute selhání propojení mezi místním směrovačem umístění 1 primárního CE a odpovídajícím MSEE. Chcete-li vyvolat selhání propojení ExpressRoute, vypněte rozhraní CE, které čelí MSEE:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

Následující obrázek ukazuje zobrazení topologie připojení virtuálního počítače v místním umístění 1 k VIRTUÁLNÍmu počítači ve virtuální síti centra prostřednictvím připojení VPN typu Site-to-site v případě nefunkčnosti připojení ExpressRoute:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsků

Traceroute výstup z místního umístění 1 k virtuálnímu počítači ve virtuální síti paprsků se zobrazuje tady:

Pojďme se vrátit k ExpressRoute primárnímu připojení a provést analýzu cesty k datům směrem k virtuální síti paprsků:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Zaveďte primární připojení ExpressRoute 1 pro zbytek analýzy cesty k datům.

### <a name="path-to-the-branch-vnet"></a>Cesta k virtuální síti pobočky

Výstup traceroute z místního umístění 1 k virtuálnímu počítači ve virtuální síti ve větvi se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Cesta k místnímu umístění 2

Jak probereme v [analýze roviny ovládacího prvku][Control-Analysis], místní umístění 1 nemá žádnou viditelnost pro místní umístění 2 na konfiguraci sítě. Následující výsledky nástroje test potvrzení: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Cesta ke vzdálené virtuální síti

Traceroute výstup z místního umístění 1 do virtuálního počítače ve vzdálené virtuální síti se zobrazuje tady:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Cesta k datům z místního umístění 2

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Výstup traceroute z místního umístění 2 k virtuálnímu počítači ve virtuální síti centra se zobrazuje tady:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsků

Výstup traceroute z místního umístění 2 k virtuálnímu počítači ve virtuální síti paprsků se zobrazuje tady:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Cesta k virtuální síti pobočky, místnímu umístění 1 a vzdálené virtuální síti

Jak probereme v [analýze roviny ovládacího prvku][Control-Analysis], místní umístění 1 nemá žádnou viditelnost pro virtuální síť pobočky, místní umístění 1 nebo vzdálenou virtuální síť na konfiguraci sítě. 

## <a name="data-path-from-the-remote-vnet"></a>Cesta dat ze vzdálené virtuální sítě

### <a name="path-to-the-hub-vnet"></a>Cesta k virtuální síti centra

Výstup traceroute ze vzdálené virtuální sítě k virtuálnímu počítači ve virtuální síti centra se zobrazuje tady:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Cesta k virtuální síti paprsků

Výstup traceroute ze vzdálené virtuální sítě k virtuálnímu počítači ve virtuální síti rozbočovače se zobrazuje tady:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Cesta k virtuální síti pobočky a místnímu umístění 2

Jak probereme v [analýze roviny ovládacího prvku][Control-Analysis], vzdálená virtuální síť nemá žádnou viditelnost pro virtuální síť pobočky ani místní umístění 2 na konfiguraci sítě. 

### <a name="path-to-on-premises-location-1"></a>Cesta k místnímu umístění 1

Výstup traceroute ze vzdálené virtuální sítě do virtuálního počítače v místním umístění 1 se zobrazuje tady:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute a připojení VPN typu Site-to-site společně

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN typu Site-to-site přes ExpressRoute

SÍŤ VPN typu Site-to-site můžete nakonfigurovat pomocí partnerského vztahu ExpressRoute Microsoftu pro soukromou výměnu dat mezi vaší místní sítí a Azure virtuální sítě. V této konfiguraci můžete vyměňovat data s důvěrnými, pravostmi a integritou. Výměna dat je také anti-Play. Další informace o tom, jak nakonfigurovat síť VPN typu Site-to-Site VPN v tunelovém režimu pomocí partnerského vztahu Microsoftu ExpressRoute, najdete v tématu [VPN typu Site-to-site over ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Primární omezení konfigurace sítě Site-to-Site VPN, která používá partnerský vztah Microsoft, je propustnost. Propustnost prostřednictvím tunelu IPsec je omezená na kapacitu brány VPN. Propustnost brány VPN Gateway je nižší než ExpressRoute propustnost. V tomto scénáři pomáhá použití tunelového propojení IPsec pro vysoce zabezpečený provoz a používání privátních partnerských vztahů pro všechny ostatní přenosy optimalizovat využití šířky pásma ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Síť VPN typu Site-to-site jako zabezpečenou cestu převzetí služeb při selhání pro ExpressRoute

ExpressRoute slouží jako redundantní dvojice okruhů, aby se zajistila vysoká dostupnost. Geograficky redundantní připojení ExpressRoute můžete nakonfigurovat v různých oblastech Azure. Jak je znázorněno v našem nastavení testu v rámci oblasti Azure, můžete použít síť VPN typu Site-to-site k vytvoření cesty převzetí služeb při selhání pro připojení ExpressRoute. Pokud jsou stejné předpony inzerovány v rámci obou ExpressRoute a VPN typu Site-to-site, Azure upřednostní ExpressRoute. Aby se zabránilo asymetrickému směrování mezi ExpressRoute a VPN typu Site-to-site, místní konfigurace sítě by se měla docházet také pomocí připojení ExpressRoute, než použije připojení Site-to-Site VPN.

Další informace o tom, jak nakonfigurovat současně existující připojení pro ExpressRoute a síť Site-to-Site VPN, najdete v tématu [ExpressRoute a koexistence typu Site][ExR-S2S-CoEx]-to-site.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozšiřování připojení back-endu k paprskovým virtuální sítě a umístěním větví

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Připojení k virtuální síti na straně koncové sítě pomocí partnerského vztahu virtuální sítě

Architektura sítě VNet centra a paprsků se běžně používá. Centrum je virtuální síť v Azure, která funguje jako centrální bod připojení mezi virtuální sítě paprsky a vaší místní sítí. Paprsky jsou virtuální sítě v partnerském vztahu s rozbočovačem a můžete je použít k izolaci úloh. Přenos toků mezi místním datacentrem a centrem prostřednictvím připojení ExpressRoute nebo VPN. Další informace o architektuře najdete v tématu [implementace síťové topologie centra s paprsky v Azure][Hub-n-Spoke].

V rámci partnerského vztahu virtuálních sítí v rámci oblasti může paprskový virtuální sítě používat brány virtuální sítě rozbočovače (brány VPN a ExpressRoute) ke komunikaci se vzdálenými sítěmi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Připojení k virtuální síti větví pomocí sítě VPN typu Site-to-site

Můžete chtít, aby větev virtuální sítě, která je v různých oblastech, a místní sítě vzájemně komunikovaly přes virtuální síť rozbočovače. Nativní řešení Azure pro tuto konfiguraci je připojení VPN typu Site-to-site pomocí sítě VPN. Alternativou je použití síťového virtuálního zařízení (síťové virtuální zařízení) pro směrování v centru.

Další informace najdete v tématu [co je VPN Gateway?][VPN] a [nasazení vysoce dostupného síťové virtuální zařízeníu][Deploy-NVA].


## <a name="next-steps"></a>Další kroky

Podívejte se na téma [Nejčastější dotazy k ExpressRoute][ExR-FAQ] :
-   Přečtěte si, kolik okruhů ExpressRoute se můžete připojit k bráně ExpressRoute.
-   Podívejte se, kolik bran ExpressRoute se můžete připojit k okruhu ExpressRoute.
-   Přečtěte si o dalších omezeních škálování pro ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher zobrazení připojení z virtuální sítě rozbočovače k virtuální síti paprsků"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher zobrazení připojení z virtuální sítě rozbočovače k virtuální síti pobočky"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Network Watcher zobrazení mřížky připojení z virtuální sítě rozbočovače k virtuální síti pobočky"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Network Performance Monitor zobrazení připojení z virtuálního počítače umístění 1 k virtuální síti centra prostřednictvím ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Network Performance Monitor zobrazení připojení z virtuálního počítače umístění 1 k virtuální síti centra prostřednictvím sítě VPN typu Site-to-site"

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


