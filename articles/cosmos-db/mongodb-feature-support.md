---
title: Azure Cosmos DB funkce – Podpora pro MongoDB | Microsoft Docs
description: Další informace o podporovaných funkcích, které poskytuje rozhraní API služby Azure DB Cosmos MongoDB pro MongoDB 3.4.
services: cosmos-db
author: alekseys
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 470ad2fe458c47392b8f55a8f46c75e54ccf87a3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Podpora rozhraní API MongoDB pro MongoDB funkce a syntaxe

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Může komunikovat s rozhraním API databázi MongoDB do některého z klienta s otevřeným zdrojem MongoDB [ovladače](https://docs.mongodb.org/ecosystem/drivers). Rozhraní API MongoDB umožňuje použití existujících ovladačů klienta tak, že se MongoDB [propojit protokol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Pomocí rozhraní API služby Azure DB Cosmos MongoDB, můžete využívat výhod API MongoDB jste zvyklí, se všechny funkce enterprise Azure Cosmos DB: [globální distribuční](distribute-data-globally.md), [automatického dělení](partition-data.md), dostupnost a latence záruky automatické indexování každé pole, šifrování v rest, zálohování a mnoho dalšího.

## <a name="mongodb-query-language-support"></a>MongoDB dotazu jazyková podpora

Rozhraní API MongoDB DB Cosmos Azure poskytuje komplexní podporu pro jazykové konstrukty dotazů MongoDB. Níže najdete podrobný seznam aktuálně podporované operace, operátory, fáze, příkazy a možnosti.


## <a name="database-commands"></a>Příkazy databáze

Azure Cosmos DB podporuje následující databáze příkazy ke všem účtům rozhraní API MongoDB. 

### <a name="query-and-write-operation-commands"></a>Příkazy pro operaci dotazu a zápisu
- odstraňovat
- Najít
- findAndModify
- getLastError
- getMore
- Vložení
- update

### <a name="authentication-commands"></a>Příkazy ověřování
- odhlásit
- ověření
- getnonce

### <a name="administration-commands"></a>Příkazy pro správu
- dropDatabase
- listCollections
- přetažení
- vytvoření
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- ConnectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Příkazy diagnostiky
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Agregace kanálu</a>

Azure Cosmos DB podporuje kanál agregace ve verzi public preview. Najdete v článku [Azure blog](https://aka.ms/mongodb-aggregation) pokyny, jak se budou registrovat pro verzi public preview.

### <a name="aggregation-commands"></a>Příkazy agregace
- agregace
- počet
- Odlišné

### <a name="aggregation-stages"></a>Agregace fázích
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

### <a name="aggregation-expressions"></a>Agregační výrazy

#### <a name="boolean-expressions"></a>Logické výrazy
- $a
- $nebo
- $not

#### <a name="set-expressions"></a>Sada výrazů
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Výrazy porovnání
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetických výrazech
- $abs
- Přidat $
- ceil – $
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $násobení
- $pow
- $sqrt
- odečtena $
- $trunc

#### <a name="string-expressions"></a>Řetězec výrazy
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

#### <a name="array-expressions"></a>Pole výrazy
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $v

#### <a name="date-expressions"></a>Datum výrazy
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $druhý
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Podmíněné výrazy
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Agregace akumulátorů
- $sum
- $avg
- $první
- $poslední
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátory

Podporuje následující operátory odpovídající příkladů jeho použití. Vezměte v úvahu tento dokument ukázka používají v dotazech níže:

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

Operátor | Příklad: |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$v | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$nebo | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$a | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$ani | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
existuje $ | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Poznámky

V dotazech $regex povolit ukotvené vlevo výrazy indexu vyhledávání. Však pomocí 'i' – modifikátor (velkých a malých) a 'm ' – modifikátor (multiline) způsobí, že kontrola kolekce v všechny výrazy.
Pokud je potřeba zahrnout '$' nebo ' |', je vhodné vytvořit dotazy, regex dva (nebo více). Například uděleno následující původní dotaz: ```find({x:{$regex: /^abc$/})```, musí být upraven následujícím způsobem: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
První část použije index omezit výsledky vyhledávání na tyto dokumenty počínaje ^ abc a druhá část bude odpovídat přesně položky. Na panelu operátor ' |' funguje jako funkce "nebo" - dotaz ```find({x:{$regex: /^abc|^def/})``` odpovídá dokumenty, ve kterém poli 'x' má hodnoty, které začínají řetězcem "abc" nebo "def". Abyste mohli využívat indexu, se doporučuje rozdělit na dvě různé dotazy, které jsou připojené k $ nebo – operátor dotazu: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Aktualizace operátory

#### <a name="field-update-operators"></a>Pole aktualizace operátory
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $nenastavené
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Pole aktualizace operátory
- $addToSet
- $pop
- $pullAll
- $pull (Poznámka: není podporováno $pull s podmínkou)
- $pushAll
- $push
- $Každý
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Operátor bitového aktualizace
- $bit

### <a name="geospatial-operators"></a>Geoprostorové operátory

Operátor | Příklad: 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$téměř | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ano
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ano
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ano
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ano
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ano
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ano

## <a name="additional-operators"></a>Další operátory

Operátor | Příklad: | Poznámky 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nepodporuje se. Místo toho použijte $regex 

### <a name="methods"></a>Metody

Podporovány jsou následující metody:

#### <a name="cursor-methods"></a>Kurzor metody

Metoda | Příklad: | Poznámky 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Získat nebyla vrácena dokumenty bez klíč řazení

## <a name="unique-indexes"></a>Jedinečné indexy

Azure Cosmos DB indexuje každé pole v dokumentech, které jsou podle výchozího nastavení zapisovány do databáze. Jedinečné indexy zkontrolujte, zda konkrétní pole bude obsahovat duplicitní hodnoty na všech dokumentech v kolekci, podobně jako jedinečnosti způsob, jak se zachová, i na klíči _id"Výchozí". Nyní můžete vytvořit vlastní indexy v databázi Cosmos Azure pomocí příkazu createIndex, včetně 'jedinečné, omezení.

Jedinečné indexy jsou k dispozici pro všechny účty rozhraní API MongoDB.

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

Azure Cosmos DB podporuje relativní time to live (TTL) podle časového razítka dokumentu. Hodnota TTL se dá nastavit pro rozhraní API MongoDB kolekce prostřednictvím [portál Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Správa uživatelů a rolí

Azure Cosmos DB zatím nepodporuje uživatelů a rolí. Řízení přístupu na základě role (RBAC) podporuje Azure Cosmos DB a pro čtení a zápis a jen pro čtení hesla nebo klíče, které můžete získat prostřednictvím [portál Azure](https://portal.azure.com) (stránka připojovací řetězec).

## <a name="replication"></a>Replikace

Azure Cosmos DB podporuje automatické, nativní replikace na nejnižší vrstvy. Tato logika je rozšířeno na zajistit také s nízkou latencí, globální replikace. Azure Cosmos DB nepodporuje příkazy ruční replikace.

## <a name="sharding"></a>Sharding

Azure Cosmos DB podporuje automatické, serverový horizontálního dělení. Azure Cosmos DB nepodporuje příkazy ruční horizontálního dělení.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [použít Studio 3T](mongodb-mongochef.md) pomocí rozhraní API pro databázi MongoDB.
- Zjistěte, jak [použít Robo 3T](mongodb-robomongo.md) pomocí rozhraní API pro databázi MongoDB.
- Seznamte se s podporou protokolů pro MongoDB Azure Cosmos DB [ukázky](mongodb-samples.md).
