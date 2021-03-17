---
title: Klíčová slova SQL pro Azure Cosmos DB
description: Seznamte se s klíčovými slovy SQL pro Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 1f3c4ef56feb77e9b01375b8b5dbdb567f5bfadb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179965"
---
# <a name="keywords-in-azure-cosmos-db"></a>Klíčová slova v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek podrobně popisuje klíčová slova, která se dají použít v Azure Cosmos DBch dotazech SQL.

## <a name="between"></a>BETWEEN

Klíčové slovo lze použít `BETWEEN` pro vyjádření dotazů na rozsahy řetězcových nebo číselných hodnot. Například následující dotaz vrátí všechny položky, ve kterých je první podřízená hodnota 1-5 (včetně).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Klíčové slovo lze použít také `BETWEEN` v `SELECT` klauzuli, jak je uvedeno v následujícím příkladu.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

V rozhraní SQL API na rozdíl od ANSI SQL můžete vyjádřit dotazy na rozsah proti vlastnostem smíšených typů. `grade`Může se například jednat o číslo, například `5` v některých položkách, a v řetězci podobném `grade4` jiným. V těchto případech, jako v jazyce JavaScript, porovnávání dvou různých typů má za následek `Undefined` , že se položka přeskočí.

## <a name="distinct"></a>DISTINCT

`DISTINCT`Klíčové slovo eliminuje duplicity v projekci dotazu.

V tomto příkladu se dotazují hodnoty pro každý poslední název:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Výsledky jsou následující:

```json
[
    "Andersen"
]
```

Můžete také projektovat jedinečné objekty. V takovém případě pole lastName v jednom ze dvou dokumentů neexistuje, takže dotaz vrátí prázdný objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Výsledky jsou následující:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` lze také použít v projekci v rámci poddotazu:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Tento dotaz projektuje pole, které obsahuje dané jméno dítěte s odebranými duplicitami. Toto pole má aliasy jako ChildNames a prochází z vnějšího dotazu.

Výsledky jsou následující:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Dotazy s agregovanou systémovou funkcí a poddotazem s `DISTINCT` nejsou podporovány. Například následující dotaz není podporován:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>LIKE

Vrací logickou hodnotu v závislosti na tom, zda konkrétní řetězec znaků odpovídá zadanému vzoru. Vzor může obsahovat běžné znaky a zástupné znaky. Můžete psát logicky ekvivalentní dotazy pomocí `LIKE` klíčového slova nebo [RegexMatch](sql-query-regexmatch.md) systémové funkce. Můžete sledovat stejné využití indexu bez ohledu na to, kterou si zvolíte. Proto byste měli použít, `LIKE` Pokud dáváte přednost její syntaxi více než regulární výrazy.

> [!NOTE]
> Vzhledem `LIKE` k tomu, že může používat index, byste měli [vytvořit index rozsahu](./index-policy.md) pro vlastnosti, které porovnáváte pomocí `LIKE` .

Můžete použít následující zástupné znaky, jako například:

| Zástupný znak | Popis                                                  | Příklad                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Libovolný řetězec nula nebo více znaků                      | KDE c. Description jako "% SO% PS%"      |
| _ (podtržítko)     | Libovolný jeden znak                                       | KDE c. Description jako "% SO_PS%"      |
| [ ]                  | Libovolný jeden znak v zadaném rozsahu ([a-f]) nebo set ([abcdef]). | KDE c. Description jako "% SO [t-z] PS%"  |
| [^]                  | Libovolný jeden znak, který není v zadaném rozsahu ([^ a-f]) nebo set ([^ abcdef]). | KDE c. Description jako "% SO [^ abc] PS%" |


### <a name="using-like-with-the--wildcard-character"></a>Použití LIKE se zástupným znakem%

`%`Znak odpovídá jakémukoli řetězci nula nebo více znaků. Například umístěním na `%` začátek a konec vzoru vrátí následující dotaz všechny položky s popisem, který obsahuje `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Pokud jste `%` na konci vzoru použili pouze znak, vrátíte pouze položky s popisem, který byl vytvořen pomocí `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Použití NOT LIKE

Následující příklad vrátí všechny položky s popisem, který neobsahuje `fruit` :

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Použití řídicí klauzule

Můžete vyhledat vzory, které zahrnují jeden nebo více zástupných znaků pomocí řídicí klauzule. Například pokud jste chtěli Hledat popisy, které obsahovaly řetězec `20-30%` , nechcete interpretovat `%` jako zástupný znak.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Použití zástupných znaků jako literálů

Zástupné znaky můžete v závorkách uzavřít, aby byly považovány za literální znaky. Když v hranatých závorkách uzavíráte zástupný znak, odeberete všechny speciální atributy. Tady je několik příkladů:

| Vzor           | Význam |
| ----------------- | ------- |
| NAPŘÍKLAD "20-30 [%]" | 20-30%  |
| LIKE "[_] n"     | _n      |
| LIKE "[[]"    | [       |
| LIKE "]"        | ]       |

## <a name="in"></a>IN

Použijte klíčové slovo IN ke kontrole, zda zadaná hodnota odpovídá jakékoli hodnotě v seznamu. Například následující dotaz vrátí všechny rodinné položky, kde `id` je `WakefieldFamily` nebo `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Následující příklad vrátí všechny položky, kde je stav kterákoli z určených hodnot:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

Rozhraní SQL API poskytuje podporu pro [iteraci přes pole JSON](sql-query-object-array.md#Iteration)s novou konstrukcí přidanou prostřednictvím klíčového slova in ve zdroji from.

Pokud zahrnete klíč oddílu do `IN` filtru, dotaz se automaticky vyfiltruje jenom na relevantní oddíly.

## <a name="top"></a>TOP

Klíčové slovo TOP vrátí první `N` počet výsledků dotazu v nedefinovaném pořadí. Jako osvědčený postup použijte klauzuli TOP s `ORDER BY` klauzulí k omezení výsledků na první `N` počet seřazených hodnot. Kombinování těchto dvou klauzulí je jediným způsobem, jak prediktivním označovat, které řádky mají vliv na nejvyšší vliv.

Můžete použít TOP s konstantní hodnotou, jako v následujícím příkladu, nebo s hodnotou proměnné s použitím parametrizovaných dotazů.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Výsledky jsou následující:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Spojení](sql-query-join.md)
- [Poddotazy](sql-query-subquery.md)
