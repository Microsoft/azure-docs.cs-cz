---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409625"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB

Následující článek popisuje běžné chyby a řešení pro databáze pomocí rozhraní Azure Cosmos DB API pro MongoDB.

>[!Note]
> Azure Cosmos DB nehostuje modul MongoDB. Poskytuje implementaci MongoDB [přenosového protokolu verze 3,6](mongodb-feature-support-36.md) a starší verze protokolu MongoDB (Standard) [verze 3,2](mongodb-feature-support.md), proto se některé z těchto chyb nacházejí pouze v rozhraní Azure Cosmos DB API pro. 

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               | Kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | Požadavek překročil 60sekundový časový limit provádění. | Tato chyba může mít mnoho příčin. Jednou z možných příčin je nedostatečná aktuální kapacita přidělených jednotek žádostí k dokončení požadavku. Tento problém je možné vyřešit zvýšením počtu jednotek žádostí dané kolekce nebo databáze. V ostatních případech se tato chyba může vyřešit tak, že se velký požadavek rozdělí na menší. |
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízený počet jednotek žádostí pro kolekci, a proto došlo k omezení. | Zvažte škálování propustnosti přiřazené kontejneru nebo sadě kontejnerů na webu Azure Portal, případně můžete zkusit operaci zopakovat. |
| ExceededMemoryLimit | 16501 | Jako služba pro více tenantů se operace převzala v průběhu plnění paměti klienta. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Cesta indexu odpovídající zadané položce řazení je vyloučená / Dotaz řazení nemá odpovídající složený index, který by ho mohl obsloužit. | 2 | Dotaz vyžaduje řazení podle pole, které není indexované. | Pro pokusy o řadicí dotaz vytvořte stejný index (nebo složený index). |
| Problémy s verzí přenosového protokolu MongoDB | - | Starší verze ovladačů MongoDB nemůžou v připojovacích řetězech rozpoznat název účtu Azure Cosmos. | Připojíte *AppName = @**account** @ * na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde název ***účtu*** je váš Cosmos DB název účtu. |

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.

