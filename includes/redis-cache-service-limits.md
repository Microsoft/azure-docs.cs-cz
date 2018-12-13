---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 71d19b5d922616944c176cdda98470607c515af9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111926"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |530 GB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Mezipaměť Azure Redis replik (pro zajištění vysoké dostupnosti) |1 |
| Horizontální oddíly takovým premium mezipaměť pomocí clusterování |10 |

Omezuje mezipaměti Azure Redis a velikosti se liší u jednotlivých cenových úrovní. Cenové úrovně a jejich přidružené velikostí najdete v tématu [mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).

Další informace o mezipaměti Azure Redis konfigurace omezení, najdete v části [výchozí konfigurace serveru Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Protože společnost Microsoft se provádí konfigurace a správy mezipaměti Azure pro instance Redis, ne všechny příkazy Redis podporují v mezipaměti Azure Redis. Další informace najdete v tématu [Redis nepodporuje v mezipaměti Azure Redis příkazy](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

