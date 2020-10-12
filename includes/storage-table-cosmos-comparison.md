---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 6cf6cc781a9fd5353f458c2317b51a47df779e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85942342"
---
Pokud aktuálně používáte službu Azure Table Storage, získáte přechodem na rozhraní API tabulky Azure Cosmos DB následující výhody:

|Příznak | Azure Table Storage | Rozhraní API tabulky Azure Cosmos DB |
| --- | --- | --- |
| Latence | Rychlá, bez horních omezení latence. | Latence pro čtení a zápis v řádu milisekund, podložená latencí pro čtení menší než 10 ms a latencí pro zápis menší než 15 ms na 99. percentilu, při libovolném škálování, kdekoli na světě. |
| Propustnost | Model variabilní propustnosti. Tabulky mají omezení škálovatelnosti 20 000 operací za sekundu. | Vysoká škálovatelnost s [vyhrazenou rezervovanou propustností na tabulku](../articles/cosmos-db/request-units.md), podložená smlouvami SLA. Účty nemají žádné horní omezení propustnosti a podporují více než 10 milionů operací za sekundu na tabulku. |
| Globální distribuce | Jedna oblast s jednou volitelnou čitelnou sekundární oblastí čtení pro vysokou dostupnost. Nemůžete zahájit převzetí služeb při selhání. | [Globální distribuce na klíč](../articles/cosmos-db/distribute-data-globally.md) od jedné po 30 a více oblastí. Podpora [automatického a ručního převzetí služeb při selhání](../articles/cosmos-db/high-availability.md) kdykoli a kdekoli na světě. |
| Indexování | PartitionKey a RowKey používají pouze primární index. Žádné sekundární indexy. | Automatické a úplné indexování u všech vlastností, žádná správa indexů. |
| Dotaz | Při provádění dotazu se používá index pro primární klíč, jinak dochází k prohledávání. | Dotazy mohou ke zrychlení použít výhod automatického indexování vlastností. |
| Konzistence | Silná v rámci primární oblasti. Nahodilá v rámci sekundární oblasti. | [Pět jasně definovaných úrovní konzistence](../articles/cosmos-db/consistency-levels.md) pro zajištění dostupnosti, latence, propustnosti a konzistence v závislosti na potřebách vaší aplikace. |
| Ceny | Optimalizované úložiště. | Optimalizovaná propustnost. |
| Smlouvy SLA | 99,99% dostupnost. | Smlouva SLA o 99,99% dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a [Nejlepší komplexní smlouvy SLA v oboru](https://azure.microsoft.com/support/legal/sla/cosmos-db/) týkající se obecné dostupnosti zajišťující 99,999% dostupnost čtení pro všechny účty databáze ve více oblastech. |
