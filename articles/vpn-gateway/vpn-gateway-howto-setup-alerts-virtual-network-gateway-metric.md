---
title: Nastavení výstrah pro Azure VPN Gateway metriky
description: Naučte se používat Azure Portal k nastavení výstrah Azure Monitor na základě metrik pro brány VPN virtuální sítě.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435653"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Nastavení výstrah pro VPN Gateway metriky

Tento článek vám pomůže nastavit výstrahy pro Azure VPN Gateway metriky. Azure Monitor poskytuje možnost nastavit výstrahy pro prostředky Azure. Můžete nastavit výstrahy pro brány virtuální sítě typu "VPN".


|**Metrika**   | **Jednotka** | **Členitost** | **Popis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Průměrné využití šířky pásma pro všechna připojení typu Site-to-site v bráně.     |
|**P2SBandwidth**| Bajty/s  | 1 min.  | Průměrné využití šířky pásma všech připojení typu Point-to-site v bráně    |
|**P2SConnectionCount**| Počet  | 1 min.  | Počet připojení typu Point-to-site v bráně   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Průměrné využití šířky pásma pro tunely vytvořené v bráně |
|**TunnelEgressBytes** | Bajty | 5 minut | Odchozí provoz na tunelech vytvořených v bráně.   |
|**TunnelEgressPackets** | Počet | 5 minut | Počet odchozích paketů v tunelech vytvořených v bráně.   |
|**TunnelEgressPacketDropTSMismatch** | Počet | 5 minut | Počet zrušených odchozích paketů v tunelech způsobených neshodou výběrů provozu |
|**TunnelIngressBytes** | Bajty | 5 minut | Příchozí provoz na tunelech vytvořených v bráně.   |
|**TunnelIngressPackets** | Počet | 5 minut | Počet příchozích paketů v tunelech vytvořených v bráně   |
|**TunnelIngressPacketDropTSMismatch** | Počet | 5 minut | Počet vynechaných příchozích paketů v tunelech způsobených neshodou v selektoru provozu |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Nastavení výstrah Azure Monitor na základě metrik pomocí Azure Portal

V následujícím ukázkovém kroku se vytvoří výstraha pro bránu pro:

- **Metrika:** TunnelAverageBandwidth
- **Podmínka:** Šířka pásma > 10 bajtů za sekundu
- **Okno:** 5 minut
- **Akce výstrahy:** Elektron



1. Přejít na prostředek brány virtuální sítě a na kartě **monitorování** vyberte **výstrahy** . Pak vytvořte nové pravidlo výstrahy nebo upravte existující pravidlo výstrahy.

   ![Výběry pro vytvoření pravidla výstrahy](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Vytvořit")

2. Jako prostředek vyberte svou bránu VPN.

   ![Tlačítko vybrat a brána sítě VPN v seznamu prostředků](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Vyberte")

3. Vyberte metriku, která se má pro výstrahu nakonfigurovat.

   ![Vybraná metrika v seznamu metrik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Vyberte")
4. Nakonfigurujte logiku signálu. Existují tři součásti:

    a. **Dimenze**: Pokud metrika obsahuje dimenze, můžete vybrat konkrétní hodnoty dimenze, aby výstraha vyhodnotila pouze data této dimenze. Tyto jsou volitelné.

    b. **Podmínka**: Jedná se o operaci pro vyhodnocení hodnoty metriky.

    c. **Čas**: Určete členitost dat metrik a časové období pro vyhodnocení výstrahy.

   ![Podrobnosti o konfiguraci logiky signálů](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Vyberte")

5. Chcete-li zobrazit konfigurovaná pravidla, vyberte možnost **Spravovat pravidla výstrah**.

   ![Tlačítko pro správu pravidel výstrah](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Vyberte")

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci výstrah v protokolech prostředků tunelu najdete v tématu [Nastavení výstrah v VPN Gatewaych protokolech prostředků](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
