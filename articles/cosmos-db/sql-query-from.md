---
title: FROM – klauzule ve službě Azure Cosmos DB
description: Další informace o SQL z klauzule pro službu Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342637"
---
# <a name="from-clause"></a>FROM – klauzule

OD (`FROM <from_specification>`) klauzule je volitelné, pokud je zdroj filtrovaná nebo předpokládané později v dotazu. Dotaz, jako jsou `SELECT * FROM Families` vytvoří výčet na celém `Families` kontejneru. Speciální identifikátor KOŘENOVÉ můžete použít také pro kontejner místo názvu kontejneru.

Klauzule FROM vynucuje každý dotaz následující pravidla:

* Kontejner může být například vytvořen alias, `SELECT f.id FROM Families AS f` nebo jednoduše `SELECT f.id FROM Families f`. Tady `f` je alias pro `Families`. JE volitelný – klíčové slovo do [alias](sql-query-aliasing.md) identifikátor.  

* Jednou alias nemůže být vázán na původní název zdroje. Například `SELECT Families.id FROM Families f` je syntakticky neplatný protože identifikátor `Families` byl vytvořen alias a nelze ho přeložit zobrazovat.  

* Všechny odkazované vlastnosti musí být plně kvalifikovaný, aby se zabránilo všechny nejednoznačný vazby, které chybí splňuje striktní schéma. Například `SELECT id FROM Families f` je syntakticky neplatný protože vlastnost `id` není vázán.

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

### <a name="get-subitems-by-using-the-from-clause"></a>Získat podřízené položky pomocí klauzule FROM

Klauzule FROM může snížit zdroji na menší podmnožinu. Pokud chcete získat výčet jenom podstrom v každé položce, můžete subroot stane zdroj, jak je znázorněno v následujícím příkladu:

```sql
    SELECT *
    FROM Families.children
```

Výsledky jsou:

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

Předchozí dotaz používá pole jako zdroj, ale můžete také použít objekt jako zdroj. Dotaz bude považovat za libovolnou hodnotu JSON platný, definovaný ve zdroji pro zařazení ve výsledku. V následujícím příkladu by vyloučit `Families` nemají `address.state` hodnotu.

```sql
    SELECT *
    FROM Families.address.state
```

Výsledky jsou:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule WHERE](sql-query-where.md)