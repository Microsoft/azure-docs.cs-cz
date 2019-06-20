---
title: Škálovatelnost, Azure Event Hubs | Dokumentace Microsoftu
description: Poskytuje informace o tom, jak škálovat služby Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276779"
---
# <a name="scaling-with-event-hubs"></a>Škálování pomocí služby Event Hubs

Existují dva faktory, které ovlivňují škálování pomocí služby Event Hubs.
*   Jednotky propustnosti
*   Oddíly

## <a name="throughput-units"></a>Jednotky propustnosti

Kapacita propustnosti je ve službě Event Hubs řízená prostřednictvím *jednotek propustnosti*. Jednotky propustnosti jsou předem zakoupené jednotky kapacity. Jeden propustnost vám umožní:

* Příchozí data: Až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dřív).
* Odchozí data: Až 2 MB za sekundu nebo 4096 událostí za sekundu.

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Jednotky propustnosti můžete spravovat na **škálování** okně oboru názvů na [webu Azure portal](https://portal.azure.com). Můžete také spravovat programově pomocí jednotek propustnosti [rozhraní API Event Hubs](event-hubs-api-overview.md).

Jednotky propustnosti se kupují předem a se účtuje po hodinách. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Propustnost až 20 jednotek pro obor názvů služby Event Hubs můžete zakoupit a jsou sdílené ve všech centrech event hubs v tomto oboru názvů.

**Automatické rozšiřování** funkce Event Hubs se automaticky zvětší zvýšením počtu jednotek propustnosti, které bude vyhovovat požadavkům využití. Zvýšení jednotek propustnosti brání omezení scénáře, ve kterém:

- Sazby za příchozí přenos dat překročí sady jednotek propustnosti.
- Žádost o přenosy dat překročí sady jednotek propustnosti.

Služba Event Hubs se zvyšuje propustnost při zvýšení zátěže ze strany nad prahovou hodnotou minimální, bez jakékoli požadavky služeb při selhání s chybami ServerBusy. 

Další informace o automatické rozšiřování funkcí, přečtěte si téma [automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Oddíly

Oddíly umožňují přizpůsobit pro příjem dat zpracování. Z důvodu modelu oddělených příjemců je pravidlo, které nabízí Služba Event Hubs s oddíly které můžete horizontální navýšení kapacity při zpracování událostí současně. Centra událostí můžete mít až 32 oddíly.

Doporučujeme, abyste vyvážili jednotky propustnosti 1:1 a oddíly, abyste dosáhli optimálního škálování. Jednoho oddílu dovoluje zaručené příchozí a výchozí přenos maximálně jednu jednotku propustnosti. Přestože je možné dosáhnout vyšší propustnost na oddíl, není zaručeno, že výkon. To je důvod, proč důrazně doporučujeme, že počet oddílů v Centru událostí být větší než nebo rovna počtu jednotek propustnosti.

Zadaný celkovou propustnost, kterou plánujete nutnosti, víte, že počet jednotek propustnosti, které vyžadujete a minimální počet oddílů, ale počet oddílů byste měli mít? Vyberte počet oddílů podle stupněm paralelismu příjmu dat, které chcete dosáhnout, jakož i vašim potřebám propustnosti budoucí. Neplatí žádné poplatky pro počet oddílů, které máte v rámci centra událostí.

Podrobné informace o cenách služby Event Hubs najdete na stránce [Ceny služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Další postup
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md)
- [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
