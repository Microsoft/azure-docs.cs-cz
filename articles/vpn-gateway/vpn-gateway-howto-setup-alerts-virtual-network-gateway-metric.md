---
title: Jak nastavit výstrahy týkající se metrik Azure VPN Gateway
description: Postup konfigurace výstrah pro metriky brány sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769463"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Nastavení upozornění na metriky brány sítě VPN

Tento článek vám pomůže nastavit upozornění na metriky brány VPN. Platforma Azure monitor poskytuje možnost nastavit výstrahy pro prostředky Azure. Upozornění můžete nastavit pro brány virtuální sítě typu "VPN".


|**Metrika**   | **Jednotka** | **Členitost** | **Popis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| B/s  | 5 minut| Průměrné využití šířky pásma kombinované všechna připojení site-to-site pro bránu.     |
|**P2SBandwidth**| B/s  | 1 minuta  | Průměrné využití šířky pásma kombinované všechna připojení point-to-site pro bránu.    |
|**P2SConnectionCount**| Počet  | 1 minuta  | P2S počet připojení na bráně.   |
|**TunnelAverageBandwidth** | B/s    | 5 minut  | Průměrné využití šířky pásma tunely vytvořené v bráně. |
|**TunnelEgressBytes** | B | 5 minut | Odchozí přenosy na tunely vytvořené v bráně.   |
|**TunnelEgressPackets** | Počet | 5 minut | Počet odchozích paketů v tunely vytvořené v bráně.   |
|**TunnelEgressPacketDropTSMismatch** | Počet | 5 minut | Počet odchozích paketů přesunuta tunely způsobené neshoda TS. |
|**TunnelIngressBytes** | B | 5 minut | Příchozí provoz na tunely vytvořené v bráně.   |
|**TunnelIngressPackets** | Počet | 5 minut | Počet příchozích paketů na tunely vytvořené v bráně.   |
|**TunnelIngressPacketDropTSMismatch** | Počet | 5 minut | Počet příchozích paketů na tunely způsobené neshoda TS. |


## <a name="setup"></a>Nastavení Azure Monitor výstrah na základě metrik pomocí portálu

Následující příklad postup vytvoří výstrahu pro bránu pro: <br>

**Metrika:** Průměrná šířka pásma tunelu <br>
**Podmínka:** šířku pásma > 10 bajtů za sekundu <br>
**Okno:** 5 minut <br>
**Akce upozornění:** Email <br>



1. Přejděte do prostředku brány virtuální sítě a na kartě monitorování vyberte "Upozornění" pak vytvořit nové pravidlo upozornění nebo upravit stávající pravidla upozornění.

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Vyberte bránu VPN jako prostředek.

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. Vyberte metriku, která se konfigurace výstrahy ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "vyberte")
4. Konfigurovat logiku signálů. Existují tři komponenty do logiku signálů:

    a. Rozměry: Pokud metrika dimenze, lze vybrat konkrétní hodnoty dimenze, tak, aby upozornění vyhodnotí pouze data této dimenze. Toto jsou volitelné.<br>
    b. Podmínka: Operace k vyhodnocení, hodnota metriky.<br>
    c. Čas: Zadejte úrovně podrobností dat metriky a časového intervalu pro vyhodnocení upozornění na.<br>

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. Chcete-li zobrazit nakonfigurovaná pravidla, klikněte na "Spravovat pravidla výstrah" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "vyberte")

## <a name="next-steps"></a>Další postup

Konfigurace výstrah pro tunelové propojení diagnostické protokoly, najdete v článku [jak nastavit výstrahy na diagnostické protokoly brány sítě VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
