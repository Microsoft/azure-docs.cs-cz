---
title: Škálovatelnost – Centra událostí Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak škálovat Centra událostí Azure pomocí oddílů a jednotky propustnost.
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
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280947"
---
# <a name="scaling-with-event-hubs"></a>Škálování pomocí rozbočovačů událostí

Existují dva faktory, které ovlivňují škálování pomocí centra událostí.
*   Jednotky propustnosti
*   Oddíly

## <a name="throughput-units"></a>Jednotky propustnosti

Kapacita propustnosti event hubů je řízena *jednotkami propustnosti*. Jednotky propustnosti jsou předem zakoupené jednotky kapacity. Jedna propustnost umožňuje:

* Příchozí přenos dat: Až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dříve).
* Odchozí přenos: Až 2 MB za sekundu nebo 4096 událostí za sekundu.

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Ke správě jednotek propustnosti můžete využít okno **Škálování** oboru názvů na webu [Azure Portal](https://portal.azure.com). Jednotky propustNOST můžete také spravovat programově pomocí [api centra událostí](event-hubs-api-overview.md).

Jednotky propustnost jsou předem zakoupeny a fakturují se za hodinu. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Pro obor názvů Event Hubs lze zakoupit až 20 jednotek propustností a jsou sdíleny ve všech rozbočovačích událostí v tomto oboru názvů.

Funkce **automatického nafouknutí** centra událostí se automaticky zvětšuje zvýšením počtu jednotek propustností, aby byly splněny potřeby využití. Zvýšení jednotky propustnost zabraňuje omezení scénáře, ve kterém:

- Rychlost příchozího přenosu dat překračuje jednotky nastavené propustnosti.
- Rychlost požadavků na odchozí přenos dat překračuje jednotky nastavené propustnosti.

Služba Event Hubs zvyšuje propustnost při zvýšení zatížení nad minimální prahovou hodnotu, aniž by došlo k selhání požadavků s chybami ServerBusy. 

Další informace o funkci automatického nafouknutí naleznete v tématu [Automaticky škálovat jednotky propustnost .](event-hubs-auto-inflate.md)

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Klíč oddílu

[Klíč oddílu](event-hubs-programming-guide.md#partition-key) můžete použít k mapování dat příchozích událostí do konkrétních oddílů pro účely organizace dat. Klíč oddílu je hodnota zadaná odesílatelem, která byla předaná do centra událostí. Zpracovává se pomocí statické hashovací funkce, která vytvoří přiřazení k oddílu. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování.

Zdroj události zná jenom svůj klíč oddílu, a ne oddíl, do kterého se události publikují. Díky tomuto oddělení klíče a oddílu odesílatel toho nepotřebuje vědět o zpracování příjmu dat příliš mnoho. Vhodným klíčem oddílu je jedinečná identita uživatele nebo zařízení, ale k seskupení souvisejících událostí do jednoho oddílu je možné použít i další atributy, například geografickou polohu.


## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md)
- [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
