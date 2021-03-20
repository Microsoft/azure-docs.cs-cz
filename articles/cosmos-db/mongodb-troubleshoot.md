---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692228"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Následující článek popisuje běžné chyby a řešení pro nasazení pomocí rozhraní Azure Cosmos DB API pro MongoDB.

>[!Note]
> Azure Cosmos DB nehostuje modul MongoDB. Poskytuje implementaci MongoDB [přenosového protokolu verze 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)a podpora starší verze [protokolu pro přenosovou 3,2 verzi](mongodb-feature-support.md). Proto se některé z těchto chyb nacházejí pouze v rozhraní Azure Cosmos DB API pro MongoDB.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Kód       | Chyba                | Popis  | Řešení  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Jednou z běžných příčin je, že cesta indexu odpovídající zadané položce řazení je vyloučená nebo dotaz řazení nemá odpovídající složený index, který by ho mohl obsloužit. Dotaz vyžaduje řazení podle pole, které není indexované. | Vytvořte pro dotaz řazení, který se pokoušíte provést, odpovídající index (nebo složený index). |
| 2 | Transakce není aktivní | Transakce s více dokumenty překročila pevný časový limit 5 sekund. | Zopakujte transakci s více dokumenty nebo omezte rozsah operací v rámci transakce s více dokumenty, aby se mohla dokončit během časového limitu 5 sekund. |
| 13 | Neautorizováno | Požadavek nemá oprávnění potřebná k dokončení. | Ujistěte se, že používáte správné klíče.  |
| 26 | NamespaceNotFound | Nepodařilo se najít databázi nebo kolekci, na kterou se odkazuje v dotazu. | Ujistěte se, že název vaší databáze nebo kolekce přesně odpovídá názvu ve vašem dotazu.|
| 50 | ExceededTimeLimit | Požadavek překročil 60sekundový časový limit provádění. |  Tato chyba může mít celou řadu příčin. Jednou z možných příčin je nedostatečná kapacita aktuálně přidělených jednotek žádostí k dokončení požadavku. Tento problém je možné vyřešit zvýšením počtu jednotek žádostí dané kolekce nebo databáze. V ostatních případech je možné tuto chybu obejít rozdělením velkého požadavku na menší požadavky. Zopakováním operace zápisu, u které došlo k této chybě, může dojít k duplicitnímu zápisu. <br><br>Pokud se pokoušíte odstranit velký objem dat, aniž by to mělo vliv na RU: <br>Zvažte použití hodnoty TTL (na základě časového razítka): [Vypršení platnosti dat s využitím rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md). <br>Použijte k odstranění kurzor a velikost dávky. S využitím smyčky můžete postupně načíst jednotlivé dokumenty a odstranit je. Tímto způsobem můžete pomalu provést odstranění dat, aniž by to mělo vliv na produkční aplikaci.|
| 61 | ShardKeyNotFound | Dokument ve vašem požadavku neobsahoval klíč horizontálního dělení kolekce (klíč oddílu Azure Cosmos DB). | Zajistěte, aby se v požadavku používal klíč horizontálního dělení kolekce.|
| 66 | ImmutableField | Požadavek se pokouší změnit neměnné pole. | pole "_id" jsou neměnné. Zajistěte, aby se váš požadavek nepokoušel aktualizovat toto pole nebo pole klíče horizontálního dělení. |
| 67 | CannotCreateIndex | Požadavek na vytvoření indexu není možné dokončit. | V rámci kontejneru je možné vytvořit až 500 indexů jednotlivých polí. Složený index může obsahovat až 8 polí (složené indexy se podporují ve verzi 3.6 nebo novější). |
| 112 | WriteConflict | Transakce s více dokumenty selhala kvůli konfliktní transakci s více dokumenty. | Opakujte transakci s více dokumenty, dokud se úspěšně nedokončí. |
| 115 | CommandNotSupported | Požadavek, který se pokoušíte provést, se nepodporuje. | V této chybě by měly být uvedené další informace. Pokud je tato funkce pro vaše nasazení důležitá, vytvořte v [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lístek podpory a Azure Cosmos DB tým se vám vrátí. |
| 11000 | DuplicateKey | Klíč horizontálního dělení (klíč oddílu Azure Cosmos DB) dokumentu, který vkládáte, již v kolekci existuje, nebo došlo k porušení omezení pro pole jedinečného indexu. | Pomocí funkce update() aktualizujte existující dokument. Pokud došlo k porušení omezení pro pole jedinečného indexu, vložte dokument s hodnotou pole, která v oddílu nebo horizontálním oddílu ještě neexistuje, případně dokument na takovou hodnotu aktualizujte. Další možností je použít pole obsahující kombinaci polí ID a klíče horizontálního dělení. |
| 16500 | TooManyRequests  | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízený počet jednotek žádostí pro kolekci, a proto došlo k omezení. | Zvažte škálování propustnosti přiřazené kontejneru nebo sadě kontejnerů na webu Azure Portal, případně můžete zkusit operaci zopakovat. Pokud povolíte opakování na straně serveru, Azure Cosmos DB bude automaticky opakovat požadavky, které selžou kvůli této chybě. |
| 16501 | ExceededMemoryLimit | Vzhledem k tomu, že se jedná o víceklientskou službu, znamená to, že operace překročila příděl paměti klienta. To platí pouze pro rozhraní API služby Azure Cosmos DB pro MongoDB verze 3.2. | Omezte rozsah operace použitím přísnějších kritérií dotazu nebo kontaktujte podporu na webu [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nerozpoznaný název fáze kanálu. | Název fáze v požadavku vašeho kanálu agregace se nerozpoznal. | Zajistěte, aby všechny názvy v kanálu agregace ve vašem požadavku byly platné. |
| - | Problémy s verzí přenosového protokolu MongoDB | Starší verze ovladačů MongoDB nedokáží v připojovacích řetězcích rozpoznat název účtu služby Azure Cosmos. | `appName=@accountName@`Na konci připojovacího řetězce přidejte, kde `accountName` je název vašeho účtu Azure Cosmos DB. |
| - | Problémy se sítěmi klienta MongoDB (například se sokety nebo výjimkami endOfStream)| Síťový požadavek selhal. Příčinou je často neaktivní připojení TCP, které se klient MongoDB pokouší použít. Ovladače MongoDB často využívají sdružování připojení, a pro požadavky se proto používají náhodná připojení z fondu. K vypršení časového limitu neaktivních připojení na straně služby Azure Cosmos DB obvykle dochází po 4 minutách. | V kódu vaší aplikace můžete tyto neúspěšné požadavky opakovat, můžete změnit nastavení klienta (ovladače) MongoDB tak, aby ukončoval neaktivní připojení TCP před uplynutím časového limitu 4 minut, nebo můžete nakonfigurovat nastavení `keepalive` vašeho operačního systému tak, aby se připojení TCP udržovala v aktivním stavu.<br><br>Pokud chcete zprávám o připojení zabránit, můžete změnit připojovací řetězec a nastavit hodnotu `maxConnectionIdleTime` na 1 nebo 2 minuty.<br>Ovladač Mongo: nakonfigurujte `maxIdleTimeMS=120000`. <br>Node.js: nakonfigurujte `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minuty.
| - | Na webu Azure Portal nefunguje prostředí Mongo Shell | Když se uživatel pokusí otevřít prostředí Mongo Shell, nic se nestane a karta zůstane prázdná.  | Zkontrolujte bránu firewall. Prostředí Mongo Shell na webu Azure Portal nepodporuje bránu firewall. <br>Nainstalujte prostředí Mongo Shell na místní počítač v rámci pravidel firewallu. <br>Použijte starší verzi prostředí Mongo Shell.
| - | Pomocí připojovacího řetězce se nejde připojit  | Při upgradu z verze 3.2 na verzi 3.6 došlo ke změně připojovacího řetězce. | Upozorňujeme, že při použití účtů rozhraní API služby Azure Cosmos DB pro MongoDB mají účty verze 3.6 koncový bod ve formátu `*.mongo.cosmos.azure.com`, zatímco účty verze 3.2 mají koncový bod ve formátu `*.documents.azure.com`.  

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
