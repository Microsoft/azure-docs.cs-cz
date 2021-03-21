---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516986"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Co můžu s clusterem dosáhnout?

V případě Event Hubs clusteru závisí množství, které můžete ingestovat a streamování, na různých faktorech, jako jsou vaše producenti, spotřebitelé, rychlost ingestování a zpracování a mnohem víc. 

V následující tabulce jsou uvedeny výsledky srovnávacích testů, které jsme dosáhli během testování:

| Tvar datové části | Příjemců | Šířka pásma příchozího přenosu dat| Příchozí zprávy | Šířka pásma pro výstup | Odchozí zprávy | Celkem počet propustnosti | Počet propustnosti na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Dávky 100x1KB | 2 | 400 MB/s | 400 tisíc zprávy za sekundu | 800 MB/s | 800k zprávy za sekundu | 400 počet propustnosti | 100 počet propustnosti | 
| Dávky 10x10KB | 2 | 666 MB/s | zprávy 66.6 k/s | 1,33 GB/s | 133k zprávy za sekundu | 666 počet propustnosti | 166 počet propustnosti |
| Dávky 6x32KB | 1 | 1,05 GB/s | 34k zprávy za sekundu | 1,05 GB/s | 34k zprávy za sekundu | 1000 počet propustnosti | 250 počet propustnosti |

Při testování se použila následující kritéria:

- Použil se cluster Event Hubs s vyhrazenou vrstvou, který má čtyři jednotky kapacity (kapacitní jednotky). 
- Centrum událostí používané pro ingestování mělo 200 oddílů. 
- Data, která byla ingestovaná, obdrží dvě aplikace přijímače, které přijímají ze všech oddílů.

### <a name="can-i-scale-updown-my-cluster"></a>Můžu svůj cluster škálovat nahoru/dolů?

Po vytvoření se clustery účtují minimálně o 4 hodiny využití. V rámci verze Preview prostředí samoobslužného řízení můžete odeslat [žádost o podporu](https://ms.portal.azure.com/#create/Microsoft.Support) týmu Event Hubs v rámci žádosti o **technickou**  >  **kvótu**  >  **pro horizontální navýšení nebo snížení kapacity vyhrazeného clusteru** pro horizontální navýšení nebo snížení kapacity clusteru. Dokončení žádosti o horizontální navýšení kapacity clusteru může trvat až 7 dní. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Jak funguje geografická DR s mým clusterem?

Obor názvů můžete geograficky spárovat v rámci clusteru vyhrazené úrovně s jiným oborem názvů v rámci clusteru s vyhrazenou vrstvou. Nedoporučujeme spárovat obor názvů vyhrazené úrovně s oborem názvů v naší standardní nabídce, protože limit propustnosti bude nekompatibilní a bude mít za následek chyby. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Můžu migrovat standardní obory názvů tak, aby patřily do clusteru vyhrazené úrovně?
V současné době nepodporujeme proces automatizované migrace pro migraci dat centra událostí ze standardního oboru názvů na vyhrazený. 
