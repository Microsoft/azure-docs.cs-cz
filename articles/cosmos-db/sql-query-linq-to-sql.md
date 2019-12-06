---
title: Překlad LINQ to SQL v Azure Cosmos DB
description: Seznamte se s podporovanými operátory LINQ a způsobem, jak jsou dotazy LINQ mapovány na dotazy SQL v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d8dd6392cf22852a10c1dc2600edcbc647f3c510
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871155"
---
# <a name="linq-to-sql-translation"></a>Technologie LINQ to SQL překladu

Zprostředkovatel dotazů Azure Cosmos DB provádí nejlepší mapování úsilí z dotazu LINQ na dotaz typu Cosmos DB SQL. Následující popis předpokládá základní znalost pomocí LINQ.

Typ zprostředkovatele dotazu systém podporuje pouze primitivní typy JSON: číselná, logická hodnota, řetězec a hodnota null.

Zprostředkovatel dotazů podporuje následující skalární výrazy:

- Konstantní hodnoty, včetně konstantních hodnot primitivních datových typů v době vyhodnocení dotazu.
  
- Výrazy indexů vlastností nebo pole, které odkazují na vlastnost objektu nebo prvku pole. Například:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetické výrazy, včetně běžných aritmetických výrazů pro numerické a logické hodnoty. Úplný seznam najdete v tématu [Azure Cosmos DB specifikace SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Výrazy porovnání řetězců, které zahrnují porovnání řetězcové hodnoty s určitou konstantní řetězcovou hodnotou.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Výrazy vytvoření objektu nebo pole, které vracejí objekt složeného typu hodnoty nebo anonymního typu, nebo pole takových objektů. Tyto hodnoty můžete vnořit.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Podporované operátory LINQ

Zprostředkovatel LINQ zahrnutý v sadě SQL .NET SDK podporuje následující operátory:

- **Vyberte**: projekce se přeloží na výběr SQL, včetně konstrukce objektu.
- **Kde**: filtry se převádějí do SQL, kde a podporují překlad mezi `&&`, `||`a `!` operátory SQL
- **Operátor SelectMany**: umožňuje uvolnění polí do klauzule SQL JOIN. Použijte k řetězení nebo vnořování výrazů k filtrování prvků pole.
- **OrderBy** a **OrderByDescending**: PŘELOŽÍ na pořadí pomocí ASC nebo DESC.
- **Počet**, **součet**, **Min**, **maximální**, a **průměrné** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
- **Metoda CompareTo**: se přeloží na porovnání rozsahu. Běžně se používá pro řetězce, protože nejsou srovnatelné v rozhraní .NET.
- **Proveďte**: přeloží se na horní část SQL pro omezení výsledků dotazu.
- **Matematické funkce**: podporuje překlad z `Abs`.net, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`a `Truncate` k ekvivalentním integrovaným funkcím SQL.
- **Řetězcové funkce**: podporuje překlad z `Concat`.net, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`a `TrimStart` do ekvivalentních integrovaných funkcí SQL.
- **Funkce pole**: podporuje překlad z rozhraní .NET `Concat`, `Contains`a `Count` na ekvivalentní integrované funkce SQL.
- **Funkce geoprostorového rozšíření**: podporuje převod z metod zástupných procedur `Distance`, `IsValid`, `IsValidDetailed`a `Within` na ekvivalentní integrované funkce SQL.
- **Uživatelsky definovaná funkce rozšíření**: podporuje převod z metody zástupné procedury `UserDefinedFunctionProvider.Invoke` na odpovídající uživatelsky definovanou funkci.
- **Různé**: podporuje překlad `Coalesce` a podmíněných operátorů. Může převést `Contains` do řetězce obsahuje, ARRAY_CONTAINS nebo SQL v, v závislosti na kontextu.

## <a name="examples"></a>Příklady

Následující příklady ilustrují, jak se některé standardní operátory dotazů LINQ převádějí na Cosmos DB dotazy.

### <a name="select-operator"></a>Vybrat operátor

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

**Operátor WHERE, příklad 1:**

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
  
**Operátor WHERE, příklad 2:**

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

Můžete sestavovat předchozí operátory a vytvořit tak výkonnější dotazy. Vzhledem k tomu, že Cosmos DB podporuje vnořené kontejnery, můžete kompozici zřetězit nebo vnořovat.

### <a name="concatenation"></a>Zřetězení

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězený dotaz může začít s volitelným dotazem `SelectMany` následovaný více `Select` nebo `Where` operátory.

**Zřetězení, příklad 1:**

- **Lambda výraz LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
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

Syntaxe je `input.SelectMany(x=>x.Q())`, kde `Q` je operátor `Select`, `SelectMany`nebo `Where`.

Vnořený dotaz aplikuje vnitřní dotaz na každý element vnějšího kontejneru. Jednou z důležitých funkcí je, že vnitřní dotaz může odkazovat na pole prvků ve vnějším kontejneru, jako je například připojovat se k sobě.

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

**Vnořování, příklad 3:**

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


## <a name="next-steps"></a>Další kroky

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Data modelu dokumentu](modeling-data.md)
