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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: e3c47a60a6cda074eba7b5c3292577c29f50c2ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87424047"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metriky služby Azure Virtual Network NAT

Prostředky brány Azure Virtual Network NAT poskytují multidimenzionální metriky. Tyto metriky můžete použít ke sledování operace a k [řešení problémů](troubleshoot-nat.md).  Výstrahy je možné nakonfigurovat pro kritické problémy, jako je například vyčerpání SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network překlad adres (NAT) pro odchozí připojení do Internetu">
</p>

*Obrázek: Virtual Network NAT pro odchozí připojení do Internetu*

## <a name="metrics"></a>Metriky

Prostředky brány NAT poskytují v Azure Monitor následující multidimenzionální metriky:

| Metric | Popis | Doporučená agregace | Dimenze |
|---|---|---|---|
| Bajty | Příchozí a odchozí bajty zpracované | Sum | Směr (v; Out), protokol (6 TCP; 17 UDP) |
| Rámců | Příchozí a odchozí pakety zpracované | Sum | Směr (v; Out), protokol (6 TCP; 17 UDP) |
| Vyřazené pakety | Pakety vyřazené bránou NAT | Sum | / |
| Počet připojení SNAT | Přechody stavu za interval | Sum | Stav připojení, protokol (6 TCP; 17 UDP) |
| Celkový počet připojení SNAT | Aktuální aktivní připojení SNAT (počet používaných portů: ~ SNAT) | Sum | Protokol (6 TCP; 17 UDP) |


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


