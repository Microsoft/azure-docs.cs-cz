---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941848"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB

Azure Cosmos DB implementuje přenosové protokoly běžných databází NoSQL, včetně MongoDB. Z důvodu implementace přenosového protokolu můžete transparentně komunikovat s Azure Cosmos DB pomocí stávajících klientských sad SDK, ovladačů a nástrojů, které fungují s databázemi NoSQL. Azure Cosmos DB nepoužívá žádný zdrojový kód databází pro poskytování rozhraní API kompatibilních s drátem pro žádnou z databází NoSQL. Libovolný ovladač klienta MongoDB, který rozumí verzi přenosných protokolů, se může připojit k Azure Cosmos DB.

Azure Cosmos DB i když je rozhraní API pro MongoDB kompatibilní s 3,2 verzí přenosového protokolu MongoDB (operátory dotazů a funkce přidané ve verzi 3,4 jsou momentálně dostupné jako verze Preview), existují některé vlastní chybové kódy, které odpovídají Azure Cosmos DB specifickým chybám. Tento článek popisuje různé chyby, kódy chyb a postup řešení těchto chyb.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Chyba               | kód  | Popis  | Řešení  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Celkový počet spotřebovaných jednotek žádostí je vyšší než stanovená sazba žádosti-jednotka pro kolekci a byla omezena. | Zvažte možnost škálování propustnosti přiřazené kontejneru nebo sady kontejnerů ze Azure Portal nebo můžete operaci zopakovat. |
| ExceededMemoryLimit | 16501 | Jako služba pro více tenantů se operace převzala v průběhu plnění paměti klienta. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Cesta k indexu odpovídající určenému pořadí podle položky je vyloučena/dotaz ORDER by nemá odpovídající složený index, ze kterého může být obsluhován. | 2 | Dotaz požádá o řazení pro pole, které není indexované. | Pro pokusy o řadicí dotaz vytvořte stejný index (nebo složený index). |
| Problémy s verzí přenosového protokolu MongoDB | - | Starší verze ovladačů MongoDB nemůžou v připojovacích řetězech rozpoznat název účtu Azure Cosmos. | Připojíte *AppName = @**account** @ * na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde název ***účtu*** je váš Cosmos DB název účtu. |


## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.

