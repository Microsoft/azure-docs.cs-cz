---
title: zahrnout soubor
description: zahrnout soubor
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 4219df03f74f737c5f2435f9bc0842189dc1fd49
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909162"
---
| | Standardní SKU | Základní SKU |
| --- | --- | --- |
| Velikost fondu back-endu | Podporuje až 1000 instancí. | Podporuje až 300 instancí. |
| Koncové body fondu back-endu | Libovolný virtuální počítač v jedné virtuální síti, včetně blendů virtuálních počítačů, skupin dostupnosti a virtuálních počítačů s měřítkem. | Virtuální počítače v jedné skupině dostupnosti nebo v sadě škálování virtuálních počítačů. |
| [Sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Chování sondy stavu](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | Připojení TCP zůstávají v provozu při testování instance dolů __a__ na všech sondách. | Připojení TCP zůstávají v provozu při testování instance. Všechna připojení TCP se ukončí, když jsou všechny sondy mimo provoz. |
| Zóny dostupnosti | Back-endy zóny a oblasti front-endu pro příchozí a odchozí provoz. Mapování odchozích toků při selhání zóny. Vyrovnávání zatížení mezi zónami. | Není k dispozici |
| Diagnostika | Azure Monitor. Multidimenzionální metriky, včetně bajtů a čítačů paketů. Stav sondy stavu. Pokusy o připojení (TCP SYN). Stav odchozího připojení (úspěšné a neúspěšné toky SNAT). Aktivní měření roviny dat. | Azure Log Analytics jenom pro veřejné Load Balancer. Výstraha vyčerpání SNAT. Počet stavů back-end fondu |
| Porty HA | Interní nástroj pro vyrovnávání zatížení | Není k dispozici |
| Zabezpečení ve výchozím nastavení | Veřejné IP adresy, koncové body veřejných Load Balancer a interní Load Balancer koncových bodů se zavřou do příchozích toků, pokud to nepovoluje skupina zabezpečení sítě. Upozorňujeme, že interní provoz z virtuální sítě do interního nástroje pro vyrovnávání zatížení je stále povolen. | Ve výchozím nastavení otevřete. Skupina zabezpečení sítě je volitelná. |
| [Odchozí připojení](../articles/load-balancer/load-balancer-outbound-connections.md) | Můžete explicitně definovat odchozí NAT založené na fondu s [odchozími pravidly](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Pro jedno pravidlo vyrovnávání zatížení můžete použít více front-endu s časovým limitem pro vyrovnávání zatížení. Odchozí scénář se _musí_ explicitně vytvořit pro virtuální počítač, skupinu dostupnosti nebo sadu škálování virtuálního počítače pro použití odchozího připojení. K koncovým bodům služby virtuální sítě se dá získat přístup bez definování odchozího připojení a nepočítá se od zpracovaných dat. Všechny veřejné IP adresy, včetně služeb Azure PaaS, nejsou k dispozici jako koncové body služby virtuální sítě, musí být dostupné pomocí odchozího připojení a počítají se ke zpracování dat. Když virtuální počítač, Skupina dostupnosti nebo sada škálování virtuálního počítače zachová jenom interní Load Balancer, odchozí připojení přes výchozí SNAT nejsou dostupná. Místo toho použijte [odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md) . Odchozí programování SNAT závisí na protokolu transportu příchozího pravidla vyrovnávání zatížení. | Jeden front-end, vybraný náhodně, pokud je k dispozici více front-endy. Pokud virtuální počítač, Skupina dostupnosti nebo sada škálování virtuálních počítačů obsluhuje jenom interní Load Balancer, použije se výchozí SNAT. |
| [Odchozí pravidla](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativní Konfigurace odchozího překladu adres (NAT), používá veřejné IP adresy nebo předpony veřejných IP adres nebo obojí. Konfigurovatelný časový limit nečinnosti pro odchozí připojení (4-120 minut). Vlastní přidělení portu SNAT | Není k dispozici |
| [Resetování protokolu TCP při nečinnosti](../articles/load-balancer/load-balancer-tcp-reset.md) | Povolit resetování protokolu TCP (TCP RST) pro časový limit nečinnosti u libovolného pravidla | Není k dispozici |
| [Více front-endy](../articles/load-balancer/load-balancer-multivip-overview.md) | Příchozí a [odchozí](../articles/load-balancer/load-balancer-outbound-connections.md) | Pouze příchozí |
| Operace správy | Většina operací < 30 sekund | typických 60 až 90 sekund |
| Smlouva SLA | 99,99% pro cestu k datům se dvěma zdravými virtuálními počítači. | Nevztahuje se | 
| Ceny | Účtuje se podle počtu pravidel, příchozích a odchozích dat, která jsou přidružená k prostředku. | Bez poplatků |
|  |  |  |
