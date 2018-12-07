---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8a9322862d63d856f96729e5784b88e0ef098fb6
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53018488"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |530 GB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Mezipaměť Azure Redis replik (pro zajištění vysoké dostupnosti) |1 |
| Horizontální oddíly takovým premium mezipaměť pomocí clusterování |10 |

Omezení Azure mezipaměti Azure Redis a velikosti se liší u jednotlivých cenových úrovní. Cenové úrovně a jejich přidružené velikostí najdete v tématu [Azure mezipaměti Azure redis Cache ceny](https://azure.microsoft.com/pricing/details/cache/).

Další informace o Azure mezipaměti Azure Redis konfigurace omezení, najdete v části [výchozí konfigurace serveru Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Protože společnost Microsoft se provádí konfigurace a správy mezipaměti Azure Azure pro instance Redis, ne všechny příkazy Redis podporují v Azure mezipaměti Azure Redis. Další informace najdete v tématu [příkazy nejsou podporovány pro Redis v Azure mezipaměti Azure Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

