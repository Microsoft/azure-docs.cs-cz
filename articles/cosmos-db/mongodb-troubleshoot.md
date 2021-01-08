---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: faf50899e5897a8f06cf0e24166abd303d24b491
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011374"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Následující článek popisuje běžné chyby a řešení pro nasazení pomocí rozhraní Azure Cosmos DB API pro MongoDB.

>[!Note]
> Azure Cosmos DB nehostuje modul MongoDB. Poskytuje implementaci MongoDB přenosového protokolu. Proto se některé z těchto chyb nacházejí pouze v rozhraní Azure Cosmos DB API pro MongoDB. 

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Kód       | Chyba                | Popis  | Řešení  |
|------------|----------------------|--------------|-----------|
| 2 | Cesta k indexu odpovídající určenému pořadí podle položky je vyloučena nebo dotaz ORDER by nemá odpovídající složený index, ze kterého může být obsluhován. | Dotaz vyžaduje řazení podle pole, které není indexované. | Pro pokusy o řadicí dotaz vytvořte stejný index (nebo složený index). |
| 13 | Neautorizováno | V žádosti chybí oprávnění k dokončení. | Ujistěte se, že jste pro databázi a kolekci nastavili správná oprávnění.  |
| 16 | InvalidLength | Zadaná žádost má neplatnou délku. | Pokud používáte funkci vysvětlit (), ujistěte se, že zadáváte pouze jednu operaci. |
| 26 | NamespaceNotFound | Databáze nebo kolekce, na kterou se odkazuje v dotazu, se nepovedlo najít. | Ujistěte se, že název databáze nebo kolekce přesně odpovídá názvu v dotazu.|
| 50 | ExceededTimeLimit | Požadavek překročil 60sekundový časový limit provádění. |  Tato chyba může mít mnoho příčin. Jednou z příčin je, že momentálně přidělená kapacita jednotek žádostí není dostačující k dokončení žádosti. Tento problém je možné vyřešit zvýšením počtu jednotek žádostí dané kolekce nebo databáze. V ostatních případech se tato chyba může vyřešit tak, že se velký požadavek rozdělí na menší.|
| 61 | ShardKeyNotFound | Dokument ve vaší žádosti neobsahoval klíč horizontálních oddílů kolekce (klíč oddílu Azure Cosmos DB). | Ujistěte se, že se v požadavku používá horizontálních oddílů klíč kolekce.|
| 66 | ImmutableField | Požadavek se pokouší změnit neměnné pole. | pole ID jsou neměnné. Ujistěte se, že se Váš požadavek nepokusí aktualizovat toto pole. |
| 67 | CannotCreateIndex | Požadavek na vytvoření indexu nelze dokončit. | V kontejneru lze vytvořit až 500 indexů s jedním polem. Do složeného indexu lze zahrnout maximálně osm polí (složené indexy jsou podporovány ve verzi 3.6 +). |
| 115 | CommandNotSupported | Pokusy o požadavek se nepodporují. | V této chybě by se měly zadat další podrobnosti. Pokud je tato funkce pro vaše nasazení důležitá, dejte nám prosím v [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)vytvořením lístku podpory. |
| 11000 | DuplicateKey | Klíč horizontálních oddílů (Azure Cosmos DB klíč oddílu) dokumentu, který vkládáte, už v kolekci existuje nebo je porušené omezení jedinečného pole indexu. | K aktualizaci existujícího dokumentu použijte funkci Update (). Pokud je omezení jedinečného pole indexu porušeno, vložte nebo aktualizujte dokument s hodnotou pole, která ještě neexistuje v horizontálních oddílů nebo oddílu. |
| 16500 | TooManyRequests  | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízený počet jednotek žádostí pro kolekci, a proto došlo k omezení. | Zvažte škálování propustnosti přiřazené kontejneru nebo sadě kontejnerů na webu Azure Portal, případně můžete zkusit operaci zopakovat. Pokud povolíte SSR (opakování na straně serveru), Azure Cosmos DB automaticky opakuje požadavky, které selhaly kvůli této chybě. |
| 16501 | ExceededMemoryLimit | Jako služba pro více tenantů se operace převzala v průběhu plnění paměti klienta. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nerozpoznaný název fáze kanálu | Název fáze v žádosti o agregovaný kanál nebyl rozpoznán. | Zajistěte, aby všechny názvy kanálů agregace byly ve vaší žádosti platné. |
| - | Problémy s verzí přenosového protokolu MongoDB | Starší verze ovladačů MongoDB nemůžou v připojovacích řetězech rozpoznat název účtu Azure Cosmos. | Připojíte *AppName = @**account** @* na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde název ***účtu*** je váš Cosmos DB název účtu. |

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
