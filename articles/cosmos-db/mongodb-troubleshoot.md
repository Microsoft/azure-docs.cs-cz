---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258180"
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
| 50 | ExceededTimeLimit | Požadavek překročil 60sekundový časový limit provádění. |  Tato chyba může mít mnoho příčin. Jednou z příčin je, že momentálně přidělená kapacita jednotek žádostí není dostačující k dokončení žádosti. Tento problém je možné vyřešit zvýšením počtu jednotek žádostí dané kolekce nebo databáze. V ostatních případech se tato chyba může vyřešit tak, že se velký požadavek rozdělí na menší. Opakování operace zápisu, která přijala tuto chybu, může způsobit duplicitní zápis. <br><br>Pokud se pokoušíte odstranit velké objemy dat, aniž by to ovlivnilo ru: <br>– Zvažte použití hodnoty TTL (na základě časového razítka): [vypršení platnosti dat pomocí rozhraní API Azure Cosmos DB pro MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) . <br>– K provedení odstranění použijte velikost kurzoru nebo dávky. S využitím smyčky můžete postupně načíst jednotlivé dokumenty a odstranit je. To vám pomůže pomalu odstranit data, aniž by to mělo vliv na produkční aplikaci.|
| 61 | ShardKeyNotFound | Dokument ve vaší žádosti neobsahoval klíč horizontálních oddílů kolekce (klíč oddílu Azure Cosmos DB). | Ujistěte se, že se v požadavku používá horizontálních oddílů klíč kolekce.|
| 66 | ImmutableField | Požadavek se pokouší změnit neměnné pole. | pole ID jsou neměnné. Ujistěte se, že se Váš požadavek nepokusí aktualizovat toto pole. |
| 67 | CannotCreateIndex | Požadavek na vytvoření indexu nelze dokončit. | V kontejneru lze vytvořit až 500 indexů s jedním polem. Do složeného indexu lze zahrnout maximálně osm polí (složené indexy jsou podporovány ve verzi 3.6 +). |
| 115 | CommandNotSupported | Pokusy o požadavek se nepodporují. | V této chybě by se měly zadat další podrobnosti. Pokud je tato funkce pro vaše nasazení důležitá, dejte nám prosím v [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)vytvořením lístku podpory. |
| 11000 | DuplicateKey | Klíč horizontálních oddílů (Azure Cosmos DB klíč oddílu) dokumentu, který vkládáte, už v kolekci existuje nebo je porušené omezení jedinečného pole indexu. | K aktualizaci existujícího dokumentu použijte funkci Update (). Pokud je omezení jedinečného pole indexu porušeno, vložte nebo aktualizujte dokument s hodnotou pole, která ještě neexistuje v horizontálních oddílů nebo oddílu. |
| 16500 | TooManyRequests  | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízený počet jednotek žádostí pro kolekci, a proto došlo k omezení. | Zvažte škálování propustnosti přiřazené kontejneru nebo sadě kontejnerů na webu Azure Portal, případně můžete zkusit operaci zopakovat. Pokud [povolíte SSR](prevent-rate-limiting-errors.md) (opakování na straně serveru), Azure Cosmos DB automaticky opakuje požadavky, které selhaly kvůli této chybě. |
| 16501 | ExceededMemoryLimit | Jako služba pro více tenantů se operace převzala v průběhu plnění paměti klienta. | Snižte rozsah operace prostřednictvím přísnějších kritérií dotazu nebo kontaktujte podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nerozpoznaný název fáze kanálu | Název fáze v žádosti o agregovaný kanál nebyl rozpoznán. | Zajistěte, aby všechny názvy kanálů agregace byly ve vaší žádosti platné. |
| - | Problémy s verzí přenosového protokolu MongoDB | Starší verze ovladačů MongoDB nemůžou v připojovacích řetězech rozpoznat název účtu Azure Cosmos. | Připojíte *AppName = @**account** @* na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde ***account**. _ je váš Cosmos DB název účtu. |
| - | MongoDB problémy se sítí klienta (například výjimky soketu nebo endOfStream)| Požadavek sítě se nezdařil. To je často způsobeno neaktivním připojením TCP, které se klient MongoDB pokouší použít. Ovladače MongoDB často využívají sdružování připojení, což má za následek náhodné připojení zvolené z fondu, který se používá pro požadavek. Neaktivním připojením obvykle vypršel časový limit Azure Cosmos DB končí po čtyřech minutách. | Tyto neúspěšné požadavky můžete buď opakovat v kódu aplikace, změnit nastavení klienta MongoDB (Driver) tak, aby rozboru neaktivní připojení TCP před časovým intervalem čtyř minut, nebo nakonfigurovat nastavení kontroly stavu systému pro správu a údržbu tak, aby se připojení TCP v aktivním stavu zachovalo.<br><br>Pokud chcete zprávám o připojení zabránit, můžete změnit připojovací řetězec a nastavit hodnotu maxConnectionIdleTime na 1 nebo 2 minuty.<br>-Mongo ovladač: Nakonfigurujte _maxIdleTimeMS = 120000 do * <br>-Node.JS: Nakonfigurujte *socketTimeoutMS = 120000 do*, *autoReconnect* = true, *naživu* = true, *keepAliveInitialDelay* = 3 minuty.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
