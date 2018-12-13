---
title: zahrnout soubor
description: zahrnout soubor
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 12/11/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 61af77897de0ad860eb01ee309bbeedf939e466b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53326582"
---
| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-endu | Podporuje až 1 000 instancí | Podporuje až 100 instancí |
| Koncové body back-Endového fondu | jakýkoli virtuální počítač v jedné virtuální sítě, včetně kombinace virtuální počítače, skupiny dostupnosti, škálovacích sad virtuálních počítačů. | Nastavení virtuálních počítačů ve škálovací sadě nebo virtuální počítač jednoho dostupnosti. |
| [Sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonda stavu dolů chování](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Připojení TCP zůstat naživu na instanci test dolů __a__ na všechny testy dolů. | Připojení TCP zůstat naživu na instanci test mimo provoz. Ukončete všechna připojení TCP pro všechny testy jsou vypnuté. |
| Zóny dostupnosti | Ve standardním SKU, zónové a zónově redundantních front-endů pro příchozí a odchozí, odchozí toky mapování překonat selhání zóny, Vyrovnávání zatížení mezi zónami. | Není k dispozici |
| Diagnostika | Azure Monitor, vícedimenzionálních metrik, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (SNAT úspěšné i neúspěšné toky), měření roviny aktivních dat. | Azure Log Analytics pro veřejný Load balancer úrovně pouze SNAT vyčerpání výstrahy, počet stavu back-endový fond. |
| HA porty | Interní nástroj pro vyrovnávání zatížení | Není k dispozici. |
| Ve výchozím nastavení zabezpečení | Pokud vstupní seznam povolených adres skupina zabezpečení sítě, není povolena pro veřejné IP adresy a nástroje pro vyrovnávání zatížení koncové body (veřejné a vnitřní). | Výchozí otevřete skupinu zabezpečení sítě, které jsou volitelné. |
| [Odchozí připojení](../articles/load-balancer/load-balancer-outbound-connections.md) | Můžete definovat explicitně fondu na základě odchozí NAT s [odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Můžete použít několik front-endů se podle zatížení vyrovnávání pravidlo odhlásit. Odchozí scénář _musí_ explicitně vytvořit virtuální počítač pomocí odchozího připojení.  Koncové body služeb virtuální sítě se dá kontaktovat bez odchozího připojení a na zpracování dat se nepočítají.  Všechny veřejné IP adresy, včetně služeb Azure PaaS, která není k dispozici jako koncové body služby virtuální sítě, musíte kontaktovat prostřednictvím odchozího připojení a počet směrem k zpracovaná data. Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, odchozí připojení přes výchozí SNAT nejsou k dispozici. použít [odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md) místo. Přenosový protokol konkrétní založené na protokolu Příchozí pravidlo Vyrovnávání zatížení je výstupní programování SNAT. | Jeden front-endu, když jsou k dispozici několik front-endů vybraného náhodně.  Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, použije se výchozí SNAT. |
| [Odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativní odchozí NAT konfiguraci pomocí veřejné IP adresy nebo veřejné předpony IP nebo obojí, Konfigurovatelný časový limit odchozí nečinnosti, vlastní SNAT přiřazování portů | Není k dispozici. |
|  [Resetování TCP v nečinnosti](../articles/load-balancer/load-balancer-tcp-reset.md) | Povolit TCP vynulují časového limitu nečinnosti (TCP RVNÍ) u libovolného pravidla | Není k dispozici. |
| [Několik front-endů](../articles/load-balancer/load-balancer-multivip-overview.md) | Příchozí a [odchozí](../articles/load-balancer/load-balancer-outbound-connections.md) | Pouze příchozí |
| Operace správy | Většina operací < 30 sekund | 60 – 90 sekund typické. |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v dobrém stavu. | [Implicitní v SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). | 
| Ceny | Účtuje podle počtu pravidel, zpracování dat příchozí a odchozí přidruženého prostředku.  | bez poplatků |
|  |  |  |
