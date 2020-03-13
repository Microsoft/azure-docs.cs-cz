---
title: Škálovatelnost – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak škálovat Event Hubs Azure pomocí oddílů a jednotek propustnosti.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280947"
---
# <a name="scaling-with-event-hubs"></a>Škálování pomocí Event Hubs

Existují dva faktory, které ovlivňují škálování pomocí Event Hubs.
*   Jednotky propustnosti
*   Oddíly

## <a name="throughput-units"></a>Jednotky propustnosti

Kapacita propustnosti je ve službě Event Hubs řízená prostřednictvím *jednotek propustnosti*. Jednotky propustnosti jsou předem zakoupené jednotky kapacity. Jedna propustnost vám umožní:

* Příchozí data: Až 1 MB za sekundu nebo 1000 událostí za sekundu (podle toho, co nastane dřív).
* Odchozí data: Až 2 MB za sekundu nebo 4096 událostí za sekundu.

Nad rámec kapacity zakoupených jednotek propustnosti je příjem příchozích dat omezen a vrátí se výjimka [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Odchozí data nezpůsobují takové výjimky, ale jsou omezená na objem přenosu dat, který poskytují zakoupené jednotky propustnosti. Pokud se vám objevují výjimky související s frekvencí publikování nebo v budoucnu očekáváte větší objem odchozích dat, zkontrolujte, kolik jednotek propustnosti jste pro konkrétní obor názvů zakoupili. Jednotky propustnosti můžete spravovat v okně **škálování** oborů názvů v [Azure Portal](https://portal.azure.com). Jednotky propustnosti můžete spravovat i programově pomocí [Event Hubs rozhraní API](event-hubs-api-overview.md).

Jednotky propustnosti se kupují předem a se účtuje po hodinách. Zakoupené jednotky propustnosti se účtují minimálně za jednu hodinu. Propustnost až 20 jednotek pro obor názvů služby Event Hubs můžete zakoupit a jsou sdílené ve všech centrech event hubs v tomto oboru názvů.

Funkce automaticky **rozšířené** Event Hubs se automaticky škáluje tak, že se zvýší počet jednotek propustnosti, aby se splnily požadavky na využití. Zvýšení jednotek propustnosti brání omezení scénáře, ve kterém:

- Sazby za příchozí přenos dat překročí sady jednotek propustnosti.
- Žádost o přenosy dat překročí sady jednotek propustnosti.

Služba Event Hubs se zvyšuje propustnost při zvýšení zátěže ze strany nad prahovou hodnotou minimální, bez jakékoli požadavky služeb při selhání s chybami ServerBusy. 

Další informace o funkci automatického rozplochení najdete v tématu [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Oddíly
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Klíč oddílu

[Klíč oddílu](event-hubs-programming-guide.md#partition-key) můžete použít k mapování příchozích dat událostí do konkrétních oddílů pro účely organizace dat. Klíč oddílu je hodnota zadaná odesílatelem, která byla předaná do centra událostí. Zpracovává se pomocí statické hashovací funkce, která vytvoří přiřazení k oddílu. Pokud při publikování události nezadáte klíč oddílu, použije se přiřazení metodou kruhového dotazování.

Zdroj události zná jenom svůj klíč oddílu, a ne oddíl, do kterého se události publikují. Díky tomuto oddělení klíče a oddílu odesílatel toho nepotřebuje vědět o zpracování příjmu dat příliš mnoho. Vhodným klíčem oddílu je jedinečná identita uživatele nebo zařízení, ale k seskupení souvisejících událostí do jednoho oddílu je možné použít i další atributy, například geografickou polohu.


## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

- [Automatické škálování jednotek propustnosti](event-hubs-auto-inflate.md)
- [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
