---
title: Překlad LINQ to SQL v Azure Cosmos DB
description: Mapování dotazů LINQ na Azure Cosmos DB dotazů SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ad6dcf7307955300a781a7a649b6ac76b3c69589
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003561"
---
# <a name="linq-to-sql-translation"></a>Technologie LINQ to SQL překladu

Zprostředkovatel dotazů Azure Cosmos DB provádí nejlepší mapování úsilí z dotazu LINQ na dotaz typu Cosmos DB SQL. Následující popis předpokládá základní znalost pomocí LINQ.

Typ zprostředkovatele dotazu systém podporuje pouze primitivní typy JSON: číselná, logická hodnota, řetězec a hodnota null.

Zprostředkovatel dotazů podporuje následující skalární výrazy:

- Konstantní hodnoty, včetně konstantních hodnot primitivních datových typů v době vyhodnocení dotazu.
  
- Výrazy indexů vlastností nebo pole, které odkazují na vlastnost objektu nebo prvku pole. Příklad:
  
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

- **Vyberte**: Projekce se převádějí na výběr SQL, včetně konstrukce objektu.
- **Kde**: Filtry se převádějí do SQL, kde a podporují `&&`překlad `||`mezi, `!` a operátory SQL.
- **Operátor SelectMany**: Umožňuje převinutí polí do klauzule SQL JOIN. Použijte k řetězení nebo vnořování výrazů k filtrování prvků pole.
- **OrderBy** a **OrderByDescending**: Přeloží na pořadí pomocí ASC nebo DESC.
- **Počet**, **součet**, **Min**, **maximální**, a **průměrné** operátory pro agregaci a jejich ekvivalenty asynchronní **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, a **AverageAsync**.
- **CompareTo**: Přeloží porovnávání rozsahů. Běžně se používá pro řetězce, protože nejsou srovnatelné v rozhraní .NET.
- **Vezměte v úvahu**: Přeloží na horní část SQL pro omezení výsledků dotazu.
- **Matematické funkce**: Podporuje překlad z rozhraní `Abs`.NET `Acos`, `Asin` `Atan` `Ceiling` ,`Floor`, ,,`Log`,,, ,`Log10`, ,`Pow` `Cos` `Exp` `Round`, `Sign`, ,,`Sin`a kekvivalentnímintegrovaným`Truncate`funkcímSQL. `Sqrt` `Tan`
- **Řetězcové funkce**: Podporuje překlad z rozhraní `Concat`.NET `Contains`, `Count` `EndsWith``IndexOf` ,`StartsWith`, ,,`SubString`,,, ,`ToLower`, ,`ToUpper` `Replace` `Reverse` `TrimEnd` a`TrimStart` ekvivalentními integrovanými funkcemi SQL.
- **Funkce pole**: Podporuje překlad z rozhraní `Concat`.NET `Contains`, a `Count` na ekvivalentní integrované funkce SQL.
- **Funkce geoprostorového rozšíření**: Podporuje překlad ze zástupných `IsValid`metod `IsValidDetailed` `Distance`,, `Within` a na ekvivalentní integrované funkce SQL.
- **Funkce rozšíření uživatelsky definované funkce**: Podporuje převod z metody `UserDefinedFunctionProvider.Invoke` zástupné procedury na odpovídající uživatelsky definovanou funkci.
- **Různé**: Podporuje překlad `Coalesce` a podmíněných operátorů. Lze překládat `Contains` na řetězec obsahuje, ARRAY_CONTAINS nebo SQL v, v závislosti na kontextu.

## <a name="examples"></a>Příklady

Následující příklady ilustrují, jak se některé standardní operátory dotazů LINQ převádějí na Cosmos DB dotazy.

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

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězený dotaz může začít s volitelným `SelectMany` dotazem následovaným více `Select` operátory OR `Where` .

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

Syntaxe `input.SelectMany(x=>x.Q())` je tam `Select`, `Q` kde je operátor `SelectMany`, nebo `Where` .

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
