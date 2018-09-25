---
title: zahrnout soubor
description: zahrnout soubor
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 8/8/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: c3b35666a4340818fa9fcabea25541cd5d27c13b
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060423"
---
| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-endu | Podporuje až 1 000 instancí | Podporuje až 100 instancí |
| Koncové body back-Endového fondu | jakýkoli virtuální počítač v jedné virtuální sítě, včetně kombinace virtuální počítače, skupiny dostupnosti, škálovacích sad virtuálních počítačů. | Nastavení virtuálních počítačů ve škálovací sadě nebo virtuální počítač jednoho dostupnosti. |
| [Sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonda stavu dolů chování](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Připojení TCP zůstat naživu na instanci test dolů __a__ na všechny testy dolů. | Připojení TCP zůstat naživu na instanci test mimo provoz. Všechna připojení TCP se ukončit na všechny testy dolů. |
| Zóny dostupnosti | V SKU Basic zónové a zónově redundantních front-endů pro příchozí a odchozí, odchozí toky mapování překonat selhání zóny Vyrovnávání zatížení mezi zónami. | Není k dispozici |
| Diagnostika | Azure Monitor, vícedimenzionálních metrik, včetně bajtů a čítače paketů, stav testu stavu, pokusy o připojení (TCP SYN), stav odchozí připojení (SNAT úspěšné i neúspěšné toky), měření roviny aktivních dat. | Azure Log Analytics pro veřejný Load balancer úrovně pouze SNAT vyčerpání výstrahy, počet stavu back-endový fond. |
| HA porty | Interní nástroj pro vyrovnávání zatížení | Není k dispozici. |
| Ve výchozím nastavení zabezpečení | Výchozí uzavřel za veřejné IP adresy a nástroje pro vyrovnávání zatížení koncové body a skupiny zabezpečení sítě musí použít na explicitní seznam povolených pro provoz do toku. | Výchozí otevřete skupinu zabezpečení sítě, které jsou volitelné. |
| [Odchozí připojení](../articles/load-balancer/load-balancer-outbound-connections.md) | Několik front-endů s za odhlásit pravidlo Vyrovnávání zatížení. Odchozí scénář _musí_ explicitně vytvořit pro virtuální počítač, abyste mohli použít odchozí připojení.  Koncové body služeb virtuální sítě dostupná bez odchozí připojení a ne započítávat zpracovaná data.  Všechny veřejné IP adresy, včetně služeb Azure PaaS, která není k dispozici jako koncové body služby virtuální sítě, musíte kontaktovat prostřednictvím odchozího připojení a počet směrem k zpracovaná data. Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, nejsou k dispozici odchozí připojení přes výchozí SNAT. Přenosový protokol konkrétní založené na protokolu Příchozí pravidlo Vyrovnávání zatížení je výstupní programování SNAT. | Jeden front-endu, když jsou k dispozici několik front-endů vybraného náhodně.  Když virtuální počítač obsluhuje jenom interní nástroj pro vyrovnávání zatížení, použije se výchozí SNAT. |
| [Odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativní odchozí NAT., včetně které veřejných IP adres nebo veřejné IP předpony, konfigurovatelné odchozí časový limit nečinnosti, vlastní SNAT port přidělení | Není k dispozici. |
|  [Resetování TCP v nečinnosti](../articles/load-balancer/load-balancer-tcp-reset.md) | Povolit TCP vynulují časového limitu nečinnosti (TCP RVNÍ) u libovolného pravidla | Není k dispozici. |
| [Několik front-endů](../articles/load-balancer/load-balancer-multivip-overview.md) | Příchozí a [odchozí](../articles/load-balancer/load-balancer-outbound-connections.md) | Pouze příchozí |
| Operace správy | Většina operací < 30 sekund | 60 – 90 sekund typické. |
| SLA | 99,99 % pro cestu k datům s dva virtuální počítače v dobrém stavu. | Implicitní v SLA k virtuálním počítačům. | 
| Ceny | Bude účtovat podle počtu pravidel, zpracování dat příchozí nebo odchozí přidruženého prostředku.  | bez poplatků |
|  |  |  |
