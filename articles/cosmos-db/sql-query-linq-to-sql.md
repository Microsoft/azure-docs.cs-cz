---
title: Překlad LINQ do SQL v Azure Cosmos DB
description: Seznamte se s podporovanými operátory LINQ a s tím, jak jsou dotazy LINQ mapovány na dotazy SQL v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441252"
---
# <a name="linq-to-sql-translation"></a>LinQ do PŘEKLADU SQL

Poskytovatel dotazu Azure Cosmos DB provádí mapování nejlepší úsilí z dotazu LINQ do dotazu Cosmos DB SQL. Následující popis předpokládá základní znalost LINQ.

Systém typu poskytovatele dotazu podporuje pouze primitivní typy JSON: číselné, logické, řetězcové a null.

Zprostředkovatel dotazu podporuje následující skalární výrazy:

- Konstantní hodnoty, včetně konstantních hodnot primitivních datových typů v době vyhodnocení dotazu.
  
- Výrazy indexu vlastnosti/pole, které odkazují na vlastnost objektu nebo prvku pole. Například:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetické výrazy, včetně běžných aritmetické výrazy na číselné a logické hodnoty. Úplný seznam najdete ve [specifikaci Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Výrazy porovnání řetězců, které zahrnují porovnání hodnoty řetězce s nějakou hodnotou konstantního řetězce.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Výrazy pro vytváření objektů a polí, které vracejí objekt složeného typu hodnoty nebo anonymního typu nebo pole těchto objektů. Tyto hodnoty můžete vnořit.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Podporované operátory LINQ

Zprostředkovatel LINQ, který je součástí sady SQL .NET SDK, podporuje následující operátory:

- **Vyberte**: Projekce se překládají na SQL SELECT, včetně konstrukce objektu.
- **Kde**: Filtry přeložit do SQL `&&` `||`KDE `!` a podporovat překlad mezi , , a sql operátory
- **SelectMany**: Umožňuje odvíjení polí klauzule SQL JOIN. Slouží k řetězení nebo vnoření výrazů pro filtrování na prvky pole.
- **OrderBy** a **OrderByDescending**: Přeložit do pořadí podle s ASC nebo DESC.
- **Operátory Count**, **Sum**, **Min**, **Max**a **Average** pro agregaci a jejich asynchronní ekvivalenty **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**a **AverageAsync**.
- **CompareTo**: Překládá na porovnání rozsahu. Běžně se používá pro řetězce, protože nejsou srovnatelné v rozhraní .NET.
- **Přeskočit** a **přijmout:** Překládá na SQL OFFSET a LIMIT pro omezení výsledků z dotazu a dělat stránkování.
- **Matematické funkce**: Podporuje `Abs`překlad `Acos` `Asin`z `Atan` `Ceiling`rozhraní `Cos` `Exp`.NET , , , , , `Floor`, `Log` `Log10`, `Pow` `Round`, `Sign` `Sin`, `Sqrt`, , `Tan`, a `Truncate` do ekvivalentních vestavěných funkcí SQL.
- **Funkce řetězce**: Podporuje `Concat`překlad `Contains` `Count`z `EndsWith``IndexOf`rozhraní `Replace` `Reverse`.NET , , , , `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, a `TrimStart` do ekvivalentních vestavěných funkcí SQL.
- **Funkce pole**: Podporuje `Concat`překlad `Contains`z `Count` rozhraní .NET a ekvivalentních vestavěných funkcí SQL.
- **Funkce geoprostorového rozšíření**: Podporuje `Distance` `IsValid`překlad `IsValidDetailed`z `Within` metod se zakázaným inzerováním , , a na ekvivalentní vestavěné funkce SQL.
- **Uživatelem definovaná funkce Rozšíření**funkce : `UserDefinedFunctionProvider.Invoke` Podporuje překlad z metody se zakázaným inzerováním do odpovídající uživatelem definované funkce.
- Různé : Podporuje překlad a podmíněné `Coalesce` **operátory.** V `Contains` závislosti na kontextu lze přeložit na řetězec OBSAHUJE, ARRAY_CONTAINS nebo SQL IN.

## <a name="examples"></a>Příklady

Následující příklady ilustrují, jak se některé standardní linq dotazy operátory přeložit do dotazů Cosmos DB.

### <a name="select-operator"></a>Vybrat operátor

Syntaxe `input.Select(x => f(x))`je `f` , kde je skalární výraz.

**Operátor Select, příklad 1:**

- **Linq lambda výraz**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Operátor Select, příklad 2:** 

- **Linq lambda výraz**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Operátor Select, příklad 3:**

- **Linq lambda výraz**
  
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

### <a name="selectmany-operator"></a>Operátor SelectMany

Syntaxe `input.SelectMany(x => f(x))`je `f` , kde je skalární výraz, který vrací typ kontejneru.

- **Linq lambda výraz**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>V případě, že obsluha

Syntaxe `input.Where(x => f(x))`je `f` , kde je skalární výraz, který vrací logickou hodnotu.

**Kde operátor, příklad 1:**

- **Linq lambda výraz**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Kde operátor, příklad 2:**

- **Linq lambda výraz**
  
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

## <a name="composite-sql-queries"></a>Složené dotazy SQL

Můžete vytvořit předchozí operátory tvořit výkonnější dotazy. Vzhledem k tomu, že Cosmos DB podporuje vnořené kontejnery, můžete zřetězit nebo vnořit kompozici.

### <a name="concatenation"></a>Zřetězení

Syntaxe je `input(.|.SelectMany())(.Select()|.Where())*`. Zřetězený dotaz může začínat volitelným `SelectMany` `Select` dotazem následovaným více nebo `Where` operátory.

**Zřetězení, příklad 1:**

- **Linq lambda výraz**
  
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

- **Linq lambda výraz**
  
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

- **Linq lambda výraz**
  
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

- **Linq lambda výraz**
  
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

Syntaxe `input.SelectMany(x=>x.Q())` je `Q` kde `Select` `SelectMany`je `Where` , , nebo operátor.

Vnořený dotaz aplikuje vnitřní dotaz na každý prvek vnějšího kontejneru. Jedním z důležitých funkcí je, že vnitřní dotaz může odkazovat na pole prvků ve vnějším kontejneru, jako je vlastní spojení.

**Vnoření, příklad 1:**

- **Linq lambda výraz**
  
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

- **Linq lambda výraz**
  
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

- **Linq lambda výraz**
  
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

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
