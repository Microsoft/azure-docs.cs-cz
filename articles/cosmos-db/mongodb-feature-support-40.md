---
title: 4,0 funkce a syntaxe v rozhraní API Azure Cosmos DB v rozhraní API pro MongoDB
description: Přečtěte si o rozhraních API Azure Cosmos DB pro podporované funkce a syntaxi pro verzi serveru MongoDB 4,0. Přečtěte si o příkazech databáze, podpoře jazyka dotazů, typech DataTypes, příkazech kanálu agregace a podporovaných operátorech.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 9eebc77c5b3d9402c766320fddfdaf05d50b574f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485397"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-40-server-version-supported-features-and-syntax"></a>Rozhraní API Azure Cosmos DB pro MongoDB (verze serveru 4,0): podporované funkce a syntaxe
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z open-source klientských [ovladačů](https://docs.mongodb.org/ecosystem/drivers)MongoDB. Rozhraní API služby Azure Cosmos DB pro MongoDB umožňuje použití existujících klientských ovladačů dodržováním [přenosového protokolu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Díky rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody MongoDB, ke kterému jste se přihlásili, se všemi podnikovými funkcemi, které Cosmos DB poskytuje: [globální distribuce](distribute-data-globally.md), [Automatické horizontálního dělení](partitioning-overview.md), dostupnost a záruky latence, šifrování v klidovém formátu, zálohování a spoustu dalšího.

## <a name="protocol-support"></a>Podpora protokolů

Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Jakýkoli ovladač klienta, který podporuje tyto protokoly, by se měl dokázat připojit k rozhraní API služby Azure Cosmos DB pro MongoDB. Při použití rozhraní API Azure Cosmos DB pro účty MongoDB má 3.6 + verze účtů ve formátu, `*.mongo.cosmos.azure.com` že verze 3,2 účtů má koncový bod ve formátu `*.documents.azure.com` .

> [!NOTE]
> Tento článek obsahuje jenom podporované příkazy serveru a nezahrnuje funkce obálky na straně klienta. Funkce obálky na straně klienta, například `deleteMany()` a `updateMany()` interně využívají `delete()` `update()` příkazy a serveru. Funkce, které využívají podporované příkazy serveru, jsou kompatibilní s rozhraním API Azure Cosmos DB pro MongoDB.

## <a name="query-language-support"></a>Podpora jazyka dotazů

Rozhraní API pro Azure Cosmos DB MongoDB poskytuje komplexní podporu konstrukcí jazyka MongoDB pro dotazy. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Rozhraní API pro Azure Cosmos DB pro MongoDB podporuje následující databázové příkazy:

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace

| Příkaz | Podporováno |
|---------|---------|
| [Změna datových proudů](mongodb-change-streams.md) | Ano |
| delete | Ano |
| platnost | Ne |
| find | Ano |
| findAndModify | Ano |
| getLastError | Ano |
| getMore | Ano |
| getPrevError | Ne |
| insert | Ano |
| parallelCollectionScan | Ne |
| resetError | Ne |
| update | Ano |

### <a name="transaction-commands"></a>Příkazy transakcí

| Příkaz | Podporováno |
|---------|---------|
| abortTransaction | Ano |
| commitTransaction | Ano |

### <a name="authentication-commands"></a>Příkazy pro ověření

| Příkaz | Podporováno |
|---------|---------|
| authenticate | Ano |
| getnonce | Ano |
| logout | Ano |

### <a name="administration-commands"></a>Příkazy pro správu

| Příkaz | Podporováno |
|---------|---------|
| cloneCollectionAsCapped | Ne |
| collMod | Ne |
| connectionStatus | Ne |
| convertToCapped | Ne |
| copydb | Ne |
| vytvoření | Ano |
| createIndexes | Ano |
| currentOp | Ano |
| drop | Ano |
| dropDatabase | Ano |
| dropIndexes | Ano |
| filemd5 | Ano |
| killCursors | Ano |
| killOp | Ne |
| listCollections | Ano |
| listDatabases | Ano |
| listIndexes | Ano |
| reIndex | Ano |
| Přejmenujte | Ne |

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku

| Příkaz | Podporováno |
|---------|---------|
| buildInfo | Ano |
| collStats | Ano |
| connPoolStats | Ne |
| connectionStatus | Ne |
| dataSize | Ne |
| dbHash | Ne |
| dbStats | Ano |
| částech | Ano |
| funkce | Ne |
| hostInfo | Ano |
| listDatabases | Ano |
| listCommands | Ne |
| modulu | Ne |
| serverStatus | Ne |
| top | Ne |
| whatsmyuri | Ano |

## <a name="aggregation-pipeline"></a><a name="aggregation-pipeline"></a>Agregovaný kanál

### <a name="aggregation-commands"></a>Příkazy pro agregaci

| Příkaz | Podporováno |
|---------|---------|
| aggregate | Ano |
| count | Ano |
| distinct | Ano |
| mapReduce | Ne |

### <a name="aggregation-stages"></a>Fáze agregace

| Příkaz | Podporováno |
|---------|---------|
| $addFields | Ano |
| $bucket | Ne |
| $bucketAuto | Ne |
| $changeStream | Ano |
| $collStats | Ne |
| $count | Ano |
| $currentOp | Ne |
| $facet | Ano |
| $geoNear | Ano |
| $graphLookup | Ano |
| $group | Ano |
| $indexStats | Ne |
| $limit | Ano |
| $listLocalSessions | Ne |
| $listSessions | Ne |
| $lookup | Ano |
| $match | Ano |
| $out | Ano |
| $project | Ano |
| $redact | Ano |
| $replaceRoot | Ano |
| $replaceWith | Ne |
| $sample | Ano |
| $skip | Ano |
| $sort | Ano |
| $sortByCount | Ano |
| $unwind | Ano |

### <a name="boolean-expressions"></a>Logické výrazy

| Příkaz | Podporováno |
|---------|---------|
| $and | Ano |
| $not | Ano |
| $or | Ano |

### <a name="conversion-expressions"></a>Výrazy převodu

| Příkaz | Podporováno |
|---------|---------|
| $convert | Ano |
| $toBool | Ano |
| $toDate | Ano |
| $toDecimal | Ano |
| $toDouble | Ano |
| $toInt | Ano |
| $toLong | Ano |
| $toObjectId | Ano |
| $toString | Ano |

### <a name="set-expressions"></a>Nastavovací výrazy

| Příkaz | Podporováno |
|---------|---------|
| $setEquals | Ano |
| $setIntersection | Ano |
| $setUnion | Ano |
| $setDifference | Ano |
| $setIsSubset | Ano |
| $anyElementTrue | Ano |
| $allElementsTrue | Ano |

### <a name="comparison-expressions"></a>Porovnávací výrazy

| Příkaz | Podporováno |
|---------|---------|
| $cmp | Ano |
| $eq | Ano | 
| $gt | Ano | 
| $gte | Ano | 
| $lt | Ano |
| $lte | Ano | 
| $ne | Ano | 
| $in | Ano | 
| $nin | Ano | 

### <a name="arithmetic-expressions"></a>Aritmetické výrazy

| Příkaz | Podporováno |
|---------|---------|
| $abs | Ano |
| $add | Ano |
| $ceil | Ano |
| $divide | Ano |
| $exp | Ano |
| $floor | Ano |
| $ln | Ano |
| $log | Ano |
| $log10 | Ano |
| $mod | Ano |
| $multiply | Ano |
| $pow | Ano |
| $sqrt | Ano |
| $subtract | Ano |
| $trunc | Ano |

### <a name="string-expressions"></a>Řetězcové výrazy

| Příkaz | Podporováno |
|---------|---------|
| $concat | Ano |
| $indexOfBytes | Ano |
| $indexOfCP | Ano |
| $ltrim | Ano |
| $rtrim | Ano |
| $trim | Ano |
| $split | Ano |
| $strLenBytes | Ano |
| $strLenCP | Ano |
| $strcasecmp | Ano |
| $substr | Ano |
| $substrBytes | Ano |
| $substrCP | Ano |
| $toLower | Ano |
| $toUpper | Ano |

### <a name="text-search-operator"></a>Operátor hledání textu

| Příkaz | Podporováno |
|---------|---------|
| $meta | Ne |

### <a name="array-expressions"></a>Maticové výrazy

| Příkaz | Podporováno |
|---------|---------|
| $arrayElemAt | Ano |
| $arrayToObject | Ano |
| $concatArrays | Ano |
| $filter | Ano |
| $indexOfArray | Ano |
| $isArray | Ano |
| $objectToArray | Ano |
| $range | Ano |
| $reverseArray | Ano |
| $reduce | Ano |
| $size | Ano |
| $slice | Ano |
| $zip | Ano |
| $in | Ano |

### <a name="variable-operators"></a>Operátory proměnných

| Příkaz | Podporováno |
|---------|---------|
| $map | Ano |
| $let | Ano |

### <a name="system-variables"></a>Systémové proměnné

| Příkaz | Podporováno |
|---------|---------|
| $ $CURRENT | Ano |
| $ $DESCEND | Ano |
| $ $KEEP | Ano |
| $ $PRUNE | Ano |
| $ $REMOVE | Ano |
| $ $ROOT | Ano |

### <a name="literal-operator"></a>Operátor literálu

| Příkaz | Podporováno |
|---------|---------|
| $literal | Ano |

### <a name="date-expressions"></a>Datumové výrazy

| Příkaz | Podporováno |
|---------|---------|
| $dayOfYear | Ano |
| $dayOfMonth | Ano |
| $dayOfWeek | Ano |
| $year | Ano |
| $month | Ano | 
| $week | Ano |
| $hour | Ano |
| $minute | Ano | 
| $second | Ano |
| $millisecond | Ano | 
| $dateToString | Ano |
| $isoDayOfWeek | Ano |
| $isoWeek | Ano |
| $dateFromParts | Ne | 
| $dateToParts | Ne |
| $dateFromString | Ne |
| $isoWeekYear | Ano |

### <a name="conditional-expressions"></a>Podmíněné výrazy

| Příkaz | Podporováno |
|---------|---------|
| $cond | Ano |
| $ifNull | Ano |
| $switch | Ano |

### <a name="data-type-operator"></a>Operátor datového typu

| Příkaz | Podporováno |
|---------|---------|
| $type | Ano |

### <a name="accumulator-expressions"></a>Výrazy akumulovaných

| Příkaz | Podporováno |
|---------|---------|
| $sum | Ano |
| $avg | Ano |
| $first | Ano |
| $last | Ano |
| $max | Ano |
| $min | Ano |
| $push | Ano |
| $addToSet | Ano |
| $stdDevPop | Ano |
| $stdDevSamp | Ano |

### <a name="merge-operator"></a>Operátor sloučení

| Příkaz | Podporováno |
|---------|---------|
| $mergeObjects | Ano |

## <a name="data-types"></a>Typy dat

Rozhraní API pro Azure Cosmos DB pro MongoDB podporuje dokumenty kódované ve formátu MongoDB BSON. Verze rozhraní API 4,0 vylepšuje interní použití tohoto formátu za účelem zvýšení výkonu a snížení nákladů. Z tohoto koncového bodu s 4,0 výhodami se zapisují nebo aktualizují dokumenty.
 
V [případě upgradu](mongodb-version-upgrade.md)nebudou dokumenty zapsané před upgradem na verzi 4,0 využívat Vylepšený výkon, dokud nebudou aktualizovány prostřednictvím operace zápisu prostřednictvím koncového bodu 4,0.

| Příkaz | Podporováno |
|---------|---------|
| dvojité | Ano |
| Řetězec | Ano |
| Objekt | Ano |
| Pole | Ano |
| Binární data | Ano | 
| ObjectId | Ano |
| Logická hodnota | Ano |
| Date (Datum) | Ano |
| Null | Ano |
| 32 bitové celé číslo (int) | Ano |
| Timestamp | Ano |
| 64-bitové celé číslo (Long) | Ano |
| MinKey | Ano |
| MaxKey | Ano |
| Decimal128 | Ano | 
| Regulární výraz | Ano |
| JavaScript | Ano |
| JavaScript (s rozsahem)| Ano |
| Nedefinované | Ano |

## <a name="indexes-and-index-properties"></a>Index a vlastnosti indexu

### <a name="indexes"></a>Indexy

| Příkaz | Podporováno |
|---------|---------|
| Index jednoho pole | Ano |
| Složený index | Ano |
| Multikey index | Ano |
| Textový index | Ne |
| 2dsphere | Ano |
| 2D index | Ne |
| Index s hodnotou hash | Ano |

### <a name="index-properties"></a>Vlastnosti indexu

| Příkaz | Podporováno |
|---------|---------|
| TTL | Ano |
| Jedinečná | Ano |
| Částečné | Ne |
| Nerozlišuje malá a velká písmena. | Ne |
| Řídké | Ne |
| Pozadí | Ano |

## <a name="operators"></a>Operátory

### <a name="logical-operators"></a>Logické operátory

| Příkaz | Podporováno |
|---------|---------|
| $or | Ano |
| $and | Ano |
| $not | Ano |
| $nor | Ano | 

### <a name="element-operators"></a>Operátory elementu

| Příkaz | Podporováno |
|---------|---------|
| $exists | Ano |
| $type | Ano |

### <a name="evaluation-query-operators"></a>Operátory dotazování pro vyhodnocení

| Příkaz | Podporováno |
|---------|---------|
| $expr | Ne |
| $jsonSchema | Ne |
| $mod | Ano |
| $regex | Ano |
| $text | Ne (není podporováno. Místo toho použijte $regex.)| 
| $where | Ne | 

V $regexch dotazech jsou výrazy ukotvené zleva povoleny hledání v indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.

Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například s ohledem na následující původní dotaz: `find({x:{$regex: /^abc$/})` , je nutné upravit následujícím způsobem:

`find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})`

V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz `find({x:{$regex: /^abc |^def/})` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: `find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })`.

### <a name="array-operators"></a>Operátory pole

| Příkaz | Podporováno | 
|---------|---------|
| $all | Ano | 
| $elemMatch | Ano | 
| $size | Ano | 

### <a name="comment-operator"></a>Operátor komentáře

| Příkaz | Podporováno | 
|---------|---------|
| $comment | Ano | 

### <a name="projection-operators"></a>Operátory projekce

| Příkaz | Podporováno |
|---------|---------|
| $elemMatch | Ano |
| $meta | Ne |
| $slice | Ano |

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí

| Příkaz | Podporováno |
|---------|---------|
| $inc | Ano |
| $mul | Ano |
| $rename | Ano |
| $setOnInsert | Ano |
| $set | Ano |
| $unset | Ano |
| $min | Ano |
| $max | Ano |
| $currentDate | Ano |

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic

| Příkaz | Podporováno |
|---------|---------|
| $ | Ano |
| $[]| Ano |
| $[<identifier>]| Ano |
| $addToSet | Ano |
| $pop | Ano |
| $pullAll | Ano |
| $pull | Ano |
| $push | Ano |
| $pushAll | Ano |

#### <a name="update-modifiers"></a>Aktualizovat modifikátory

| Příkaz | Podporováno |
|---------|---------|
| $each | Ano |
| $slice | Ano |
| $sort | Ano |
| $position | Ano |

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci

| Příkaz | Podporováno |
|---------|---------|
| $bit | Ano | 
| $bitsAllSet | Ne |
| $bitsAnySet | Ne |
| $bitsAllClear | Ne |
| $bitsAnyClear | Ne |

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Podporováno | 
--- | --- |
$geoWithin | Ano |
$geoIntersects | Ano | 
$near | Ano |
$nearSphere | Ano |
$geometry | Ano |
$minDistance | Ano |
$maxDistance | Ano |
$center | Ne |
$centerSphere | Ne |
$box | Ne |
$polygon | Ne |

## <a name="sort-operations"></a>Operace řazení

Při použití `findOneAndUpdate` operace jsou podporovány operace řazení v jednom poli, ale operace řazení u více polí nejsou podporovány.

## <a name="unique-indexes"></a>Jedinečné indexy

[Jedinečné indexy](mongodb-indexing.md#unique-indexes) zajišťují, že konkrétní pole neobsahuje duplicitní hodnoty ve všech dokumentech v kolekci, podobně jako jedinečnost je zachována ve výchozím klíči "_ID". V Azure Cosmos DB můžete vytvořit jedinečné indexy pomocí `createIndex` příkazu s `unique` parametrem omezení:

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

[Složené indexy](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) poskytují způsob, jak vytvořit index pro skupiny polí po dobu až osmi polí. Tento typ indexu se od nativních složených indexů MongoDB liší. V Azure Cosmos DB se pro řazení operací, které jsou použity na více polí, používají složené indexy. Chcete-li vytvořit složený index, je nutné zadat více než jednu vlastnost jako parametr:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
    "createdCollectionAutomatically" : false, 
    "numIndexesBefore" : 1,
    "numIndexesAfter" : 2,
    "ok" : 1
}
```

## <a name="gridfs"></a>GridFS

Azure Cosmos DB podporuje GridFS prostřednictvím jakéhokoli ovladače Mongo kompatibilního s GridFS.

## <a name="replication"></a>Replikace

Azure Cosmos DB podporuje automatickou nativní replikaci v nejnižších vrstvách. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Cosmos DB nepodporuje příkazy ruční replikace.

## <a name="retryable-writes"></a>Opakované zápisy

Cosmos DB zatím nepodporuje opakované zápisy. Ovladače klienta musí do svého připojovacího řetězce přidat parametr URL ' retryWrites = false '. Další parametry adresy URL lze přidat pomocí prefixování s &. 

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatický sharding na straně serveru. Spravuje horizontálních oddílů vytváření, umísťování a vyrovnávání automaticky. Azure Cosmos DB nepodporuje ruční příkazy horizontálního dělení, což znamená, že nemusíte volat příkazy, jako je addShard, balancerStart, moveChunk atd. Při vytváření kontejnerů nebo dotazování na data stačí zadat horizontálních oddílů klíč.

## <a name="sessions"></a>Relace

Azure Cosmos DB ještě nepodporuje příkazy relací na straně serveru.

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Azure Cosmos DB podporuje hodnotu TTL (Time-to-Live) na základě časového razítka dokumentu. Hodnotu TTL lze povolit pro kolekce, a to tak, že se vrátíte do [Azure Portal](https://portal.azure.com).

## <a name="transactions"></a>Transakce

Transakce s více dokumenty jsou podporovány v rámci kolekce unsharded. Transakce s více dokumenty nejsou v kolekcích nebo v kolekcích horizontálně dělené podporovány. Časový limit pro transakce je pevný 5 sekund.

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Azure Cosmos DB zatím nepodporuje uživatele a role. Cosmos DB však podporuje řízení přístupu na základě role Azure (Azure RBAC) a hesla pro čtení i zápis a hesla/klíče jen pro čtení, které lze získat pomocí [Azure Portal](https://portal.azure.com) (stránka připojovací řetězec).

## <a name="write-concern"></a>Write Concern

Některé aplikace spoléhají na [důležité operace zápisu](https://docs.mongodb.com/manual/reference/write-concern/), která určuje počet odpovědí vyžadovaných během operace zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Všechny důležité problémy s zápisy, které jsou určeny klientským kódem, se ignorují. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
