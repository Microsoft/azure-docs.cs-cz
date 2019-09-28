---
title: StringToNumber v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToNumber funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349224"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Vrátí výraz přeložený na číslo. Pokud výraz nelze přeložit, vrátí nedefinované funkce.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Je řetězcový výraz, který má být analyzován jako výraz čísla JSON. Čísla ve formátu JSON musí být celé číslo nebo plovoucí desetinná čárka. Podrobnosti o formátu JSON najdete v tématu [JSON.org](https://json.org/) .  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz nebo nedefinovaný.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak `StringToNumber` se chová v různých typech. 

Prázdný znak je povolen pouze před nebo za číslem.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Tady je sada výsledků.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Ve formátu JSON musí být platné číslo buď celé číslo, nebo číslo s plovoucí desetinnou čárkou.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Tady je sada výsledků.  
  
```json
{{}}
```  

Předaný výraz bude analyzován jako číselný výraz; Tyto vstupy se nevyhodnotí na typ Number, takže se vrátí nedefinované. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Tady je sada výsledků.  
  
```json
{{}}
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
