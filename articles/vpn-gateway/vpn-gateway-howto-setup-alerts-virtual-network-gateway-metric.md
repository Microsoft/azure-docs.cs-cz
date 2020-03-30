---
title: Nastavení výstrah na metrikách služby Azure VPN Gateway
description: Postup konfigurace výstrah v metrikách brány VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605232"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Nastavení upozornění na metriky brány VPN

Tento článek vám pomůže nastavit výstrahy na metriky Azure VPN Gateway. Azure Monitor poskytuje možnost nastavit výstrahy pro prostředky Azure. Můžete nastavit výstrahy pro brány virtuální sítě typu "VPN".


|**Metrika**   | **Jednotka** | **Členitost** | **Popis** | 
|---       | ---        | ---       | ---            | ---       |
|**Průměrná šířka pásma**| Bajty/s  | 5 minut| Průměrné kombinované využití šířky pásma všech připojení mezi lokalitami v bráně.     |
|**Šířka pásma P2S**| Bajty/s  | 1 minuta  | Průměrné kombinované využití šířky pásma všech připojení typu point-to-site v bráně.    |
|**P2SConnectionCount**| Počet  | 1 minuta  | Počet připojení bodu k webu v bráně.   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Průměrné využití šířky pásma tunelů vytvořených na bráně. |
|**TunnelEgressBytes** | Bajty | 5 minut | Odchozí provoz na tunelech vytvořených na bráně.   |
|**TunnelEgressPackets** | Počet | 5 minut | Počet odchozích paketů v tunelech vytvořených v bráně.   |
|**TunnelEgressPacketDropTSMismatch** | Počet | 5 minut | Počet odchozích paketů vynechaných v tunelech způsobených neshodou selektoru provozu. |
|**TunnelIngressBytes** | Bajty | 5 minut | Příchozí provoz na tunelech vytvořených na bráně.   |
|**TunnelIngressPackets** | Počet | 5 minut | Počet příchozích paketů v tunelových propojeních vytvořených v bráně.   |
|**TunnelIngressPacketDropTSMismatch** | Počet | 5 minut | Počet příchozích paketů vynechaných v tunelech způsobených neshodou voličů provozu. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Nastavení výstrah Azure Monitoru na základě metrik pomocí portálu Azure

Následující příklad kroků vytvoří výstrahu na bráně pro:

- **Metrika:** TunnelAverageBandwidth
- **Stav:** Šířka pásma > 10 bajtů za sekundu
- **Okno:** 5 minut
- **Výstražná akce:** E-mail



1. Přejděte na prostředek brány virtuální sítě a na kartě **Monitorování** vyberte **Výstrahy.** Potom vytvořte nové pravidlo výstrahy nebo upravte existující pravidlo výstrahy.

   ![Výběry pro vytvoření pravidla výstrahy](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Vytvořit")

2. Jako prostředek vyberte bránu VPN.

   ![Tlačítko Vybrat a brána VPN v seznamu zdrojů](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Vyberte")

3. Vyberte metriku, kterou chcete pro výstrahu nakonfigurovat.

   ![Vybraná metrika v seznamu metrik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Vyberte")
4. Nakonfigurujte logiku signálu. Existují tři složky:

    a. **Dimenze**: Pokud má metrika dimenze, můžete vybrat konkrétní hodnoty dimenzí tak, aby výstraha vyhodnotila pouze data této dimenze. Ty jsou volitelné.

    b. **Podmínka**: Jedná se o operaci k vyhodnocení hodnoty metriky.

    c. **Čas**: Zadejte rozlišovací schopnost dat metriky a dobu vyhodnocení výstrahy.

   ![Podrobnosti pro konfiguraci logiky signálu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Vyberte")

5. Chcete-li zobrazit nakonfigurovaná pravidla, vyberte **možnost Spravovat pravidla výstrah**.

   ![Tlačítko pro správu pravidel výstrah](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Vyberte")

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci výstrah v protokolech diagnostiky tunelového propojení naleznete v [tématu Nastavení výstrah v diagnostických protokolech brány VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
