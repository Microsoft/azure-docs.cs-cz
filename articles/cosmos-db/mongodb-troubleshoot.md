---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 41d667f4e90114052a17c5707989634bbb5fc421
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719830"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB

Azure Cosmos DB implementuje přenosové protokoly běžných databází NoSQL, včetně MongoDB. Z důvodu implementace přenosového protokolu můžete transparentně komunikovat s Azure Cosmos DB pomocí stávajících klientských sad SDK, ovladačů a nástrojů, které fungují s databázemi NoSQL. Azure Cosmos DB nepoužívá žádný zdrojový kód databází pro poskytování rozhraní API kompatibilních s drátem pro žádnou z databází NoSQL. Libovolný ovladač klienta MongoDB, který rozumí verzi přenosných protokolů, se může připojit k Azure Cosmos DB.

Azure Cosmos DB i když je rozhraní API pro MongoDB kompatibilní s 3,2 verzí MongoDB přenosového protokolu (operátory a funkce dotazů přidané ve verzi 3,4 jsou momentálně dostupné jako verze Preview), existují některé vlastní chybové kódy, které odpovídají Azure Cosmos DB konkrétní chyby. Tento článek popisuje různé chyby, kódy chyb a postup řešení těchto chyb.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               | kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je větší než počet zřízených jednotky žádosti pro kolekci a se omezila. | Zvažte možnost škálování propustnosti přiřazené kontejneru nebo sady kontejnerů ze Azure Portal nebo můžete operaci zopakovat. |
| ExceededMemoryLimit | 16501 | Jako víceklientská služba operace přešel přes klienta přidělení paměti. | Redukujte obor operaci prostřednictvím více omezující kritéria dotazu nebo se obraťte na podporu – od [webu Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Cesta k indexu odpovídající určenému pořadí podle položky je vyloučena/dotaz ORDER by nemá odpovídající složený index, ze kterého může být obsluhován. | 2 | Dotaz požádá o řazení pro pole, které není indexované. | Pro pokusy o řadicí dotaz vytvořte stejný index (nebo složený index). |
| Problémy s verzí MongoDB drátu | - | Starší verze ovladačů MongoDB nemůžou v připojovacích řetězech rozpoznat název účtu Azure Cosmos. | Připojením *AppName = @**account**@* na konci rozhraní API služby Cosmos DB pro připojovací řetězec MongoDB, kde ***account*** Name je váš Cosmos DB název účtu. |


## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.

