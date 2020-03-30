---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71839222"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |1,2 TB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Azure Cache pro repliky Redis, pro vysokou dostupnost |1 |
| Úlomky v prémiové mezipaměti s clusteringem |10 |

Azure Cache pro omezení a velikosti Redis se liší pro každou cenovou úroveň. Informace o cenových úrovních a jejich přidružených velikostech najdete v [tématu Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/).

Další informace o limitech konfigurace Azure Cache for Redis najdete [v tématu Default Redis server configuration](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Vzhledem k tomu, že konfiguraci a správu instancí Azure Cache for Redis provádí Microsoft, ne všechny příkazy Redis jsou podporované v Azure Cache for Redis. Další informace najdete v tématu [Redis příkazy nejsou podporovány v Azure Cache for Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

