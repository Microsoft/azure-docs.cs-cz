---
title: Nastavte si upozornění na metriky Azure VPN Gateway
description: Postup konfigurace výstrah pro metriky brány sítě VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509907"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Nastavte si upozornění na metriky brány sítě VPN

Tento článek vám pomůže nastavit upozornění na metriky Azure VPN Gateway. Azure Monitor umožňuje nastavit výstrahy pro prostředky Azure. Můžete nastavit výstrahy pro brány virtuální sítě typu "VPN".


|**Metrika**   | **Jednotka** | **Členitost** | **Popis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Průměrné využití šířky pásma kombinované všechna připojení site-to-site k bráně.     |
|**P2SBandwidth**| Bajty/s  | 1 minuta  | Průměrné využití šířky pásma kombinované všechna připojení point-to-site k bráně.    |
|**P2SConnectionCount**| Count  | 1 minuta  | Počet připojení point-to-site k bráně.   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Průměrné využití šířky pásma tunely vytvořené v bráně. |
|**TunnelEgressBytes** | B | 5 minut | Odchozí přenosy na tunely vytvořené v bráně.   |
|**TunnelEgressPackets** | Count | 5 minut | Počet odchozích paketů v tunely vytvořené v bráně.   |
|**TunnelEgressPacketDropTSMismatch** | Počet | 5 minut | Počet odchozích paketů přesunuta tunely způsobené neshoda selektor provozu. |
|**TunnelIngressBytes** | B | 5 minut | Příchozí provoz na tunely vytvořené v bráně.   |
|**TunnelIngressPackets** | Count | 5 minut | Počet příchozích paketů na tunely vytvořené v bráně.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minut | Počet příchozích paketů na tunely způsobené neshoda selektor provozu. |


## <a name="setup"></a>Nastavení Azure Monitor výstrah na základě metrik s využitím webu Azure portal

Následující příklady postupu bude vytvoření výstrahy pro bránu pro:

- **Metrika:** TunnelAverageBandwidth
- **Podmínka:** Šířka pásma > 10 bajtů za sekundu
- **Okno:** 5 minut
- **Akce upozornění:** Email



1. Přejděte do prostředku brány virtuální sítě a vyberte **výstrahy** z **monitorování** kartu. Pak vytvořte nové pravidlo upozornění nebo upravit stávající pravidla upozornění.

   ![Vybrané možnosti pro vytvoření pravidla upozornění](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Vyberte bránu VPN jako prostředek.

   ![Vyberte tlačítko a brány VPN v seznamu prostředků](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "vyberte")

3. Vyberte metriku, která se konfigurace výstrahy.

   ![Vybraná metrika v seznamu metrik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "vyberte")
4. Konfigurovat logiku signálů. Existují tři součásti:

    a. **Dimenze:** Pokud metrika dimenze, můžete vybrat konkrétní hodnoty dimenze tak, aby upozornění vyhodnotí jenom data z dané dimenzi. Toto jsou volitelné.

    b. **Podmínka**: Právě tato operace se vyhodnotit hodnotu metriky.

    c. **Čas**: Zadejte úrovně podrobností dat metriky a časového intervalu pro vyhodnocení výstrahy.

   ![Podrobnosti o konfiguraci signalizuje, že logic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "vyberte")

5. Chcete-li zobrazit nakonfigurovaná pravidla, vyberte **spravovat pravidla výstrah**.

   ![Tlačítko pro spravovat pravidla výstrah](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "vyberte")

## <a name="next-steps"></a>Další postup

Konfigurace výstrah pro diagnostické protokoly pro tunelové propojení, najdete v článku [nastavení výstrah u diagnostické protokoly brány sítě VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
