---
title: Zobrazit metriky služby Azure VPN Gateway
description: Postup zobrazení VPN Gateway metriky
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531254"
---
# <a name="view-vpn-gateway-metrics"></a>Zobrazit VPN Gateway metriky

Brány VPN Azure můžete monitorovat pomocí Azure Monitor. Tento článek popisuje metriky, které jsou k dispozici prostřednictvím portálu. Metriky jsou odlehčené a můžou podporovat scénáře téměř v reálném čase, které jsou užitečné při upozorňování a rychlé detekci problémů.


|**Metrika**   | **Jednotka** | **Členitost** | **Popis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Průměrné využití šířky pásma pro všechna připojení typu Site-to-site v bráně.     |
|**P2SBandwidth**| Bajty/s  | 1 min.  | Průměrné využití šířky pásma všech připojení typu Point-to-site v bráně    |
|**P2SConnectionCount**| Count  | 1 min.  | Počet připojení typu Point-to-site v bráně   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Průměrné využití šířky pásma pro tunely vytvořené v bráně |
|**TunnelEgressBytes** | Bajty | 5 minut | Odchozí provoz na tunelech vytvořených v bráně.   |
|**TunnelEgressPackets** | Count | 5 minut | Počet odchozích paketů v tunelech vytvořených v bráně.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minut | Počet zrušených odchozích paketů v tunelech způsobených neshodou výběrů provozu |
|**TunnelIngressBytes** | Bajty | 5 minut | Příchozí provoz na tunelech vytvořených v bráně.   |
|**TunnelIngressPackets** | Count | 5 minut | Počet příchozích paketů v tunelech vytvořených v bráně   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minut | Počet vynechaných příchozích paketů v tunelech způsobených neshodou v selektoru provozu |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>Následující kroky vám pomůžou najít a zobrazit metriky:

1. Přejděte na prostředek brány virtuální sítě na portálu.
2. Výběrem **přehledu** zobrazíte celkový počet metrik příchozího tunelového propojení a odchozího přenosu dat.

   ![Výběry pro vytvoření pravidla výstrahy](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Zobrazit")

3. Pro zobrazení jakékoli jiné metriky uvedené výše. V rámci prostředku brány virtuální sítě klikněte na část **metriky** a vyberte metriku v rozevíracím seznamu.

   ![Tlačítko vybrat a brána sítě VPN v seznamu prostředků](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Vyberte")

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat výstrahy pro metriky tunelu, přečtěte si téma [Nastavení výstrah pro VPN Gateway metriky](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Informace o konfiguraci výstrah v protokolech prostředků tunelu najdete v tématu [Nastavení výstrah v VPN Gatewaych protokolech prostředků](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
