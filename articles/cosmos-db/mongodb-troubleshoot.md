---
title: Řešení běžných chyb v rozhraní API služby Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných problémů v Azure Cosmos DB přes rozhraní API pro MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735703"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných potíží v Azure Cosmos DB přes rozhraní API pro MongoDB

Azure Cosmos DB implementuje přenosový protokoly common databází NoSQL, včetně MongoDB. Kvůli implementaci protokolu přenosu můžete transparentně pracovat s Azure Cosmos DB pomocí existující klientské sady SDK, ovladačů a nástrojů, které fungují s databází NoSQL. Azure Cosmos DB nepoužívá žádný zdrojový kód databází k zajištění přenosu kompatibilní s rozhraním API pro žádnou z databází NoSQL. Všechny ovladače klienta MongoDB, která analyzuje verze protokolu přenosu můžete připojit ke službě Azure Cosmos DB.

Přestože rozhraní API služby Azure Cosmos DB pro MongoDB je kompatibilní s 3.2 verzi protokolu přenosu MongoDB. (funkce přidané do verze 3.4 a operátorů dotazu jsou aktuálně k dispozici jako verze preview), existují některé vlastní chybové kódy, které odpovídají ke službě Azure Cosmos DB konkrétní chyby. Tento článek popisuje různé chyby, kódy chyb a kroky k vyřešení těchto chyb.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               | Kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je větší než počet zřízených jednotky žádosti pro kolekci a se omezila. | Zvažte možnost škálování propustnosti přiřazené ke kontejneru nebo sadu kontejnerů na webu Azure Portal nebo můžete operaci opakovat. |
| ExceededMemoryLimit | 16501 | Jako víceklientská služba operace přešel přes klienta přidělení paměti. | Redukujte obor operaci prostřednictvím více omezující kritéria dotazu nebo se obraťte na podporu – od [webu Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problémy s verzí při přenosu MongoDB | - | Starší verze ovladače MongoDB se nám rozpoznat název účtu Azure Cosmos v připojovacích řetězcích. | Připojit *appName = @**accountName** @*  na konci rozhraní API Cosmos DB pro MongoDB připojovací řetězec, ve kterém ***accountName*** je název vašeho účtu služby Cosmos DB . |


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použití studia 3T](mongodb-mongochef.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [použít Robo 3T](mongodb-robomongo.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.
- Prozkoumejte MongoDB [ukázky](mongodb-samples.md) s rozhraním API služby Azure Cosmos DB pro MongoDB.

