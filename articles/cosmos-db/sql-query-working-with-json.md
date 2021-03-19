---
title: Práce s formátem JSON ve službě Azure Cosmos DB
description: Další informace o dotazování a přístupu k vnořeným vlastnostem JSON a použití speciálních znaků v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337825"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Práce s formátem JSON ve službě Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V rozhraní SQL (Core) API služby Azure Cosmos DB se položky ukládají ve formátu JSON. Systém typů a výrazy jsou omezené tak, aby pracovaly pouze s typy JSON. Další informace najdete v tématu [specifikace JSON](https://www.json.org/).

Shrnujeme některé důležité aspekty práce s JSON:

- Objekty JSON vždycky začínají `{` levou složenou závorku a končí `}` pravou závorkou.
- Vlastnosti JSON můžete mít [vnořené](#nested-properties) mezi sebou.
- Hodnoty vlastností JSON můžou být pole.
- V názvech vlastností JSON se rozlišují velká a malá písmena.
- Název vlastnosti JSON může být libovolná hodnota řetězce (včetně mezer nebo znaků, které nejsou písmena).

## <a name="nested-properties"></a>Vnořené vlastnosti

K vnořenému JSON můžete přistupovat pomocí přístupového objektu s tečkami. Ve svých dotazech můžete použít vnořené vlastnosti JSON stejným způsobem jako ostatní vlastnosti.

Zde je dokument s vnořeným kódem JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

V tomto případě `state` `country` `city` jsou vlastnosti, a všechny vnořené v rámci `address` Vlastnosti.

Následující příklad projekty jsou dvě vnořené vlastnosti: `f.address.state` a `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Práce s poli

Kromě vnořených vlastností JSON podporuje také pole.

Zde je příklad dokumentu s polem:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Při práci s poli můžete získat přístup k určitému prvku v rámci pole odkazem na jeho pozici:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Ve většině případů ale při práci s poli použijete [poddotaz](sql-query-subquery.md) nebo [samostatné spojení](sql-query-join.md) .

Tady je například dokument, který zobrazuje denní zůstatek bankovního účtu zákazníka.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Pokud jste chtěli spustit dotaz, který ukázal všechny zákazníky s negativním zůstatkem, [můžete použít s](sql-query-subquery.md#exists-expression) poddotazem:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Rozdíl mezi hodnotou null a nedefinovaným

Pokud vlastnost není definována v položce, její hodnota je `undefined` . Vlastnost s hodnotou `null` musí být explicitně definována a přiřazena `null` hodnota.

Zvažte například tuto ukázkovou položku:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

V tomto příkladu `isRegistered` má vlastnost hodnotu, `undefined` protože je vynechána z položky. Vlastnost `creationDate` má `null` hodnotu.

Azure Cosmos DB podporuje dvě užitečné funkce kontroly typů pro `null` a `undefined` vlastnosti:

* [IS_NULL](sql-query-is-null.md) – kontroluje, jestli je hodnota vlastnosti `null`
* [IS_DEFINED](sql-query-is-defined.md) – kontroluje, jestli je definovaná hodnota vlastnosti.

Můžete získat informace o [podporovaných operátorech](sql-query-operators.md) a jejich chování `null` pro `undefined` hodnoty a.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Vyhrazená klíčová slova a speciální znaky ve formátu JSON

K vlastnostem můžete přistupovat pomocí operátoru vlastnosti v uvozovkách `[]` . Například `SELECT c.grade` a `SELECT c["grade"]` jsou ekvivalentní. Tato syntaxe je užitečná pro řídicí znak, který obsahuje mezery, speciální znaky nebo má stejný název jako klíčové slovo SQL nebo vyhrazené slovo.

Například zde je dokument s vlastností s názvem `order` a vlastností `price($)` , která obsahuje speciální znaky:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Pokud spustíte dotazy, které obsahují `order` vlastnost nebo `price($)` vlastnost, dojde k chybě syntaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Výsledek je následující:

`
Syntax error, incorrect syntax near 'order'
`

Stejné dotazy byste měli přepsat následujícím způsobem:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Výrazy JSON

Projekce také podporuje výrazy JSON, jak je znázorněno v následujícím příkladu:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

V předchozím příkladu `SELECT` musí klauzule vytvořit objekt JSON a protože ukázka neposkytuje žádný klíč, klauzule používá název proměnné implicitního argumentu `$1` . Následující dotaz vrátí dvě proměnné implicitního argumentu: `$1` a `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Používání aliasů

V dotazech můžete explicitně aliasovat hodnoty. Pokud má dotaz dvě vlastnosti se stejným názvem, použijte aliasing pro přejmenování jedné nebo obou vlastností tak, aby byly v očekávaném výsledku nejednoznačné.

### <a name="examples"></a>Příklady

`AS`Klíčové slovo použité pro aliasing je volitelné, jak je znázorněno v následujícím příkladu při projekci druhé hodnoty jako `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Výsledky jsou následující:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing s vyhrazenými klíčovými slovy nebo speciálními znaky

Aliasing nelze použít k projekci hodnoty jako názvu vlastnosti s mezerou, speciálním znakem nebo rezervovaným slovem. Pokud jste chtěli změnit projekci hodnoty například na název vlastnosti s mezerou, můžete použít [výraz JSON](#json-expressions).

Tady je příklad:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule WHERE](sql-query-where.md)
