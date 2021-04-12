---
title: Ladění výkonu a paměti pro Microsoft OPC Publisher
description: V tomto kurzu se naučíte, jak vyladit výkon a paměť OPC vydavatele.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787675"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Kurz: ladění výkonu a paměti vydavatele OPC

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Úprava výkonu
> * Úprava toku zpráv na paměťové prostředky

Při spuštění OPC vydavatele v produkčním prostředí je potřeba zvážit požadavky na výkon sítě (propustnost a latence) a prostředky paměti. OPC Publisher zpřístupňuje následující parametry příkazového řádku, které vám pomůžou splnit tyto požadavky:

* Kapacita fronty zpráv ( `mq` pro verzi 2,5 a nižší, není k dispozici ve verzi 2,6 `om` pro verzi 2,7)
* IoT Hub – interval odesílání ( `si` )
* IoT Hub velikost zprávy ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Úprava IoT Hub intervalu odesílání a IoT Hub velikost zprávy

`mq/om`Parametr určuje horní limit kapacity interní fronty zpráv. Tato fronta ukládá všechny zprávy do vyrovnávací paměti až po jejich odeslání do IoT Hub. Výchozí velikost fronty je až 2 MB pro OPC Publisher verze 2,5 a nižší a 4000 IoT Hub zprávy pro verzi 2,7 (tj. Pokud nastavení pro IoT Hub velikost zprávy je 256 KB), bude velikost fronty až 1 GB. Pokud OPC Publisher nemůže odesílat zprávy, aby IoT Hub dostatečně rychle, počet položek v této frontě se zvýší. Pokud k tomu dojde během testovacího běhu, je možné provést jednu z následujících akcí:

* snížit IoT Hub interval odesílání ( `si` )

* Zvětšete velikost zprávy IoT Hub ( `ms` maximální hodnota může být nastavená na 256 KB).

Pokud je fronta stále rostoucí, i když `si` byly `ms` nastaveny parametry a, nakonec se dosáhne maximální kapacity fronty a ztratí se zprávy. Důvodem je skutečnost, že `si` `ms` parametr a má fyzické limity a připojení k internetu mezi vydavatelem OPC a IoT Hub není pro počet zpráv, které musí být v daném scénáři odesíláno, dostatečně rychlé. V takovém případě vám pomůže jenom nastavení několika paralelně OPC vydavatelů. `mq/om`Parametr má také největší dopad na paměť, kterou OPC Publisher spotřebovává. 

`si`Parametr vynutí, aby Vydavatel OPC odesílal zprávy do IoT Hub v zadaném intervalu. Zpráva se pošle buď v případě, že je k dispozici maximální velikost zprávy IoT Hub 256 KB (aktivuje se interval odesílání pro resetování), nebo když uplynul zadaný čas.

`ms`Parametr umožňuje dávkování zpráv odesílaných do IoT Hub. Ve většině síťových nastavení je latence odeslání jedné zprávy na IoT Hub vysoká, v porovnání s časem potřebným k přenosu datové části. Důvodem je hlavně požadavky na technologii QoS (Quality of Service), protože zprávy jsou potvrzeny až po jejich zpracování IoT Hub). Proto platí, že pokud je přijatelné zpoždění dat, která se mají dorazit na IoT Hub, musí být Vydavatel OPC nakonfigurovaný tak, aby používal maximální velikost zprávy 256 KB nastavením `ms` parametru na hodnotu 0. Je to také nejúčinnější způsob, jak používat OPC Publisher.

Výchozí konfigurace odesílá data do IoT Hub každých 10 sekund () nebo v případě, že jsou `si=10` k dispozici 256 KB IoT Hub data zprávy `ms=0` . To přidá maximální prodlevu 10 sekund, ale má nízkou pravděpodobnost ztráty dat z důvodu velké velikosti zprávy. Metrika `monitored item notifications enqueue failure`  v OPC Publisher verze 2,5 a nižší a `messages lost` v OPC Publisher verze 2,7 ukazuje, kolik zpráv bylo ztraceno.

Když `si` jsou oba `ms` parametry a nastavené na 0, OPC Publisher odešle zprávu, která se IoT Hub, jakmile budou data k dispozici. Výsledkem je průměrná IoT Hub velikost zprávy přesně přes 200 bajtů. Výhodou této konfigurace ale je, že OPC Publisher odesílá data z připojeného assetu bez zpoždění. Počet ztracených zpráv bude vysoký pro případy použití, kdy je potřeba publikovat velké množství dat, takže to není pro tyto scénáře doporučováno.

Pro měření výkonu OPC vydavatele `di` lze použít parametr pro tisk metrik výkonu do protokolu trasování v zadaném intervalu (v sekundách).

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak vyladit výkon a paměť OPC vydavatele, si můžete prohlédnout úložiště GitHubu OPC pro další prostředky:

> [!div class="nextstepaction"]
> [Úložiště GitHub pro OPC vydavatele](https://github.com/Azure/Industrial-IoT)