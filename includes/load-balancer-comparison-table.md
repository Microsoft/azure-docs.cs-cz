---
title: zahrnout soubor
description: zahrnout soubor
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78202485"
---
| | Standard Load Balancer | Základní Load Balancer |
| --- | --- | --- |
| [Velikost fondu back-endu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Podporuje až 1000 instancí. | Podporuje až 300 instancí. |
| Koncové body fondu back-endu | Všechny virtuální počítače nebo sady škálování virtuálních počítačů v jedné virtuální síti. | Virtuální počítače v jedné skupině dostupnosti nebo v sadě škálování virtuálních počítačů. |
| [Sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Chování sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Připojení TCP zůstávají v provozu při testování instance dolů __a__ na všech sondách. | Připojení TCP zůstávají v provozu při testování instance. Všechna připojení TCP se ukončí, když jsou všechny sondy mimo provoz. |
| Zóny dostupnosti | Zóna – redundantní a oblasti front-endu pro příchozí a odchozí provoz. | Není k dispozici. |
| Diagnostika | [Azure Monitor multidimenzionální metriky](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Protokoly služby Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Porty HA | [K dispozici pro interní Load Balancer](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Není k dispozici. |
| Zabezpečení ve výchozím nastavení | Uzavřeno do příchozích toků, pokud to není povoleno skupinou zabezpečení sítě. Upozorňujeme, že interní provoz z virtuální sítě do interního nástroje pro vyrovnávání zatížení je povolený. | Ve výchozím nastavení otevřete. Skupina zabezpečení sítě je volitelná. |
| Pravidla pro odchozí provoz | [Konfigurace deklarativního odchozího překladu adres (NAT)](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Není k dispozici. |
| Resetování protokolu TCP při nečinnosti | [K dispozici u libovolného pravidla](../articles/load-balancer/load-balancer-tcp-reset.md) | Není k dispozici. |
| [Více front-endy](../articles/load-balancer/load-balancer-multivip-overview.md) | Příchozí a [odchozí](../articles/load-balancer/load-balancer-outbound-connections.md) | Pouze příchozí |
| Operace správy | Většina operací < 30 sekund | typických 60 až 90 sekund |
| SLA | [99,99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Není k dispozici. | 
