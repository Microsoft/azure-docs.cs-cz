---
title: Funkční podpora Azure Cosmos DB pro MongoDB | Microsoft Docs
description: Poznejte funkční podporu, kterou rozhraní MongoDB API služby Azure Cosmos DB poskytuje pro MongoDB 3.4.
services: cosmos-db
author: alekseys
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 2c86cbe2ac9a0611873aca35480af92304abe5b5
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928682"
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Podpora rozhraní API MongoDB pro funkce a syntaxi MongoDB

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. S rozhraním MongoDB API databáze můžete komunikovat prostřednictvím libovolného Open Source klientského [ovladače](https://docs.mongodb.org/ecosystem/drivers) MongoDB. Rozhraní MongoDB API umožňuje použití existujících klientských ovladačů dodržováním [přenosového protokolu](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Při používání rozhraní MongoDB API služby Azure Cosmos DB můžete využít výhody rozhraní MongoDB API, na které jste zvyklí, se všemi podnikovými možnostmi, které nabízí Azure Cosmos DB: [globální distribuci](distribute-data-globally.md), [automatický sharding](partition-data.md), záruky dostupnosti a latence, automatické indexování všech polí, šifrování při nečinnosti, zálohy a spoustu dalších.

## <a name="mongodb-protocol-support"></a>Podpora protokolu MongoDB

Rozhraní MongoDB API služby Azure Cosmos DB je ve výchozím natavení kompatibilní se serverem MongoDB verze **3.2**. Podporované operátory a případná omezení nebo výjimky jsou uvedené níže. Funkce nebo operátory dotazu přidané v MongoDB verze **3.4** jsou v současné době dostupné jako funkce Preview. Pomocí rozhraní MongoDB API by se ke službě Cosmos DB měl být schopný připojit jakýkoli klient podporující tyto protokoly.

[Kanál agregace MongoDB](#aggregation-pipeline) je v současné době také dostupný jako samostatná funkce Preview.

## <a name="mongodb-query-language-support"></a>Podpora dotazovacího jazyka MongoDB

Rozhraní MongoDB API služby Azure Cosmos DB nabízí komplexní podporu konstruktorů dotazovacího jazyka MongoDB. Níže najdete podrobný seznam aktuálně podporovaných operací, operátorů, fází, příkazů a možností.

## <a name="database-commands"></a>Databázové příkazy

Azure Cosmos DB podporuje u všech účtů rozhraní MongoDB API následující databázové příkazy.

### <a name="query-and-write-operation-commands"></a>Příkazy pro dotazovací a zápisové operace
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Příkazy pro ověření
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Příkazy pro správu
- dropDatabase
- listCollections
- drop
- vytvoření
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Příkazy pro diagnostiku
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Agregační zřetězení</a>

Azure Cosmos DB podporuje ve veřejné verzi Preview agregační zřetězení. Pokyny k registraci veřejné verze Preview najdete na [blogu Azure](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Příkazy pro agregaci
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fáze agregace
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Výrazy pro agregaci

#### <a name="boolean-expressions"></a>Logické výrazy
- $and
- $or
- $not

#### <a name="set-expressions"></a>Nastavovací výrazy
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Porovnávací výrazy
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetické výrazy
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Řetězcové výrazy
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Maticové výrazy
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Datumové výrazy
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Podmíněné výrazy
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Agregační zásobníky
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátory

Jsou podporované následující operátory, doplněné o odpovídající příklady použití. V níže uvedených dotazech se používá tento ukázkový dokument:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operátor | Příklad |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Poznámky

V dotazech $regex umožňují zleva ukotvené výrazy hledání indexu. Použití modifikátorů „i“ (rozlišování malých a velkých písmen) a „m“ (více řádků) ale způsobí, že se kolekce prohledává ve všech výrazech.
Pokud potřebujete začlenit „$“ nebo „|“, je nejlepší vytvořit dva (nebo více) dotazů využívajících regulární výrazy. Například následující původní dotaz ```find({x:{$regex: /^abc$/})``` by se měl upravit takto: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
V první části se použije index k omezení hledání na dokumenty, které začínají na ^abc a ve druhé části se porovnají přesné položky. Operátor „|“ se chová jako funkce „nebo“ – dotaz ```find({x:{$regex: /^abc|^def/})``` porovnává dokumenty, ve kterých pole „x“ nabývá hodnot, které začínají na „abc“ nebo „def“. Pokud chcete využít index, je doporučeno dotaz rozdělit na dva různé dotazy spojené operátorem $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Aktualizační operátory

#### <a name="field-update-operators"></a>Operátory pro aktualizaci polí
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operátory pro aktualizaci matic
- $addToSet
- $pop
- $pullAll
- $pull (Poznámka: $pull s podmínkou se nepodporuje.)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operátor pro bitovou aktualizaci
- $bit

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Příklad 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ano
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ano
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ano
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano

## <a name="additional-operators"></a>Další operátory

Operátor | Příklad | Poznámky 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nepodporuje se. Použijte místo toho $regex.

## <a name="unsupported-operators"></a>Nepodporované operátory

Azure Cosmos DB nepodporuje operátory ```$where``` a ```$eval```.

### <a name="methods"></a>Metody

Podporují se následující metody:

#### <a name="cursor-methods"></a>Metody kurzoru

Metoda | Příklad | Poznámky 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumenty bez klíče řazení se nevracejí

## <a name="unique-indexes"></a>Jedinečné indexy

Azure Cosmos DB standardně indexuje všechna pole v dokumentech, která jsou zapsaná do databáze. Jedinečné indexy zajišťují, aby určité pole nemělo duplicitní hodnoty v rámci všech dokumentů v kolekci, což se podobá zachování jedinečnosti u výchozího klíče „_id“. Ve službě Azure Cosmos DB teď můžete vyvářet vlastní indexy příkazem createIndex včetně omezení „unique“.

Jedinečné indexy jsou dostupné pro všechny účty rozhraní MongoDB API.

## <a name="time-to-live-ttl"></a>Hodnota TTL (Time-To-Live)

Azure Cosmos DB podporuje relativní hodnotu TTL (Time-To-Live) založenou na časovém razítku dokumentu. Hodnotu TTL lze povolit u kolekcí rozhraní MongoDB API přes [Azure Portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Azure Cosmos DB zatím nepodporuje uživatele a role. Azure Cosmos DB podporuje řízení přístupu na základě rolí (RBAC) a hesla/klíče pro čtení a zápis nebo jen pro čtení, které lze získat prostřednictvím [Azure Portalu](https://portal.azure.com) (stránka Připojovací řetězec).

## <a name="replication"></a>Replikace

Azure Cosmos DB podporuje automatickou nativní replikaci v nejnižších vrstvách. Tato logika umožňuje zároveň dosáhnout nízké latence a globální replikace. Azure Cosmos DB nepodporuje příkazy pro ruční replikaci.

## <a name="write-concern"></a>Write Concern

Některá rozhraní MongoDB API podporují možnost zadat [Write Concern](https://docs.mongodb.com/manual/reference/write-concern/), který určuje počet požadovaných odpovědí při operaci zápisu. Kvůli tomu, jak Cosmos DB zpracovává replikaci na pozadí, jsou všechny zápisy ve výchozím nastavení automaticky kvora. Každý Write Concern určený klientským kódem se ignoruje. Další informace najdete v tématu popisujícím [využití úrovní konzistence pro maximalizaci dostupnosti a výkonu](consistency-levels.md).

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatický sharding na straně serveru. Azure Cosmos DB nepodporuje příkazy pro ruční sharding.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API pro databázi MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API pro databázi MongoDB.
- Prozkoumejte Azure Cosmos DB pomocí podpory protokolů pro [ukázky](mongodb-samples.md) MongoDB.
