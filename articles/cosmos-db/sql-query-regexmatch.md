---
title: RegexMatch v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o funkci RegexMatch SQL System v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004ece7877358be54ba67c2f72eb3210f16fb0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098123"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Poskytuje možnosti regulárních výrazů. Regulární výrazy jsou stručným a flexibilním zápisem pro hledání vzorů textu. Azure Cosmos DB používá [standardní výrazy kompatibilní s Perl (PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Syntaxe
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je regulární výraz.

*str_expr3*  
   Je řetězec vybraných modifikátorů pro použití s regulárním výrazem. Tato řetězcová hodnota je nepovinná. Chcete-li spustit RegexMatch bez modifikátorů, můžete buď přidat prázdný řetězec, nebo jej vynechat zcela. 

Můžete se dozvědět o [syntaxi pro vytváření regulárních výrazů v jazyce Perl](https://perldoc.perl.org/perlre). 

Azure Cosmos DB podporuje následující čtyři modifikátory:

| Modifikátor | Description |
| ------ | ----------- |
| `m` | Zacházejte řetězcovým výrazem, který má být prohledán jako více řádků. Bez této možnosti se bude shodovat "^" a "$" na začátku nebo na konci řetězce, a ne na každém jednotlivém řádku. |
| `s` | Povoluje, aby znak "." odpovídal jakémukoli znaku, včetně znaku nového řádku. | 
| `i` | Ignorovat velikost písmen při porovnávání vzorů |
| `x` | Ignoruje všechny prázdné znaky. |

## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz. Vrátí undefined, pokud má být prohledáván řetězcový výraz, regulární výraz nebo vybrané Modifikátory nejsou platné.
  
## <a name="examples"></a>Příklady
  
Následující jednoduchý RegexMatch příklad zkontroluje řetězec "abcd" pro porovnávání regulárního výrazu pomocí několika různých modifikátorů.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Zde je sada výsledků.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Pomocí RegexMatch můžete použít metaznaky k provádění složitějších hledání řetězců, které by jinak nebylo možné provádět pomocí systémových funkcí StartsWith, EndsWith, Contains nebo StringEquals. Tady jsou některé další příklady, které můžete spustit pomocí Testovací prostředí dotazů sady výživy, která je dostupná prostřednictvím [Azure Cosmos DB](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Pokud potřebujete použít metaznak v regulárním výrazu a nechcete, aby měl mít zvláštní význam, měli byste řídicí metaznak pomocí použít `\` .

**Zkontroluje položky s popisem, který obsahuje slovo "sůl" přesně jednou:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Zkontroluje položky s popisem, který obsahuje číslo mezi 0 a 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Zkontroluje položky s popisem, který obsahuje čtyři slova, počínaje písmenem S nebo s:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude těžit z [indexu rozsahu](index-policy.md#includeexclude-strategy) , pokud je možné regulární výraz rozdělit do funkce StartsWith, EndsWith, Contains nebo StringEquals System Functions.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
