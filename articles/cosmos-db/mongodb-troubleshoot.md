---
title: Řešení běžných chyb v rozhraní API Azure Cosmos DB pro Mongo DB
description: Tento dokument popisuje způsoby řešení běžných potíží, ke kterým došlo v rozhraní Azure Cosmos DB API pro MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661269"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Řešení běžných problémů v rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Následující článek popisuje běžné chyby a řešení pro nasazení pomocí rozhraní Azure Cosmos DB API pro MongoDB.

>[!Note]
> Azure Cosmos DB nehostuje modul MongoDB. Poskytuje implementaci MongoDB [přenosového protokolu verze 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)a podpora starší verze [protokolu pro přenosovou 3,2 verzi](mongodb-feature-support.md). Proto se některé z těchto chyb nacházejí pouze v rozhraní Azure Cosmos DB API pro MongoDB.

## <a name="common-errors-and-solutions"></a>Běžné chyby a řešení

| Kód       | Chyba                | Popis  | Řešení  |
|------------|----------------------|--------------|-----------|
| 2 | Cesta k indexu odpovídající určenému pořadí podle položky je vyloučena nebo dotaz ORDER by nemá odpovídající složený index, ze kterého může být obsluhován. | Dotaz vyžaduje řazení podle pole, které není indexované. | Vytvořte pro dotaz řazení, který se pokoušíte provést, odpovídající index (nebo složený index). |
| 13 | Neautorizováno | Požadavek nemá oprávnění potřebná k dokončení. | Ujistěte se, že jste pro databázi a kolekci nastavili správná oprávnění.  |
| 16 | InvalidLength | Zadaná žádost má neplatnou délku. | Pokud používáte funkci vysvětlit (), ujistěte se, že zadáváte pouze jednu operaci. |
| 26 | NamespaceNotFound | Nepodařilo se najít databázi nebo kolekci, na kterou se odkazuje v dotazu. | Ujistěte se, že název vaší databáze nebo kolekce přesně odpovídá názvu ve vašem dotazu.|
| 50 | ExceededTimeLimit | Požadavek překročil 60sekundový časový limit provádění. |  Tato chyba může mít celou řadu příčin. Jednou z možných příčin je nedostatečná kapacita aktuálně přidělených jednotek žádostí k dokončení požadavku. Tento problém je možné vyřešit zvýšením počtu jednotek žádostí dané kolekce nebo databáze. V ostatních případech je možné tuto chybu obejít rozdělením velkého požadavku na menší požadavky. Zopakováním operace zápisu, u které došlo k této chybě, může dojít k duplicitnímu zápisu. <br><br>Pokud se pokoušíte odstranit velký objem dat, aniž by to mělo vliv na RU: <br>– Zvažte použití hodnoty TTL (na základě časového razítka): [vypršení platnosti dat pomocí rozhraní API Azure Cosmos DB pro MongoDB](./mongodb-time-to-live.md) . <br>– K provedení odstranění použijte velikost kurzoru nebo dávky. S využitím smyčky můžete postupně načíst jednotlivé dokumenty a odstranit je. Tímto způsobem můžete pomalu provést odstranění dat, aniž by to mělo vliv na produkční aplikaci.|
| 61 | ShardKeyNotFound | Dokument ve vašem požadavku neobsahoval klíč horizontálního dělení kolekce (klíč oddílu Azure Cosmos DB). | Zajistěte, aby se v požadavku používal klíč horizontálního dělení kolekce.|
| 66 | ImmutableField | Požadavek se pokouší změnit neměnné pole. | pole ID jsou neměnné. Ujistěte se, že se Váš požadavek nepokusí aktualizovat toto pole. |
| 67 | CannotCreateIndex | Požadavek na vytvoření indexu není možné dokončit. | V rámci kontejneru je možné vytvořit až 500 indexů jednotlivých polí. Složený index může obsahovat až 8 polí (složené indexy se podporují ve verzi 3.6 nebo novější). |
| 115 | CommandNotSupported | Požadavek, který se pokoušíte provést, se nepodporuje. | V této chybě by měly být uvedené další informace. Pokud je tato funkce pro vaše nasazení důležitá, dejte nám prosím v [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)vytvořením lístku podpory. |
| 11000 | DuplicateKey | Klíč horizontálního dělení (klíč oddílu Azure Cosmos DB) dokumentu, který vkládáte, již v kolekci existuje, nebo došlo k porušení omezení pro pole jedinečného indexu. | Pomocí funkce update() aktualizujte existující dokument. Pokud došlo k porušení omezení pro pole jedinečného indexu, vložte dokument s hodnotou pole, která v oddílu nebo horizontálním oddílu ještě neexistuje, případně dokument na takovou hodnotu aktualizujte. |
| 16500 | TooManyRequests  | Celkový počet spotřebovaných jednotek žádostí je vyšší než zřízený počet jednotek žádostí pro kolekci, a proto došlo k omezení. | Zvažte škálování propustnosti přiřazené kontejneru nebo sadě kontejnerů na webu Azure Portal, případně můžete zkusit operaci zopakovat. Pokud [povolíte SSR](prevent-rate-limiting-errors.md) (opakování na straně serveru), Azure Cosmos DB automaticky opakuje požadavky, které selhaly kvůli této chybě. |
| 16501 | ExceededMemoryLimit | Vzhledem k tomu, že se jedná o víceklientskou službu, znamená to, že operace překročila příděl paměti klienta. | Omezte rozsah operace použitím přísnějších kritérií dotazu nebo kontaktujte podporu na webu [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Příklad: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nerozpoznaný název fáze kanálu. | Název fáze v požadavku vašeho kanálu agregace se nerozpoznal. | Zajistěte, aby všechny názvy v kanálu agregace ve vašem požadavku byly platné. |
| - | Problémy s verzí přenosového protokolu MongoDB | Starší verze ovladačů MongoDB nedokáží v připojovacích řetězcích rozpoznat název účtu služby Azure Cosmos. | Připojíte *AppName = @**account** @* na konci rozhraní API Cosmos DB pro připojovací řetězec MongoDB, kde název ***účtu*** je váš Cosmos DB název účtu. |
| - | Problémy se sítěmi klienta MongoDB (například se sokety nebo výjimkami endOfStream)| Síťový požadavek selhal. Příčinou je často neaktivní připojení TCP, které se klient MongoDB pokouší použít. Ovladače MongoDB často využívají sdružování připojení, a pro požadavky se proto používají náhodná připojení z fondu. K vypršení časového limitu neaktivních připojení na straně služby Azure Cosmos DB obvykle dochází po 4 minutách. | Tyto neúspěšné požadavky můžete buď opakovat v kódu aplikace, změnit nastavení klienta MongoDB (Driver) tak, aby rozboru neaktivní připojení TCP před časovým intervalem čtyř minut, nebo nakonfigurovat nastavení kontroly stavu systému pro správu a údržbu tak, aby se připojení TCP v aktivním stavu zachovalo.<br><br>Pokud chcete zprávám o připojení zabránit, můžete změnit připojovací řetězec a nastavit hodnotu maxConnectionIdleTime na 1 nebo 2 minuty.<br>-Mongo ovladač: konfigurace *maxIdleTimeMS = 120000 do* <br>-Node.JS: Nakonfigurujte *socketTimeoutMS = 120000 do*, *autoReconnect* = true, *naživu* = true, *keepAliveInitialDelay* = 3 minuty.

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.