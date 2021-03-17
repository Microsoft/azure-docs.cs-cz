---
title: Škálovatelnost – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak škálovat Event Hubs Azure pomocí oddílů a jednotek propustnosti.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4dacb24ace2332f590db54959cbf1f06694b982b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86521951"
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

### <a name="partition-key"></a>Klíč oddílu

[Klíč oddílu](event-hubs-programming-guide.md#partition-key) můžete použít k mapování příchozích dat událostí do konkrétních oddílů pro účely organizace dat. Klíč oddílu je hodnota zadaná odesílatelem, která byla předaná do centra událostí. Zpracovává se pomocí statické hashovací funkce, která vytvoří přiřazení k oddílu. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování.

Zdroj události zná jenom svůj klíč oddílu, a ne oddíl, do kterého se události publikují. Díky tomuto oddělení klíče a oddílu odesílatel toho nepotřebuje vědět o zpracování příjmu dat příliš mnoho. Vhodným klíčem oddílu je jedinečná identita uživatele nebo zařízení, ale k seskupení souvisejících událostí do jednoho oddílu je možné použít i další atributy, například geografickou polohu.


## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md)
- [Přehled služby Event Hubs](./event-hubs-about.md)
