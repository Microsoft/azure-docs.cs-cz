---
title: Škálovatelnost – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak škálovat Event Hubs Azure pomocí oddílů a jednotek propustnosti.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601270"
---
# <a name="scaling-with-event-hubs"></a>Škálování pomocí Event Hubs

Existují dva faktory, které ovlivňují škálování pomocí Event Hubs.
*   Jednotky propustnosti
*   Oddíly

## <a name="throughput-units"></a>Jednotky propustnosti

Kapacita propustnosti Event Hubs je řízena *jednotkami propustnosti*. Jednotky propustnosti jsou předem zakoupené jednotky kapacity. Jedna propustnost vám umožní:

* Příchozí přenos dat: až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dřív).
* Odchozí data: až 2 MB za sekundu nebo 4096 událostí za sekundu.

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Ke správě jednotek propustnosti můžete využít okno **Škálování** oboru názvů na webu [Azure Portal](https://portal.azure.com). Jednotky propustnosti můžete spravovat i programově pomocí [Event Hubs rozhraní API](./event-hubs-samples.md).

Jednotky propustnosti jsou předem zakoupené a účtují se za hodinu. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Pro obor názvů Event Hubs lze zakoupit až 20 jednotek propustnosti, které jsou sdíleny napříč všemi centry událostí v daném oboru názvů.

Funkce automaticky **rozšířené** Event Hubs se automaticky škáluje tak, že se zvýší počet jednotek propustnosti, aby se splnily požadavky na využití. Zvýšení jednotek propustnosti zabraňuje scénářům omezování, ve kterých:

- Míry příchozího přenosu dat překračují nastavené jednotky propustnosti.
- Míry požadavků na výstup dat přesahují nastavené jednotky propustnosti.

Služba Event Hubs zvyšuje propustnost, když se zatížení zvýší nad minimální prahovou hodnotu, aniž by došlo k selhání požadavků s ServerBusymi chybami. 

Další informace o funkci automatického rozplochení najdete v tématu [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md)
- [Přehled služby Event Hubs](./event-hubs-about.md)
