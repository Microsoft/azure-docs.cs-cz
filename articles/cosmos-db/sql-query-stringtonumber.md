---
title: StringToNumber v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o StringToNumber funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4fefc59a6f95382d093f5501a0023dc2ef50025b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088246"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Následující příklad ukazuje, jak se `StringToNumber` chová v různých typech. 

Prázdný znak je povolen pouze před nebo za číslem.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Zde je sada výsledků.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Ve formátu JSON musí být platné číslo buď celé číslo, nebo číslo s plovoucí desetinnou čárkou.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Zde je sada výsledků.  
  
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
  
 Zde je sada výsledků.  
  
```json
{{}}
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
