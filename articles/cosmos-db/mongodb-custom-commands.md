---
title: Příkazy rozšíření MongoDB pro správu dat v rozhraní API Služby Azure Cosmos DB pro MongoDB
description: Tento článek popisuje, jak používat příkazy rozšíření MongoDB ke správě dat uložených v rozhraní API Služby Azure Cosmos DB pro MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445210"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Použití příkazů rozšíření MongoDB ke správě dat uložených v rozhraní API služby Azure Cosmos DB pro MongoDB 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete komunikovat s rozhraním API Azure Cosmos DB pro MongoDB pomocí libovolného z open source [ovladačů klienta MongoDB](https://docs.mongodb.org/ecosystem/drivers). Rozhraní API Azure Cosmos DB pro MongoDB umožňuje použití existujících klientských ovladačů dodržováním [drátového protokolu MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Pomocí rozhraní API Azure Cosmos DB pro MongoDB můžete využívat výhody Cosmos DB, jako je globální distribuce, automatické rozdělení, vysoká dostupnost, záruky latence, automatické šifrování v klidovém stavu, zálohování a mnoho dalších, při zachování vašich investic. v aplikaci MongoDB.

## <a name="mongodb-protocol-support"></a>Podpora protokolu MongoDB

Ve výchozím nastavení je rozhraní API Azure Cosmos DB pro MongoDB kompatibilní se serverem MongoDB verze 3.2, další podrobnosti naleznete v [tématu podporované funkce a syntaxe](mongodb-feature-support.md). Funkce nebo operátory dotazů přidané v MongoDB verze 3.4 jsou aktuálně k dispozici jako náhled v rozhraní API Azure Cosmos DB pro MongoDB. Následující příkazy rozšíření podporují specifické funkce Azure Cosmos DB při provádění operací CRUD na datech uložených v rozhraní API Služby Azure Cosmos DB pro MongoDB:

* [Vytvořit databázi](#create-database)
* [Aktualizovat databázi](#update-database)
* [Získat databázi](#get-database)
* [Vytvořit kolekci](#create-collection)
* [Aktualizovat kolekci](#update-collection)
* [Získat kolekci](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Vytvořit databázi

Příkaz vytvořit rozšíření databáze vytvoří novou databázi MongoDB. Název databáze se používá z kontextu databáze, proti kterému je příkaz proveden. Formát příkazu CreateDatabase je následující:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| Customaction   |  řetězec  |   Název vlastního příkazu, musí být "CreateDatabase".      |
| offerThroughput | int  | Zřízená propustnost, kterou jste nastavili v databázi. Tento parametr je volitelný. |

### <a name="output"></a>Výstup

Vrátí výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Vytvoření databáze**

Chcete-li vytvořit databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Vytvoření databáze s propustností**

Chcete-li vytvořit databázi s názvem "test" a zřízenou propustnost 1000 ru, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aktualizovat databázi

Příkaz rozšíření aktualizace databáze aktualizuje vlastnosti přidružené k zadané databázi. V současné době můžete aktualizovat pouze vlastnost "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| Customaction    |    řetězec     |   Název vlastního příkazu. Musí být "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nová zřízená propustnost, kterou chcete nastavit v databázi.    |

### <a name="output"></a>Výstup

Vrátí výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Aktualizace zřízená propustnost přidružená k databázi**

Chcete-li aktualizovat zřízenou propustnost databáze s názvem "test" na 1200 ru, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Získat databázi

Příkaz get database extension vrátí databázový objekt. Název databáze se používá z kontextu databáze, proti kterému je příkaz proveden.

```
{
  customAction: "GetDatabase"
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  Customaction   |   řetězec      |   Název vlastního příkazu. Musí být "GetDatabase"|
        
### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `database`    |    `string`        |   Název databáze.      |
|   `provisionedThroughput`  |    `int`      |    Zřízená propustnost, která je nastavena v databázi. Toto je volitelný parametr odpovědi.     |

Pokud příkaz selže, je vrácena výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Získání databáze**

Chcete-li získat databázový objekt pro databázi s názvem "test", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Vytvořit kolekci

Příkaz create collection extension vytvoří novou kolekci MongoDB. Název databáze se používá z kontextu databáze, proti kterému je příkaz proveden. Formát příkazu CreateCollection je následující:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Následující tabulka popisuje parametry v rámci příkazu:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| Customaction    | řetězec | Název vlastního příkazu. Musí být "CreateCollection"     |
|  – kolekce      | řetězec | Název sbírky                                   |
| offerThroughput | int    | Zřízená propustnost nastavit v databázi. Jedná se o volitelný parametr |
| shardKey        | řetězec | Cesta klíče štěřidla k vytvoření oddílové kolekce. Jedná se o volitelný parametr |

### <a name="output"></a>Výstup

Vrátí výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Vytvoření neodsuzované kolekce**

Chcete-li vytvořit nestoličenou kolekci s názvem "testCollection" a zřízenou propustností 1000 ru, použijte následující příkaz: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Vytvoření oddílové kolekce**

Chcete-li vytvořit oborovou kolekci s názvem "testCollection" a zřízenou propustností 1000 ru, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aktualizovat kolekci

Příkaz rozšíření kolekce aktualizace aktualizuje vlastnosti přidružené k zadané kolekci.

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
|  Customaction   |   řetězec      |   Název vlastního příkazu. Musí být "UpdateCollection".      |
|   – kolekce   |   řetězec      |   Název kolekce.       |
| offerThroughput   |int|   Zřízená propustnost nastavit na kolekci.|

## <a name="output"></a>Výstup

Vrátí výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Aktualizace zřízená propustnost přidružená k kolekci**

Chcete-li aktualizovat zřízenou propustnost kolekce s názvem "testCollection" na 1200 ru, použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Získat kolekci

Příkaz get collection custom vrátí objekt kolekce.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Následující tabulka popisuje parametry v rámci příkazu:


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
| Customaction    |   řetězec      |   Název vlastního příkazu. Musí být "GetCollection".      |
|  – kolekce    |    řetězec     |    Název kolekce.     |

### <a name="output"></a>Výstup

Pokud je příkaz úspěšný, odpověď obsahuje dokument s následujícími poli


|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `database`    |    `string`     |   Název databáze.      |
| `collection`    |    `string`     |    Název kolekce.     |
|  `shardKeyDefinition`   |   `document`      |  Dokument specifikace indexu používaný jako klíč střepu. Toto je volitelný parametr odpovědi.       |
|  `provisionedThroughput`   |   `int`      |    Zřízená propustnost nastavit na kolekci. Toto je volitelný parametr odpovědi.     |

Pokud příkaz selže, je vrácena výchozí vlastní odpověď příkazu. Parametry ve výstupu naleznete ve [výchozím výstupu](#default-output) vlastního příkazu.

### <a name="examples"></a>Příklady

**Získejte sbírku**

Chcete-li získat objekt kolekce pro kolekci s názvem "testCollection", použijte následující příkaz:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Výchozí výstup vlastního příkazu

Pokud není zadán, vlastní odpověď obsahuje dokument s následujícími poli:

|**Pole**|**Typ** |**Popis** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stav odpovědi. 1 == úspěch. 0 == selhání.      |
| `code`    |   `int`      |   Vráceno pouze v případě, že se příkaz nezdařil (tj. ok == 0). Obsahuje kód chyby MongoDB. Toto je volitelný parametr odpovědi.      |
|  `errMsg`   |  `string`      |    Vráceno pouze v případě, že se příkaz nezdařil (tj. ok == 0). Obsahuje uživatelsky přívětivou chybovou zprávu. Toto je volitelný parametr odpovědi.      |

## <a name="next-steps"></a>Další kroky

Dále se můžete dozvědět následující koncepty Azure Cosmos DB: 

* [Indexování ve službě Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Platnost dat v Azure Cosmos DB automaticky s časem žít](../cosmos-db/time-to-live.md)
