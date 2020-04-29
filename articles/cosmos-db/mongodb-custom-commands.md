---
title: Příkazy rozšíření MongoDB pro správu dat v rozhraní API Azure Cosmos DB pro MongoDB
description: Tento článek popisuje, jak pomocí příkazů rozšíření MongoDB spravovat data uložená v rozhraní API Azure Cosmos DB pro MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583582"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Použití příkazů rozšíření MongoDB ke správě dat uložených v rozhraní API Azure Cosmos DB pro MongoDB 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí kteréhokoli z open-source [klientských ovladačů MongoDB](https://docs.mongodb.org/ecosystem/drivers). Rozhraní API pro Azure Cosmos DB pro MongoDB umožňuje použití existujících ovladačů klientů, které dodržuje [MongoDB síťový protokol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Díky rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhod Cosmos DB, jako je globální distribuce, automatická horizontálního dělení, vysoká dostupnost, záruky latence, automatické, šifrování v klidovém režimu, zálohování a spousta dalších, a zároveň zachovat investice do vaší aplikace MongoDB.

## <a name="mongodb-protocol-support"></a>Podpora protokolu MongoDB

Ve výchozím nastavení je rozhraní API Azure Cosmos DB pro MongoDB kompatibilní s MongoDB serverem verze 3,2. Další informace najdete v tématu [podporované funkce a syntaxe](mongodb-feature-support.md). Funkce nebo operátory dotazů přidané v MongoDB verze 3,4 jsou aktuálně k dispozici jako náhled v rozhraní API Azure Cosmos DB pro MongoDB. Následující příkazy rozšíření podporují konkrétní funkce Azure Cosmos DB při provádění operací CRUD s daty uloženými v rozhraní Azure Cosmos DB API pro MongoDB:

* [Vytvořit databázi](#create-database)
* [Aktualizovat databázi](#update-database)
* [Získat databázi](#get-database)
* [Vytvořit kolekci](#create-collection)
* [Aktualizovat kolekci](#update-collection)
* [Získat kolekci](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Vytvořit databázi

Příkaz vytvořit rozšíření databáze vytvoří novou databázi MongoDB. Název databáze se používá z kontextu databáze, proti které je příkaz spuštěn. Formát příkazu CreateDatabase je následující:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction   |  řetězec  |   Název vlastního příkazu musí být "CreateDatabase".      |
| offerThroughput | int  | Zřízená propustnost, kterou jste nastavili v databázi. Tento parametr je volitelný. |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Vytvoření databáze**

Chcete-li vytvořit databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Vytvoření databáze s propustností**

K vytvoření databáze s názvem "test" a zajištěné propustnosti 1000 ru použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aktualizovat databázi

Příkaz aktualizovat databázi rozšíření aktualizuje vlastnosti přidružené k zadané databázi. V současné době můžete aktualizovat pouze vlastnost "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction    |    řetězec     |   Název vlastního příkazu Musí být "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nová zřízená propustnost, kterou chcete nastavit v databázi.    |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Aktualizace zřízené propustnosti přidružené k databázi**

K aktualizaci zřízené propustnosti databáze s názvem "test" na 1200 ru použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Získat databázi

Příkaz Get Database Extension vrátí objekt databáze. Název databáze je použit z kontextu databáze, proti kterému je příkaz spuštěn.

```
{
  customAction: "GetDatabase"
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  customAction   |   řetězec      |   Název vlastního příkazu Musí být "getdatabase"|
        
### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `database`    |    `string`        |   Název databáze.      |
|   `provisionedThroughput`  |    `int`      |    Zřízená propustnost, která je nastavena v databázi. Toto je volitelný parametr odpovědi.     |

Pokud příkaz neproběhne úspěšně, vrátí se výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Získat databázi**

Chcete-li získat objekt databáze pro databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Vytvořit kolekci

Příkaz vytvořit rozšíření kolekce vytvoří novou kolekci MongoDB. Název databáze se používá z kontextu databáze, proti které je příkaz spuštěn. Formát příkazu Vytvořitcollection je následující:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Následující tabulka popisuje parametry v rámci příkazu:

| **Pole** | **Typ** | **Požadováno** | **Popis** |
|---------|---------|---------|---------|
| customAction | řetězec | Požaduje se | Název vlastního příkazu Musí být "Vytvořitcollection".|
|  – kolekce | řetězec | Požaduje se | Název kolekce Nejsou povoleny žádné speciální znaky.|
| offerThroughput | int | Volitelné | Zřízená propustnost pro nastavení databáze. Pokud tento parametr není zadán, bude výchozí hodnota minimálně 400 RU/s. * Pokud chcete zadat propustnost přesahující 10 000 RU/s, `shardKey` parametr je povinný.|
| shardKey | řetězec | Volitelné | Cesta k horizontálních oddílůmu klíči pro kolekci horizontálně dělené Tento parametr je vyžadován, pokud nastavíte více než 10 000 RU/s `offerThroughput`v.  Pokud je zadaný, bude tato hodnota vyžadovat všechny vložené dokumenty. |

### <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Vytvoření kolekce unsharded**

Chcete-li vytvořit kolekci unsharded s názvem "TestCollection" a zřízenou propustností 1000 ru, použijte následující příkaz: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Vytvoření kolekce horizontálně dělené**

Chcete-li vytvořit kolekci horizontálně dělené s názvem "TestCollection" a zřízenou propustností 1000 ru a vlastnost shardkey "a. b", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aktualizovat kolekci

Příkaz rozšíření kolekce aktualizací aktualizuje vlastnosti přidružené k zadané kolekci.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  customAction   |   řetězec      |   Název vlastního příkazu Musí být "Updatecollection".      |
|   – kolekce   |   řetězec      |   Název kolekce       |
| offerThroughput   |int|   Zřízená propustnost, která se má nastavit v kolekci|

## <a name="output"></a>Výstup

Vrátí výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Aktualizace zřízené propustnosti přidružené ke kolekci**

Chcete-li aktualizovat zřízenou propustnost kolekce s názvem "TestCollection" na 1200 ru, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Získat kolekci

Vlastní příkaz Get Collection vrátí objekt kolekce.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| customAction    |   řetězec      |   Název vlastního příkazu Musí být "GetCollection".      |
|  – kolekce    |    řetězec     |    Název kolekce     |

### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `database`    |    `string`     |   Název databáze.      |
| `collection`    |    `string`     |    Název kolekce     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specifikace indexu použitý jako horizontálních oddílů klíč Toto je volitelný parametr odpovědi.       |
|  `provisionedThroughput`   |   `int`      |    Zřízená propustnost, která se má nastavit v kolekci Toto je volitelný parametr odpovědi.     |

Pokud příkaz neproběhne úspěšně, vrátí se výchozí odpověď vlastního příkazu. Podívejte se na [výchozí výstup](#default-output) vlastního příkazu pro parametry ve výstupu.

### <a name="examples"></a>Příklady

**Získat kolekci**

Chcete-li získat objekt kolekce pro kolekci s názvem "TestCollection", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Výchozí výstup vlastního příkazu

Pokud tento parametr nezadáte, vlastní odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi 1 = = úspěch. 0 = = chyba.      |
| `code`    |   `int`      |   Vrátí se jenom v případě, že se příkaz nezdařil (tj. ok = = 0). Obsahuje kód chyby MongoDB. Toto je volitelný parametr odpovědi.      |
|  `errMsg`   |  `string`      |    Vrátí se jenom v případě, že se příkaz nezdařil (tj. ok = = 0). Obsahuje uživatelsky přívětivou chybovou zprávu. Toto je volitelný parametr odpovědi.      |

## <a name="next-steps"></a>Další kroky

Dále si můžete přečíst následující pojmy Azure Cosmos DB: 

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Automatické vypršení platnosti dat v Azure Cosmos DB s časem až Live](../cosmos-db/time-to-live.md)
