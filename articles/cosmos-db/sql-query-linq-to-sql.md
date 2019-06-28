---
title: Technologie LINQ to SQL překlad ve službě Azure Cosmos DB
description: Mapování LINQ dotazy na dotazy SQL služby Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342805"
---
# <a name="linq-to-sql-translation"></a>Technologie LINQ to SQL překladu

Poskytovatele služby Azure Cosmos DB dotazů provede nejlepší úsilí mapování z dotazu LINQ do dotazu Cosmos DB SQL. Následující popis předpokládá základní znalost LINQ.

Systém typů poskytovatele dotazů podporuje pouze primitivní typy JSON: číselná nebo logická hodnota, řetězec a s hodnotou null.

Poskytovatele dotazů podporuje následující skalární výrazy:

- Konstantní hodnoty, včetně konstantní hodnoty primitivních datových typů v době vyhodnocení dotazu.
  
- Vlastnost nebo pole indexové výrazy, které odkazují na vlastnosti objektu nebo k elementu pole. Příklad:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetických výrazů, včetně aritmetických výrazů běžné u číselných a logických hodnot. Úplný seznam najdete v článku [SQL služby Azure Cosmos DB specifikace](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Výrazy porovnání řetězce, mezi které patří porovnání řetězcovou hodnotu na hodnotu konstanty typu řetězec.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objekt nebo pole vytváření výrazů, které vracejí objekt složené hodnotový typ nebo anonymního typu nebo polem tyto objekty. Tyto hodnoty můžete vnořit.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Podporované operátory LINQ

Poskytovatel LINQ součástí SQL SDK pro .NET podporuje následující operátory:

- **Vyberte**: Projekce přeložit a vyberte SQL, včetně konstrukce objektu.
- **Kde**: Filtry přeloží do WHERE příkazu SQL a podporovat překlad mezi `&&`, `||`, a `!` SQL operátorů
- **Operátor SelectMany**: Umožňuje uvolnění polí do klauzule SQL JOIN. Pomocí řetězení nebo vnořené výrazy k filtrování prvků pole.
- **Řadit podle** a **OrderByDescending**: Převede uzel do ORDER BY s ASC nebo DESC.
- **Počet**, **součet**, **Min**, **maximální**, a **průměrné** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
- **CompareTo**: Přeloží na porovnání rozsahu. Běžně používá pro řetězce, protože nejsou porovnatelné v rozhraní .NET.
- **Využijte**: Přeloží nahoru SQL pro omezení výsledků z dotazu.
- **Matematické funkce**: Podporuje překlad z .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, a `Truncate` na ekvivalentní funkce integrované v SQL.
- **Řetězec funkce**: Podporuje překlad z .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, a `TrimStart` na ekvivalentní funkce integrované v SQL.
- **Array – funkce**: Podporuje překlad z .NET `Concat`, `Contains`, a `Count` na ekvivalentní funkce integrované v SQL.
- **Rozšíření geoprostorové funkce**: Podporuje překlad z metody zástupných procedur `Distance`, `IsValid`, `IsValidDetailed`, a `Within` na ekvivalentní funkce integrované v SQL.
- **Uživatelem definované funkce rozšíření funkce**: Podporuje překlad z zástupná metoda `UserDefinedFunctionProvider.Invoke` odpovídající uživatelem definované funkce.
- **Různé**: Podporuje překlad `Coalesce` a podmíněných operátorů. Jsou dobře převeditelné `Contains` řetězec obsahuje, ARRAY_CONTAINS nebo v SQL, v závislosti na kontextu.

## <a name="examples"></a>Příklady

Následující příklady znázorňují, jak některé standardní operátory dotazu LINQ přeložit do služby Cosmos DB dotazů.

### <a name="select-operator"></a>Vyberte operátor

Syntaxe je `input.Select(x => f(x))`, kde `f` je skalární výraz.

**Vyberte operátor, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Vyberte operátor, příklad 2:** 

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Vyberte operátor, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operátor SelectMany – operátor

Syntaxe je `input.SelectMany(x => f(x))`, kde `f` je skalární výraz, který vrátí typ kontejneru.

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Kde – operátor

Syntaxe je `input.Where(x => f(x))`, kde `f` je skalární výraz, který vrací logickou hodnotu.

**Pokud operátor, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Pokud operátor, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Složený dotazy SQL

Můžete vytvořit předchozí operátorům tvoří výkonnějších dotazů. Protože Cosmos DB podporuje vnořené kontejnery, lze zřetězit nebo vnořené složení.

### <a name="concatenation"></a>Zřetězení

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězených dotazů můžete začít s volitelným `SelectMany` dotazu, za nímž následuje více `Select` nebo `Where` operátory.

**Zřetězení, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Zřetězení, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Zřetězení, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Zřetězení, příklad 4:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Vnoření

Syntaxe je `input.SelectMany(x=>x.Q())` kde `Q` je `Select`, `SelectMany`, nebo `Where` operátor.

Vnořený dotaz použije vnitřní dotaz na každý prvek vnější kontejneru. Jednu důležitou funkci je, že vnitřní dotaz mohou odkazovat na pole prvků v vnější kontejneru, jako jsou spojení sama.

**Vnoření, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Vnoření, příklad 2:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Vnoření, příklad 3:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelování dat dokumentů](modeling-data.md)
