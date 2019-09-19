---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175411"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |530 GB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Mezipaměť Azure pro repliky Redis pro vysokou dostupnost |1 |
| Horizontální oddíly takovým premium mezipaměť pomocí clusterování |10 |

Omezuje mezipaměti Azure Redis a velikosti se liší u jednotlivých cenových úrovní. Pokud chcete zobrazit cenové úrovně a jejich přidružené velikosti, přečtěte si článek [Azure cache for Redis Price](https://azure.microsoft.com/pricing/details/cache/).

Další informace o mezipaměti Azure Redis konfigurace omezení, najdete v části [výchozí konfigurace serveru Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Protože společnost Microsoft se provádí konfigurace a správy mezipaměti Azure pro instance Redis, ne všechny příkazy Redis podporují v mezipaměti Azure Redis. Další informace najdete v tématu [Redis nepodporuje v mezipaměti Azure Redis příkazy](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

