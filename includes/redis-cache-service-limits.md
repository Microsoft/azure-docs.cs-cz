---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "71839222"
---
| Prostředek | Omezení |
| --- | --- |
| Velikost mezipaměti |1,2 TB |
| Databáze |64 |
| Maximální počet připojených klientů |40,000 |
| Mezipaměť Azure pro repliky Redis pro vysokou dostupnost |1 |
| Horizontálních oddílů v mezipaměti Premium s clusteringem |10 |

Mezipaměť Azure pro limity Redis a velikosti se u každé cenové úrovně liší. Pokud chcete zobrazit cenové úrovně a jejich přidružené velikosti, přečtěte si článek [Azure cache for Redis Price](https://azure.microsoft.com/pricing/details/cache/).

Další informace o omezeních konfigurace Azure cache pro Redis najdete v tématu [výchozí konfigurace serveru Redis](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Vzhledem k tomu, že konfigurace a Správa služby Azure cache pro instance Redis provádí Microsoft, nejsou všechny příkazy Redis podporované v mezipaměti Azure pro Redis. Další informace najdete v tématu [Redis příkazy nepodporované v mezipaměti Azure pro Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

