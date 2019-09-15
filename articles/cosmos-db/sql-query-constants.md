---
title: Konstanty SQL v Azure Cosmos DB
description: Přečtěte si o konstantách SQL v Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: 41cafeb76b7b9481a78ffcff179676d0aa7bf6fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002046"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB konstanty dotazů SQL  

 Konstanta, označované také jako literál nebo skalární hodnota, je symbol, který reprezentuje hodnotu konkrétního data. Formát konstantě závisí na datový typ, který představuje.  
  
 **Podporované skalárních datových typů:**  
  
|**Typ**|**Hodnoty pořadí**|  
|-|-|  
|**Nedefinovaný**|Jedna hodnota: **nedefinované**|  
|**Hodnotu Null**|Jedna hodnota: **null**|  
|**Datový typ Boolean**|Hodnoty: **false**, **true**.|  
|**Číslo**|Číslo dvojité přesnosti s plovoucí desetinnou čárkou, standardní IEEE 754.|  
|**řetězec**|Posloupnost nula nebo více znaků Unicode. Řetězce musí být uzavřen v jednoduchých nebo dvojitých uvozovkách.|  
|**Pole**|Posloupnost nula nebo více prvků. Každý prvek může být hodnota jakéhokoli skalárního datového typu, s výjimkou **nedefinovaného**.|  
|**objekt**|Neuspořádanou sadu párů název/hodnota nula nebo více. Název je řetězec znaků Unicode, hodnota může být libovolného skalární datového typu, s výjimkou **Nedefinováno**.|  
  
## <a name="bk_syntax"></a>Syntaktick
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a>Náhodné
  
* `<undefined_constant>; Undefined`  
  
  Představuje nedefinované hodnoty typu Nedefinováno.  
  
* `<null_constant>; null`  
  
  Představuje **null** hodnotu typu **Null**.  
  
* `<boolean_constant>`  
  
  Představuje konstantu typu Boolean.  
  
* `false`  
  
  Představuje **false** hodnotu typu Boolean.  
  
* `true`  
  
  Představuje **true** hodnotu typu Boolean.  
  
* `<number_constant>`  
  
  Představuje konstantu.  
  
* `decimal_literal`  
  
  Desítkové literály jsou čísla vyjadřuje desítkovém zápisu nebo vědecký zápis.  
  
* `hexadecimal_literal`  
  
  Šestnáctkové literály jsou čísla, které jsou reprezentovány pomocí předpony "0 x" a jedné nebo několika šestnáctkovými číslicemi.  
  
* `<string_constant>`  
  
  Představuje konstantu typu String.  
  
* `string _literal`  
  
  Řetězcové literály jsou reprezentovány posloupnost nula nebo více znaků Unicode nebo řídicí sekvence řetězců v kódu Unicode. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách (apostrof: ") nebo dvojité uvozovky (uvozovky:").  
  
  Následující řídicí sekvence jsou povoleny:  
  
|**Řídicí sekvence**|**Popis**|**Znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|dvojité uvozovky (")|U+0022|  
|\\\ |obrácené lomítko (\\)|U+005C|  
|\\/|lomítko (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|Posun strany|U + 000C|  
|\n|Přechod na nový řádek|U + 000A|  
|\r|Návrat na začátek|U + 000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Znak Unicode definované 4 šestnáctkovými číslicemi.|U+XXXX|  

## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Data modelu dokumentu](modeling-data.md)
