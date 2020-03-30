---
title: Řešení problémů s běžnými chybami v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných problémů, které se vyskytly v rozhraní API Azure Cosmos DB pro MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941848"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní API Azure Cosmos DB pro MongoDB

Azure Cosmos DB implementuje drátové protokoly běžných databází NoSQL, včetně MongoDB. Díky implementaci drátového protokolu můžete transparentně pracovat s Azure Cosmos DB pomocí existujících sad SDK klienta, ovladačů a nástrojů, které pracují s databázemi NoSQL. Azure Cosmos DB nepoužívá žádný zdrojový kód databází pro poskytování rozhraní API kompatibilní s drátem pro některou z databází NoSQL. Každý klientský ovladač MongoDB, který rozumí verzím drátového protokolu, se může připojit k Azure Cosmos DB.

Zatímco rozhraní API Azure Cosmos DB pro MongoDB je kompatibilní s verzí 3.2 drátového protokolu MongoDB (operátory dotazů a funkce přidané ve verzi 3.4 jsou aktuálně k dispozici jako náhled), existují některé vlastní kódy chyb, které odpovídají Azure Cosmos DB konkrétní chyby. Tento článek vysvětluje různé chyby, kódy chyb a kroky k vyřešení těchto chyb.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               | kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek požadavku je větší než zřízená sazba jednotky požadavku pro kolekci a byla omezena. | Zvažte škálování propustnosti přiřazené ke kontejneru nebo sadě kontejnerů z portálu Azure nebo můžete operaci zopakovat. |
| ExceededMemoryLimit | 16501 | Jako služba s více tenanty operace přešla přes přidělení paměti klienta. | Zmenšete rozsah operace prostřednictvím přísnějších kritérií dotazu nebo se obraťte na podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Cesta indexu odpovídající zadané položce podle objednávky je vyloučena / Pořadí podle dotazu nemá odpovídající složený index, ze kterého může být obsluhován. | 2 | Dotaz požaduje řazení na pole, které není indexováno. | Vytvořte odpovídající index (nebo složený index) pro pokus o dotaz řazení. |
| Problémy s verzí přenosového protokolu MongoDB | - | Starší verze ovladačů MongoDB nejsou schopny rozpoznat název účtu Azure Cosmos v připojovacích řetězcích. | Append *appName=@**accountName** @ * na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde ***accountName*** je název účtu Cosmos DB. |


## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.

