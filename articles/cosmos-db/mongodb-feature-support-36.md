---
title: Podporované funkce a syntaxe rozhraní API pro MongoDB (verze 3,6) Azure Cosmos DB
description: Přečtěte si o podporovaných funkcích a syntaxi rozhraní API pro Azure Cosmos DB pro MongoDB (verze 3,6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/07/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 399c078481fb205221a683e44f74b4d70c383cbe
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096559"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Rozhraní API služby Azure Cosmos DB pro MongoDB (verze 3.6): Podporované funkce a syntax
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z open-source klientských [ovladačů](https://docs.mongodb.org/ecosystem/drivers)MongoDB. Rozhraní API služby Azure Cosmos DB pro MongoDB umožňuje použití existujících klientských ovladačů dodržováním [přenosového protokolu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Díky rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody MongoDB, ke kterému jste se přihlásili, se všemi podnikovými funkcemi, které Cosmos DB poskytuje: [globální distribuce](distribute-data-globally.md), [Automatické horizontálního dělení](partitioning-overview.md), dostupnost a záruky latence, šifrování v klidovém formátu, zálohování a spoustu dalšího.

## <a name="protocol-support"></a>Podpora protokolů

Rozhraní API služby Azure Cosmos DB pro MongoDB je ve výchozím nastavení pro nové účty kompatibilní se serverem MongoDB verze **3.6** . Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API služby Azure Cosmos DB pro MongoDB. Upozorňujeme, že při použití účtů rozhraní API služby Azure Cosmos DB pro MongoDB mají účty verze 3.6 koncový bod ve formátu `*.mongo.cosmos.azure.com`, zatímco účty verze 3.2 mají koncový bod ve formátu `*.documents.azure.com`.

## <a name="query-language-support"></a>Podpora jazyka dotazů

Rozhraní API pro Azure Cosmos DB MongoDB poskytuje komplexní podporu konstrukcí jazyka MongoDB pro dotazy. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API pro Azure Cosmos DB pro MongoDB podporuje následující databázové příkazy:

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace

|Příkaz  |Podporováno |
|---------|---------|
|delete | Yes |
|find | Yes     |
|findAndModify | Yes  |
|getLastError|   Yes |
|getMore  |  Yes  |
|getPrevError | No  |
|insert  |   Yes  |
|parallelCollectionScan  | Yes   |
|resetError |    No  |
|update  |   Yes  |
|[Změna datových proudů](mongodb-change-streams.md)  |  Yes  |
|GridFS |   Yes  |

### <a name="authentication-commands"></a>Příkazy pro ověření

|Příkaz  |Podporováno |
|---------|---------|
|authenticate    |   Yes      |
|logout    |      Yes   |
|getnonce   |    Yes     |


### <a name="administration-commands"></a>Příkazy pro správu

|Příkaz  |Podporováno |
|---------|---------|
|Kolekce omezené   |   No      |
|cloneCollectionAsCapped     |   No      |
|collMod     |   No      |
|collMod: expireAfterSeconds   |   No      |
|convertToCapped   |  No       |
|copydb     |  No       |
|vytvoření   |    Yes     |
|createIndexes     |  Yes       |
|currentOp     |  Yes       |
|drop     |   Yes      |
|dropDatabase     |  Yes       |
|dropIndexes     |   Yes      |
|filemd5    |   Yes      |
|killCursors    |  Yes       |
|killOp     |   No      |
|listCollections     |  Yes       |
|listDatabases     |  Yes       |
|listIndexes     |  Yes       |
|reIndex     |    Yes     |
|Přejmenujte     |    No     |
|connectionStatus    |     No    |

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku

|Příkaz  |Podporováno |
|---------|---------|
|buildInfo         |   Yes      |
|collStats    |  Yes       |
|connPoolStats     |  No       |
|connectionStatus     |  No       |
|dataSize     |   No      |
|dbHash    |    No     |
|dbStats     |   Yes      |
|částech     |   Yes      |
|Vysvětlení: executionStats     |   Yes      |
|funkce     |    No     |
|hostInfo     |   No      |
|listDatabases         |   Yes      |
|listCommands     |  No       |
|modulu     |  No       |
|serverStatus     |  No       |
|top     |    No     |
|whatsmyuri     |   Yes      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Agregační zřetězení</a>

### <a name="aggregation-commands"></a>Příkazy pro agregaci

|Příkaz  |Podporováno |
|---------|---------|
|aggregate |   Yes  |
|count     |   Yes  |
|distinct  | Yes |
|mapReduce | No |

### <a name="aggregation-stages"></a>Fáze agregace

|Příkaz  |Podporováno |
|---------|---------|
|$collStats    |No|
|$project    |Yes|
|$match    |Yes|
|$redact|    Yes|
|$limit    |Yes|
|$skip    |Yes|
|$unwind|    Yes|
|$group    |    Yes|
|$sample|        Yes|
|$sort    |Yes|
|$geoNear|    No|
|$lookup    |    Yes|
|$out        |Yes|
|$indexStats|        No|
|$facet    |Yes|
|$bucket|    No|
|$bucketAuto|    No|
|$sortByCount|    Yes|
|$addFields    |Yes|
|$replaceRoot|    Yes|
|$count    |Yes|
|$currentOp|    No|
|$listLocalSessions    |No|
|$listSessions    |No|
|$graphLookup    |No|

### <a name="boolean-expressions"></a>Logické výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$and| Yes|
|$or|Yes|
|$not|Yes|

### <a name="set-expressions"></a>Nastavovací výrazy

|Příkaz  |Podporováno |
|---------|---------|
| $setEquals | Yes|
|$setIntersection|Yes|
| $setUnion|Yes|
| $setDifference|Yes|
| $setIsSubset|Yes|
| $anyElementTrue|Yes|
| $allElementsTrue|Yes|

### <a name="comparison-expressions"></a>Porovnávací výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$cmp     |  Yes       |
|$eq|    Yes| 
|$gt |    Yes| 
|$gte|    Yes| 
|$lt    |Yes|
|$lte|    Yes| 
|$ne    |    Yes| 
|$in    |    Yes| 
|$nin    |    Yes| 

### <a name="arithmetic-expressions"></a>Aritmetické výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$abs |  Yes       |
| $add |  Yes       |
| $ceil |  Yes       |
| $divide |  Yes       |
| $exp |  Yes       |
| $floor |  Yes       |
| $ln |  Yes       |
| $log |  Yes       |
| $log10 |  Yes       |
| $mod |  Yes       |
| $multiply |  Yes       |
| $pow |  Yes       |
| $sqrt |  Yes       |
| $subtract |  Yes       |
| $trunc |  Yes       |

### <a name="string-expressions"></a>Řetězcové výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$concat |  Yes       |
| $indexOfBytes|  Yes       |
| $indexOfCP|  Yes       |
| $split|  Yes       |
| $strLenBytes|  Yes       |
| $strLenCP|  Yes       |
| $strcasecmp|  Yes       |
| $substr|  Yes       |
| $substrBytes|  Yes       |
| $substrCP|  Yes       |
| $toLower|  Yes       |
| $toUpper|  Yes       |

### <a name="text-search-operator"></a>Operátor hledání textu

|Příkaz  |Podporováno |
|---------|---------|
| $meta | No|

### <a name="array-expressions"></a>Maticové výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$arrayElemAt    |    Yes|
|$arrayToObject|    Yes|
|$concatArrays    |    Yes|
|$filter    |    Yes|
|$indexOfArray    |Yes|
|$isArray    |    Yes|
|$objectToArray    |Yes|
|$range    |Yes|
|$reverseArray    |    Yes|
|$reduce|    Yes|
|$size    |    Yes|
|$slice    |    Yes|
|$zip    |    Yes|
|$in    |    Yes|

### <a name="variable-operators"></a>Operátory proměnných

|Příkaz  |Podporováno |
|---------|---------|
|$map    |No|
|$let    |Yes|

### <a name="system-variables"></a>Systémové proměnné

|Příkaz  |Podporováno |
|---------|---------|
|$ $CURRENT|    Yes|
|$ $DESCEND|        Yes|
|$ $KEEP        |Yes|
|$ $PRUNE    |    Yes|
|$ $REMOVE    |Yes|
|$ $ROOT        |Yes|

### <a name="literal-operator"></a>Operátor literálu

|Příkaz  |Podporováno |
|---------|---------|
|$literal    |Yes|

### <a name="date-expressions"></a>Datumové výrazy

|Příkaz  |Podporováno |
|---------|---------|
|$dayOfYear    |Yes    |
|$dayOfMonth|    Yes    |
|$dayOfWeek    |Yes    |
|$year    |Yes    |
|$month    |Yes|    
|$week    |Yes    |
|$hour    |Yes    |
|$minute|    Yes|    
|$second    |Yes    |
|$millisecond|    Yes|    
|$dateToString    |Yes    |
|$isoDayOfWeek    |Yes    |
|$isoWeek    |Yes    |
|$dateFromParts|    No|    
|$dateToParts    |No    |
|$dateFromString|    No|
|$isoWeekYear    |Yes    |

### <a name="conditional-expressions"></a>Podmíněné výrazy

|Příkaz  |Podporováno |
|---------|---------|
| $cond| Yes|
| $ifNull| Yes|
| $switch |Yes|

### <a name="data-type-operator"></a>Operátor datového typu

|Příkaz  |Podporováno |
|---------|---------|
| $type| Yes|

### <a name="accumulator-expressions"></a>Výrazy akumulovaných

|Příkaz  |Podporováno |
|---------|---------|
|$sum    |Yes    |
|$avg    |Yes    |
|$first|    Yes|
|$last    |Yes    |
|$max    |Yes    |
|$min    |Yes    |
|$push|    Yes|
|$addToSet|    Yes|
|$stdDevPop|    No    |
|$stdDevSamp|    No|

### <a name="merge-operator"></a>Operátor sloučení

|Příkaz  |Podporováno |
|---------|---------|
| $mergeObjects | Yes|

## <a name="data-types"></a>Typy dat

|Příkaz  |Podporováno |
|---------|---------|
|dvojité    |Ano    |
|Řetězec    |Yes    |
|Objekt    |Yes    |
|Pole    |Yes    |
|Binární data    |Yes|    
|ObjectId    |Yes    |
|Logická hodnota    |Yes    |
|Datum    |Yes    |
|Null    |Yes    |
|32 bitové celé číslo (int)    |Yes    |
|Timestamp    |Yes    |
|64-bitové celé číslo (Long)    |Yes    |
|MinKey    |Yes    |
|MaxKey    |Yes    |
|Decimal128    |Yes|    
|Regulární výraz    |Yes|
|JavaScript    |Yes|
|JavaScript (s rozsahem)|    Yes    |
|Nedefinované    |Yes    |

## <a name="indexes-and-index-properties"></a>Index a vlastnosti indexu

### <a name="indexes"></a>Indexy

|Příkaz  |Podporováno |
|---------|---------|
|Index jednoho pole    |Yes    |
|Složený index    |Yes    |
|Multikey index    |Yes    |
|Textový index    |No|
|2dsphere    |Yes    |
|2D index    |No    |
|Index s hodnotou hash    | Yes|

### <a name="index-properties"></a>Vlastnosti indexu

|Příkaz  |Podporováno |
|---------|---------|
|TTL|    Yes    |
|Jedinečná    |Yes|
|Částečné|    No|
|Nerozlišuje malá a velká písmena.    |No|
|Řídké    |No |
|Pozadí|    Yes |

## <a name="operators"></a>Operátory

### <a name="logical-operators"></a>Logické operátory

|Příkaz  |Podporováno |
|---------|---------|
|$or    |    Yes|
|$and    |    Yes|
|$not    |    Yes|
|$nor    |    Yes| 

### <a name="element-operators"></a>Operátory elementu

|Příkaz  |Podporováno |
|---------|---------|
|$exists|    Yes|
|$type    |    Yes|

### <a name="evaluation-query-operators"></a>Operátory dotazování pro vyhodnocení

|Příkaz  |Podporováno |
|---------|---------|
|$expr    |    No|
|$jsonSchema    |    No|
|$mod    |    Yes|
|$regex |    Yes|
|$text    | Ne (není podporováno. Místo toho použijte $regex.)| 
|$where    |No| 

V $regexch dotazech jsou výrazy ukotvené zleva povoleny hledání v indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.

Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například s ohledem na následující původní dotaz: ```find({x:{$regex: /^abc$/})``` , je nutné upravit následujícím způsobem:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Operátory pole

|Příkaz  |Podporováno | 
|---------|---------|
| $all | Yes| 
| $elemMatch | Yes| 
| $size | Yes | 

### <a name="comment-operator"></a>Operátor komentáře

|Příkaz  |Podporováno | 
|---------|---------|
$comment |Yes| 

### <a name="projection-operators"></a>Operátory projekce

|Příkaz  |Podporováno |
|---------|---------|
|$elemMatch    |Yes|
|$meta|    No|
|$slice    | Yes|

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí

|Příkaz  |Podporováno |
|---------|---------|
|$inc    |    Yes|
|$mul    |    Yes|
|$rename    |    Yes|
|$setOnInsert|    Yes|
|$set    |Yes|
|$unset| Yes|
|$min    |Yes|
|$max    |Yes|
|$currentDate    | Yes|

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic

|Příkaz  |Podporováno |
|---------|---------|
|$    |Yes|
|$[]|    Yes|
|$[<identifier>]|    Yes|
|$addToSet    |Yes|
|$pop    |Yes|
|$pullAll|    Yes|
|$pull    |Yes|
|$push    |Yes|
|$pushAll| Yes|


#### <a name="update-modifiers"></a>Aktualizovat modifikátory

|Příkaz  |Podporováno |
|---------|---------|
|$each    |    Yes|
|$slice    |Yes|
|$sort    |Yes|
|$position    |Yes|

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci

|Příkaz  |Podporováno |
|---------|---------|
| $bit    |    Yes|    
|$bitsAllSet    |    No|
|$bitsAnySet    |    No|
|$bitsAllClear    |No|
|$bitsAnyClear    |No|

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Podporováno| 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near |  Yes |
$nearSphere |  Yes |
$geometry |  Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | No |
$centerSphere | No |
$box | No |
$polygon |  No |

## <a name="cursor-methods"></a>Metody kurzoru

|Příkaz  |Podporováno |
|---------|---------|
|cursor.batchSize ()    |    Yes|
|Cursor. Close ()    |Yes|
|Cursor. uzavřeno ()|        Yes|
|Cursor. COLLATE ()|    No|
|Cursor. Comment ()    |Yes|
|Cursor. Count ()    |Yes|
|Cursor. vysvětlit ()|    No|
|Cursor. forEach ()    |Yes|
|Cursor. hasNext ()    |Yes|
|Cursor. Hint ()    |Yes|
|Cursor.-vyčerpáno ()|    Yes|
|Cursor. itcount ()    |Yes|
|Cursor. limit ()    |Yes|
|Cursor. map ()    |Yes|
|Cursor. maxScan ()    |Yes|
|Cursor. maxTimeMS ()|    Yes|
|Cursor. Max ()    |Yes|
|Cursor. min ()    |Yes|
|Cursor. Next ()|    Yes|
|Cursor. noCursorTimeout ()    |No|
|Cursor. objsLeftInBatch ()    |Yes|
|Cursor. poměrně ()|    Yes|
|Cursor. readConcern ()|    Yes|
|Cursor. readPref ()        |Yes|
|Cursor. returnKey ()    |No|
|Cursor. showRecordId ()|    No|
|Cursor. Size ()    |Yes|
|Cursor. Skip ()    |Yes|
|cursor.sort()    |    Yes|
|Cursor. Tail ()|    No|
|Cursor. ToArray – ()    |Yes|

## <a name="sort-operations"></a>Operace řazení

Při použití `findOneAndUpdate` operace jsou podporovány operace řazení v jednom poli, ale operace řazení u více polí nejsou podporovány.

## <a name="unique-indexes"></a>Jedinečné indexy

[Jedinečné indexy](mongodb-indexing.md#unique-indexes) zajišťují, že konkrétní pole neobsahuje duplicitní hodnoty ve všech dokumentech v kolekci, podobně jako jedinečnost je zachována ve výchozím klíči "_ID". V Cosmos DB můžete vytvořit jedinečné indexy pomocí `createIndex` příkazu s `unique` parametrem omezení:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Složené indexy

[Složené indexy](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) poskytují způsob, jak vytvořit index pro skupiny polí po dobu až 8 polí. Tento typ indexu se od nativních složených indexů MongoDB liší. V Azure Cosmos DB se pro řazení operací, které jsou použity na více polí, používají složené indexy. Chcete-li vytvořit složený index, je nutné zadat více než jednu vlastnost jako parametr:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

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
