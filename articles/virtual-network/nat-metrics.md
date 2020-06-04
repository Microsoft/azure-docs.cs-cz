---
title: Metriky a výstrahy pro Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Pochopení Azure Monitor metrik a upozornění dostupných pro Virtual Network překladu adres (NAT)
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 7b4f433ec5782c7a819ffcd6f48bf277d7b13de2
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84341350"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metriky služby Azure Virtual Network NAT

Prostředky brány Azure Virtual Network NAT poskytují multidimenzionální metriky. Tyto metriky můžete použít ke sledování operace a k [řešení problémů](troubleshoot-nat.md).  Výstrahy je možné nakonfigurovat pro kritické problémy, jako je například vyčerpání SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*

## <a name="metrics"></a>Metriky

Prostředky brány NAT poskytují v Azure Monitor následující multidimenzionální metriky:

| Metric | Popis | Doporučená agregace | Dimenze |
|---|---|---|---|
| Bajty | Příchozí a odchozí bajty zpracované | Součet | Směr (v; Out), protokol (6 TCP; 17 UDP) |
| Rámců | Příchozí a odchozí pakety zpracované | Součet | Směr (v; Out), protokol (6 TCP; 17 UDP) |
| Vyřazené pakety | Pakety vyřazené bránou NAT | Součet | / |
| Počet připojení SNAT | Přechody stavu za interval | Součet | Stav připojení, protokol (6 TCP; 17 UDP) |
| Celkový počet připojení SNAT | Aktuální aktivní připojení SNAT (počet používaných portů: ~ SNAT) | Součet | Protokol (6 TCP; 17 UDP) |


## <a name="alerts"></a>Výstrahy

Výstrahy pro metriky je možné nakonfigurovat v Azure Monitor pro každou z výše uvedených [metrik](#metrics).

## <a name="limitations"></a>Omezení

Resource Health se nepodporuje.

## <a name="next-steps"></a>Další kroky

* Informace o [Virtual Network NAT](nat-overview.md)
* Další informace o [prostředku brány NAT](nat-gateway-resource.md)
* Informace o [Azure monitor](../azure-monitor/overview.md)
* Přečtěte si informace o [řešení potíží s prostředky brány NAT](troubleshoot-nat.md).
* [Řekněte nám, co se má sestavit příště pro Virtual Network překlad adres (NAT) ve službě UserVoice](https://aka.ms/natuservoice).


