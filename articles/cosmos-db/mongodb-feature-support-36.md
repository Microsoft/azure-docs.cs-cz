---
title: Podporované funkce a syntaxe rozhraní API pro MongoDB (verze 3,6) Azure Cosmos DB
description: Přečtěte si o podporovaných funkcích a syntaxi rozhraní API pro Azure Cosmos DB pro MongoDB (verze 3,6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: cde731f9d9e673446bc4d08117004b028db2a7f9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462458"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Rozhraní API Azure Cosmos DB pro MongoDB (verze 3,6): podporované funkce a syntaxe

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z open-source klientských [ovladačů](https://docs.mongodb.org/ecosystem/drivers)MongoDB. Rozhraní API pro Azure Cosmos DB pro MongoDB umožňuje použití existujících ovladačů klientů, které dodržuje MongoDB [síťový protokol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Díky rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody MongoDB, ke kterému jste se přihlásili, se všemi podnikovými funkcemi, které Cosmos DB poskytuje: [globální distribuce](distribute-data-globally.md), [Automatické horizontálního dělení](partition-data.md), dostupnost a záruky latence, šifrování v klidovém formátu, zálohování a spoustu dalšího.

## <a name="protocol-support"></a>Podpora protokolů

Rozhraní API Azure Cosmos DB pro MongoDB je ve výchozím nastavení kompatibilní s MongoDB serverem verze **3,6** pro nové účty. Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API služby Azure Cosmos DB pro MongoDB. Počítejte s tím, že při použití rozhraní API Azure Cosmos DB pro účty MongoDB má koncový bod 3,6 verze účtů ve formátu `*.mongo.cosmos.azure.com` zatímco 3,2 verze účtů má koncový bod ve formátu `*.documents.azure.com`.

## <a name="query-language-support"></a>Podpora jazyka dotazů

Rozhraní API pro Azure Cosmos DB MongoDB poskytuje komplexní podporu konstrukcí jazyka MongoDB pro dotazy. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API pro Azure Cosmos DB pro MongoDB podporuje následující databázové příkazy:

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace

|Příkaz  |Podporuje se |
|---------|---------|
|delete | Ano |
|find | Ano     |
|findAndModify | Ano  |
|getLastError|   Ano |
|getMore  |  Ano  |
|getPrevError | Ne  |
|insert  |   Ano  |
|parallelCollectionScan  | Ano   |
|resetError |   Ne  |
|update  |   Ano  |
|[Změna datových proudů](mongodb-change-streams.md)  |  Ano  |
|GridFS |   Ano  |

### <a name="authentication-commands"></a>Příkazy pro ověření

|Příkaz  |Podporuje se |
|---------|---------|
|authenticate    |   Ano      |
|logout    |      Ano   |
|getnonce   |    Ano     |


### <a name="administration-commands"></a>Příkazy pro správu

|Příkaz  |Podporuje se |
|---------|---------|
|Kolekce omezené   |   Ne      |
|cloneCollectionAsCapped     |   Ne      |
|collMod     |   Ne      |
|collMod: expireAfterSeconds   |   Ne      |
|convertToCapped   |  Ne       |
|copydb     |  Ne       |
|vytvoření   |    Ano     |
|createIndexes     |  Ano       |
|currentOp     |  Ano       |
|drop     |   Ano      |
|dropDatabase     |  Ano       |
|dropIndexes     |   Ano      |
|filemd5    |   Ano      |
|killCursors    |  Ano       |
|killOp     |   Ne      |
|listCollections     |  Ano       |
|listDatabases     |  Ano       |
|listIndexes     |  Ano       |
|reIndex     |    Ano     |
|Přejmenujte     |    Ne     |
|connectionStatus    |     Ne    |

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku

|Příkaz  |Podporuje se |
|---------|---------|
|buildInfo       |   Ano      |
|collStats    |  Ano       |
|connPoolStats     |  Ne       |
|connectionStatus     |  Ne       |
|dataSize     |   Ne      |
|dbHash    |    Ne     |
|dbStats     |   Ano      |
|částech     | Ne        |
|Vysvětlení: executionStats     |     Ne    |
|funkce     |    Ne     |
|hostInfo     |   Ne      |
|listDatabases       |   Ano      |
|listCommands     |  Ne       |
|profiler     |  Ne       |
|serverStatus     |  Ne       |
|vrchol     |    Ne     |
|whatsmyuri     |   Ano      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Agregační zřetězení</a>

### <a name="aggregation-commands"></a>Příkazy pro agregaci

|Příkaz  |Podporuje se |
|---------|---------|
|aggregate |   Ano  |
|count     |   Ano  |
|distinct  | Ano |
|mapReduce | Ne |

### <a name="aggregation-stages"></a>Fáze agregace

|Příkaz  |Podporuje se |
|---------|---------|
|$collStats |Ne|
|$project   |Ano|
|$match |Ano|
|$redact|   Ano|
|$limit |Ano|
|$skip  |Ano|
|$unwind|   Ano|
|$group |   Ano|
|$sample|       Ano|
|$sort  |Ano|
|$geoNear|  Ne|
|$lookup    |   Ano|
|$out       |Ano|
|$indexStats|       Ne|
|$facet |Ne|
|$bucket|   Ne|
|$bucketAuto|   Ne|
|$sortByCount|  Ano|
|$addFields |Ano|
|$replaceRoot|  Ano|
|$count |Ano|
|$currentOp|    Ne|
|$listLocalSessions |Ne|
|$listSessions  |Ne|
|$graphLookup   |Ne|

### <a name="boolean-expressions"></a>Logické výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$and| Ano|
|$or|Ano|
|$not|Ano|

### <a name="set-expressions"></a>Nastavovací výrazy

|Příkaz  |Podporuje se |
|---------|---------|
| $setEquals | Ano|
|$setIntersection|Ano|
| $setUnion|Ano|
| $setDifference|Ano|
| $setIsSubset|Ano|
| $anyElementTrue|Ano|
| $allElementsTrue|Ano|

### <a name="comparison-expressions"></a>Porovnávací výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$cmp     |  Ano       |
|$eq|   Ano| 
|$gt |  Ano| 
|$gte|  Ano| 
|$lt    |Ano|
|$lte|  Ano| 
|$ne    |   Ano| 
|$in    |   Ano| 
|$nin   |   Ano| 

### <a name="arithmetic-expressions"></a>Aritmetické výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$abs |  Ano       |
| $add |  Ano       |
| $ceil |  Ano       |
| $divide |  Ano       |
| $exp |  Ano       |
| $floor |  Ano       |
| $ln |  Ano       |
| $log |  Ano       |
| $log10 |  Ano       |
| $mod |  Ano       |
| $multiply |  Ano       |
| $pow |  Ano       |
| $sqrt |  Ano       |
| $subtract |  Ano       |
| $trunc |  Ano       |

### <a name="string-expressions"></a>Řetězcové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$concat |  Ano       |
| $indexOfBytes|  Ano       |
| $indexOfCP|  Ano       |
| $split|  Ano       |
| $strLenBytes|  Ano       |
| $strLenCP|  Ano       |
| $strcasecmp|  Ano       |
| $substr|  Ano       |
| $substrBytes|  Ano       |
| $substrCP|  Ano       |
| $toLower|  Ano       |
| $toUpper|  Ano       |

### <a name="text-search-operator"></a>Operátor hledání textu

|Příkaz  |Podporuje se |
|---------|---------|
| $meta | Ne|

### <a name="array-expressions"></a>Maticové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$arrayElemAt   |   Ano|
|$arrayToObject|    Ano|
|$concatArrays  |   Ano|
|$filter    |   Ano|
|$indexOfArray  |Ano|
|$isArray   |   Ano|
|$objectToArray |Ano|
|$range |Ano|
|$reverseArray  |   Ano|
|$reduce|   Ano|
|$size  |   Ano|
|$slice |   Ano|
|$zip   |   Ano|
|$in    |   Ano|

### <a name="variable-operators"></a>Operátory proměnných

|Příkaz  |Podporuje se |
|---------|---------|
|$map   |Ne|
|$let   |Ano|

### <a name="system-variables"></a>Systémové proměnné

|Příkaz  |Podporuje se |
|---------|---------|
|$ $CURRENT| Ano|
|$ $DESCEND|     Ano|
|$ $KEEP     |Ano|
|$ $PRUNE    |   Ano|
|$ $REMOVE   |Ano|
|$ $ROOT     |Ano|

### <a name="literal-operator"></a>Operátor literálu

|Příkaz  |Podporuje se |
|---------|---------|
|$literal   |Ano|

### <a name="date-expressions"></a>Datumové výrazy

|Příkaz  |Podporuje se |
|---------|---------|
|$dayOfYear |Ano    |
|$dayOfMonth|   Ano |
|$dayOfWeek |Ano    |
|$year  |Ano    |
|$month |Ano|   
|$week  |Ano    |
|$hour  |Ano    |
|$minute|   Ano|    
|$second    |Ano    |
|$millisecond|  Ano|    
|$dateToString  |Ano    |
|$isoDayOfWeek  |Ano    |
|$isoWeek   |Ano    |
|$dateFromParts|    Ne| 
|$dateToParts   |Ne |
|$dateFromString|   Ne|
|$isoWeekYear   |Ano    |

### <a name="conditional-expressions"></a>Podmíněné výrazy

|Příkaz  |Podporuje se |
|---------|---------|
| $cond| Ano|
| $ifNull| Ano|
| $switch |Ano|

### <a name="data-type-operator"></a>Operátor datového typu

|Příkaz  |Podporuje se |
|---------|---------|
| $type| Ano|

### <a name="accumulator-expressions"></a>Výrazy akumulovaných

|Příkaz  |Podporuje se |
|---------|---------|
|$sum   |Ano    |
|$avg   |Ano    |
|$first|    Ano|
|$last  |Ano    |
|$max   |Ano    |
|$min   |Ano    |
|$push| Ano|
|$addToSet| Ano|
|$stdDevPop|    Ne  |
|$stdDevSamp|   Ne|

### <a name="merge-operator"></a>Operátor sloučení

|Příkaz  |Podporuje se |
|---------|---------|
| $mergeObjects | Ano|

## <a name="data-types"></a>Typy dat

|Příkaz  |Podporuje se |
|---------|---------|
|Double |Ano    |
|Řetězec |Ano    |
|Objekt |Ano    |
|Pole  |Ano    |
|Binary Data    |Ano|   
|Objektu   |Ano    |
|Logická hodnota    |Ano    |
|Datum   |Ano    |
|Null   |Ano    |
|32 bitové celé číslo (int)   |Ano    |
|Časové razítko  |Ano    |
|64-bitové celé číslo (Long)  |Ano    |
|MinKey |Ano    |
|MaxKey |Ano    |
|Decimal128 |Ano|   
|Regulární výraz |Ano|
|JavaScript |Ano|
|JavaScript (s rozsahem)|   Ano |
|Nedefinováno  |Ano    |

## <a name="indexes-and-index-properties"></a>Index a vlastnosti indexu

### <a name="indexes"></a>Indexy

|Příkaz  |Podporuje se |
|---------|---------|
|Index jednoho pole |Ano    |
|Složený index |Ano    |
|Multikey index |Ano    |
|Textový index |Ne|
|2dsphere   |Ano    |
|2D index   |Ne |
|Index s hodnotou hash   | Ano|

### <a name="index-properties"></a>Vlastnosti indexu

|Příkaz  |Podporuje se |
|---------|---------|
|Hodnota TTL|   Ano |
|Jedinečná |Ano|
|Částečně|   Ne|
|Nerozlišuje malá a velká písmena.   |Ne|
|Řídké |Ne |
|Pozadí|    Ano |

## <a name="operators"></a>Operátory

### <a name="logical-operators"></a>Logické operátory

|Příkaz  |Podporuje se |
|---------|---------|
|$or    |   Ano|
|$and   |   Ano|
|$not   |   Ano|
|$nor   |   Ano| 

### <a name="element-operators"></a>Operátory elementu

|Příkaz  |Podporuje se |
|---------|---------|
|$exists|   Ano|
|$type  |   Ano|

### <a name="evaluation-query-operators"></a>Operátory dotazování pro vyhodnocení

|Příkaz  |Podporuje se |
|---------|---------|
|$expr  |   Ne|
|$jsonSchema    |   Ne|
|$mod   |   Ano|
|$regex |   Ano|
|$text  | Ne (není podporováno. Místo toho použijte $regex.)| 
|$where |Ne| 

V $regexch dotazech jsou výrazy ukotvené zleva povoleny hledání v indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.

Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například s ohledem na následující původní dotaz: ```find({x:{$regex: /^abc$/})```, musí být upraveno následujícím způsobem:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operátory pole

|Příkaz  |Podporuje se | 
|---------|---------|
| $all | Ano| 
| $elemMatch | Ano| 
| $size | Ano | 

### <a name="comment-operator"></a>Operátor komentáře

|Příkaz  |Podporuje se | 
|---------|---------|
$comment |Ano| 

### <a name="projection-operators"></a>Operátory projekce

|Příkaz  |Podporuje se |
|---------|---------|
|$elemMatch |Ano|
|$meta| Ne|
|$slice | Ano|

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí

|Příkaz  |Podporuje se |
|---------|---------|
|$inc   |   Ano|
|$mul   |   Ano|
|$rename    |   Ano|
|$setOnInsert|  Ano|
|$set   |Ano|
|$unset| Ano|
|$min   |Ano|
|$max   |Ano|
|$currentDate   | Ano|

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic

|Příkaz  |Podporuje se |
|---------|---------|
|$  |Ano|
|$[]|   Ano|
|$ [<identifier>]|   Ano|
|$addToSet  |Ano|
|$pop   |Ano|
|$pullAll|  Ano|
|$pull  |Ano|
|$push  |Ano|
|$pushAll| Ano|


#### <a name="update-modifiers"></a>Aktualizovat modifikátory

|Příkaz  |Podporuje se |
|---------|---------|
|$each  |   Ano|
|$slice |Ano|
|$sort  |Ano|
|$position  |Ano|

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci

|Příkaz  |Podporuje se |
|---------|---------|
| $bit  |   Ano|    
|$bitsAllSet    |   Ne|
|$bitsAnySet    |   Ne|
|$bitsAllClear  |Ne|
|$bitsAnyClear  |Ne|

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Podporuje se| 
--- | --- |
$geoWithin | Ano |
$geoIntersects | Ano | 
$near |  Ano |
$nearSphere |  Ano |
$geometry |  Ano |
$minDistance | Ano |
$maxDistance | Ano |
$center | Ano |
$centerSphere | Ano |
$box | Ano |
$polygon |  Ano |

## <a name="cursor-methods"></a>Metody kurzoru

|Příkaz  |Podporuje se |
|---------|---------|
|Cursor. batchSize () |   Ano|
|Cursor. Close () |Ano|
|Cursor. uzavřeno ()|     Ano|
|Cursor. COLLATE ()|    Ne|
|Cursor. Comment ()   |Ano|
|Cursor. Count () |Ano|
|Cursor. vysvětlit ()|  Ne|
|Cursor. forEach ()   |Ano|
|Cursor. hasNext ()   |Ano|
|Cursor. Hint ()  |Ano|
|Cursor.-vyčerpáno ()|  Ano|
|Cursor. itcount ()   |Ano|
|Cursor. limit () |Ano|
|Cursor. map ()   |Ano|
|Cursor. maxScan ()   |Ano|
|Cursor. maxTimeMS ()|    Ano|
|Cursor. Max ()   |Ano|
|Cursor. min ()   |Ano|
|Cursor. Next ()| Ano|
|Cursor. noCursorTimeout ()   |Ne|
|Cursor. objsLeftInBatch ()   |Ano|
|Cursor. poměrně ()|   Ano|
|Cursor. readConcern ()|  Ano|
|Cursor. readPref ()      |Ano|
|Cursor. returnKey () |Ne|
|Cursor. showRecordId ()| Ne|
|Cursor. Size ()  |Nes|
|Cursor. Skip ()  |Ano|
|cursor.sort()  |   Ano|
|Cursor. Tail ()| Ne|
|Cursor. ToArray – ()   |Ano|

## <a name="sort-operations"></a>Operace řazení

Při použití operace `findOneAndUpdate` jsou podporovány operace řazení v jednom poli, ale operace řazení pro více polí nejsou podporovány.

## <a name="unique-indexes"></a>Jedinečné indexy

Jedinečné indexy zajišťují, aby určité pole nemělo duplicitní hodnoty v rámci všech dokumentů v kolekci. Je to podobné, jako když má být zachovaná jedinečnost výchozího klíče „_id“. Vlastní indexy můžete v Cosmos DB vytvořit pomocí příkazu createIndex, včetně omezení UNIQUE.

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Cosmos DB podporuje hodnotu TTL (Time-to-Live) na základě časového razítka dokumentu. Hodnotu TTL lze povolit pro kolekce, a to tak, že se vrátíte do [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Cosmos DB ještě nepodporuje uživatele a role. Cosmos DB však podporuje řízení přístupu na základě role (RBAC) a hesla a klíče pro čtení i zápis a jen pro čtení, které lze získat pomocí [Azure Portal](https://portal.azure.com) (stránka připojovací řetězec).

## <a name="replication"></a>Replikace

Cosmos DB podporuje automatickou nativní replikaci na nejnižší vrstvě. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Cosmos DB nepodporuje příkazy ruční replikace.

## <a name="write-concern"></a>Write Concern

Některé aplikace spoléhají na [oprávnění k zápisu](https://docs.mongodb.com/manual/reference/write-concern/) , které určuje počet odpovědí vyžadovaných během operace zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Všechny důležité problémy s zápisy, které jsou určeny klientským kódem, se ignorují. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatický sharding na straně serveru. Spravuje horizontálních oddílů vytváření, umísťování a vyrovnávání automaticky. Azure Cosmos DB nepodporuje ruční příkazy horizontálního dělení, což znamená, že nemusíte volat příkazy, jako je addShard, balancerStart, moveChunk atd. Při vytváření kontejnerů nebo dotazování na data stačí zadat horizontálních oddílů klíč.

## <a name="sessions"></a>Relace

Azure Cosmos DB ještě nepodporuje příkazy pro relace na straně serveru.

## <a name="next-steps"></a>Další kroky

- Další informace najdete v [Mongo 3,6 – funkce verze](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.

<sup>Poznámka: Tento článek popisuje funkci Azure Cosmos DB, která poskytuje kompatibilitu s protokolem sítě s databázemi MongoDB. Společnost Microsoft nespouští MongoDB databáze pro poskytování této služby. K Azure Cosmos DB není přidružena společnost MongoDB, Inc.</sup>
