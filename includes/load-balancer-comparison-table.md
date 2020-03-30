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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78202485"
---
| | Standardní balancer zatížení | Základní systém vyrovnávání zatížení |
| --- | --- | --- |
| [Velikost back-endu fondu](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Podporuje až 1000 instancí. | Podporuje až 300 instancí. |
| Koncové body back-endového fondu | Všechny virtuální počítače nebo škálovací sady virtuálních počítačů v jedné virtuální síti. | Virtuální počítače v jedné sadě dostupnosti nebo škálovací sadě virtuálních počítačů. |
| [Sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Chování sondy stavu dolů](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Připojení TCP zůstávají naživu na sondě instance dolů __a__ na všech sondách dolů. | Připojení TCP zůstávají naživu na sondě instance dolů. Všechna připojení TCP jsou ukončena, když jsou všechny sondy vypnuty. |
| Zóny dostupnosti | Zónově redundantní a zónové frontendy pro příchozí a odchozí přenosy. | Není k dispozici. |
| Diagnostika | [Vícerozměrné metriky Azure Monitoru](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Protokoly azure monitoru](../articles/load-balancer/load-balancer-monitor-log.md) |
| Porty HA | [K dispozici pro interní vytápěč zatížení](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Není k dispozici. |
| Zabezpečení ve výchozím nastavení | Uzavřeno pro příchozí toky, pokud to není povoleno skupinou zabezpečení sítě. Vezměte prosím na vědomí, že vnitřní provoz z virtuální sítě do interního vyrovnávání zatížení je povoleno. | Ve výchozím nastavení otevřít. Skupina zabezpečení sítě je volitelná. |
| Pravidla pro odchozí provoz | [Deklarativní odchozí konfigurace naváděného naserveru NAT](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Není k dispozici. |
| Obnovení protokolu TCP při nečinnosti | [K dispozici na jakémkoli pravidle](../articles/load-balancer/load-balancer-tcp-reset.md) | Není k dispozici. |
| [Více předních konců](../articles/load-balancer/load-balancer-multivip-overview.md) | Příchozí a [odchozí](../articles/load-balancer/load-balancer-outbound-connections.md) | Pouze příchozí |
| Operace řízení | Většina operací < 30 sekund | 60-90+ sekund typických |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Není k dispozici. | 
