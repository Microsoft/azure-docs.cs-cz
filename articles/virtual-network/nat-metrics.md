---
title: Metriky a výstrahy pro virtuální síť Azure NAT
titleSuffix: Azure Virtual Network
description: Seznamte se s metrikami a výstrahami Azure Monitor, které jsou k dispozici pro virtuální síť NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 4f1760c32117b34d4d453964473cba3c7d07f725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79202174"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metriky virtuální sítě Azure

Prostředky brány virtuální sítě Azure poskytují vícerozměrné metriky. Tyto metriky můžete použít ke sledování operace a k [řešení potíží](nat-metrics.md).  Výstrahy lze nakonfigurovat pro kritické problémy, jako je vyčerpání SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuální síť NAT pro odchozí do Internetu">
</p>

*Obrázek: Virtuální síť NAT pro odchozí do Internetu*

## <a name="metrics"></a>Metriky

Prostředky brány NAT poskytují v Azure Monitoru následující vícerozměrné metriky:

| Metrika | Popis | Doporučená agregace | Dimenze |
|---|---|---|---|
| Bajty | Počet zpracovaných příchozích a odchozích | Součet | Směr (V; Out), Protokol (6 TCP; 17 UDP) |
| Pakety | Příchozí a odchozí pakety | Součet | Směr (V; Out), Protokol (6 TCP; 17 UDP) |
| Vynecháné pakety | Pakety vynechané bránou NAT | Součet | / |
| Počet připojení SNAT | Přechody stavu za interval | Součet | Stav připojení, protokol (6 TCP; 17 UDP) |
| Celkový počet připojení SNAT | Aktuální aktivní připojení SNAT (~ porty SNAT v provozu) | Součet | Protokol (6 TCP; 17 UDP) |


## <a name="alerts"></a>Výstrahy

Výstrahy pro metriky lze nakonfigurovat v Azure Monitoru pro každou z [předchozích metrik](#metrics).

## <a name="limitations"></a>Omezení

Stav prostředků není podporován.

## <a name="next-steps"></a>Další kroky

* Další informace o [překladu virtuálních sítí](nat-overview.md)
* Informace o [prostředku brány NAT](nat-gateway-resource.md)
* Další informace o [Azure Monitoru](../azure-monitor/overview.md)
* Informace o [řešení potíží s prostředky brány NAT](troubleshoot-nat.md).
* [Řekněte nám, co se má stavět pro virtuální síť NAT v UserVoice](https://aka.ms/natuservoice).


