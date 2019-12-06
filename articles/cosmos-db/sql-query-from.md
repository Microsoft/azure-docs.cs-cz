---
title: Klauzule FROM v Azure Cosmos DB
description: Přečtěte si o syntaxi SQL a příklad pro klauzuli FROM pro Azure Cosmos DB. Tento článek také ukazuje příklady určení rozsahu výsledků a získání dílčích položek pomocí klauzule FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 4f6d7580ea7ff0e8968c0c3ce4b3ca6111c86ac8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873365"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzule FROM v Azure Cosmos DB

Klauzule FROM (`FROM <from_specification>`) je volitelná, pokud se zdroj nefiltruje nebo se v dotazu později nepoužívá. Dotaz, jako je `SELECT * FROM Families`, se vyčísluje v celém kontejneru `Families`. Místo použití názvu kontejneru můžete použít také kořenový kořenový identifikátor pro kontejner.

Klauzule FROM vynutila následující pravidla na dotaz:

* Kontejner může být například vytvořen alias, `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Zde je `f` alias pro `Families`. Stejně jako volitelné klíčové slovo pro [alias](sql-query-aliasing.md) identifikátoru.  

* Po aliasu nelze původní název zdroje svázat. Například `SELECT Families.id FROM Families f` je syntakticky neplatný, protože `Families` identifikátoru byl alias vytvořen a již jej nelze přeložit.  

* Všechny odkazované vlastnosti musí být plně kvalifikované, aby nedocházelo k nejednoznačným vazbám při neexistenci striktního dodržování schématu. Například `SELECT id FROM Families f` je syntakticky neplatný, protože `id` vlastností není svázáno.

## <a name="syntax"></a>Syntaxe
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<from_source>`  
  
  Určuje zdroj dat, s nebo bez něj alias. Pokud není zadaný alias, bude odvodit z `<container_expression>` pomocí následujících pravidel:  
  
  -  Pokud má výraz hodnotu container_name, bude container_name použít jako alias.  
  
  -  Pokud má výraz hodnotu `<container_expression>`, pak %{Property_Name/ pak %{Property_Name/ se použije jako alias. Pokud má výraz hodnotu container_name, bude container_name použít jako alias.  
  
- STEJNĚ JAKO `input_alias`  
  
  Určuje, že `input_alias` je sada hodnot vrácených základní výraz kontejneru.  
 
- `input_alias` INDIE  
  
  Určuje, že `input_alias` by měly představovat sadu hodnot získala iterace přes všechny prvky pole každé pole vrácené výrazem základní kontejneru. Libovolnou hodnotu vrácenou základní výraz kontejneru, který není pole se ignoruje.  
  
- `<container_expression>`  
  
  Určuje výraz kontejneru se použije k načtení dokumentů.  
  
- `ROOT`  
  
  Určuje, že tento dokument by mělo být získáno z výchozí hodnoty, aktuálně připojeného kontejneru.  
  
- `container_name`  
  
  Určuje, že tento dokument by měl být načten ze zadaného kontejneru. Název kontejneru musí odpovídat názvu kontejneru aktuálně připojeni.  
  
- `input_alias`  
  
  Určuje, že tento dokument by měl být načten z jiného zdroje určené zadaný alias.  
  
- `<container_expression> '.' property_`  
  
  Určuje tento dokument by mělo být získáno díky přístupu `property_name` vlastnost nebo array_index prvek pole pro všechny dokumenty načte zadaný výraz kontejneru.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Určuje tento dokument by mělo být získáno díky přístupu `property_name` vlastnost nebo array_index prvek pole pro všechny dokumenty načte zadaný výraz kontejneru.  
  
## <a name="remarks"></a>Poznámky
  
K dispozici všechny aliasy nebo odvozen `<from_source>(`s) musí být jedinečný. Syntaxe `<container_expression>.`%{Property_Name/ je stejný jako `<container_expression>' ['"property_name"']'`. Druhá možnost syntaxi však můžete použít, pokud název vlastnosti obsahuje znak identifikátoru.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>zpracování chybí vlastnosti chybějící prvky pole a nedefinované hodnoty
  
Pokud výraz kontejneru, který přistupuje k vlastnosti nebo prvky pole a že hodnota neexistuje, tuto hodnotu budou ignorovány a další nebyl zpracován.  
  
### <a name="container-expression-context-scoping"></a>Zkoumání kontextu výrazu kontejneru  
  
Kontejner výrazu může být kontejner rozsahem nebo rozsahem dokumentu:  
  
-   Výraz je kontejner s rozsahem, pokud podkladový zdroj výraz kontejneru je buď ROOT nebo `container_name`. Takový výraz představuje sadu dokumentů načíst přímo z kontejneru a není závislá na zpracování jiných výrazech kontejneru.  
  
-   Výraz je dokument s rozsahem, pokud je podkladový zdroj výrazu kontejneru `input_alias` zavedené dříve v dotazu. Takový výraz představuje sadu dokumentů získanou vyhodnocením výrazu kontejneru v rámci jednotlivých dokumentů, které patří do sady spojenými s daným kontejnerem alias.  Výsledná sada bude sjednocení sad získanou vyhodnocením výrazu kontejner pro všechny dokumenty v základní sadě. 

## <a name="examples"></a>Příklady

### <a name="get-subitems-by-using-the-from-clause"></a>Získat podpoložky pomocí klauzule FROM

Klauzule FROM může snížit zdroj na menší podmnožinu. Chcete-li vytvořit výčet pouze podstrom v každé položce, může se stát, že se dílčí kořen stane zdrojem, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou následující:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Předchozí dotaz použil pole jako zdroj, ale můžete také použít objekt jako zdroj. Dotaz považuje při zahrnutí do výsledku platnou definovanou hodnotu JSON ve zdroji. Následující příklad vyloučí `Families`, které nemají hodnotu `address.state`.

```sql
    SELECT *
    FROM Families.address.state
```

Výsledky jsou následující:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule WHERE](sql-query-where.md)