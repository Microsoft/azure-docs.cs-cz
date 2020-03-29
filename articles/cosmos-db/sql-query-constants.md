---
title: Konstanty SQL v Db Azure Cosmos
description: Informace o tom, jak se konstanty dotazů SQL v Azure Cosmos DB používají k reprezentaci konkrétní hodnoty dat
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873416"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Konstanty dotazu SQL azure cosmos DB  

 Konstanta, označovaná také jako literál nebo skalární hodnota, je symbol, který představuje určitou hodnotu dat. Formát konstanty závisí na datovém typu hodnoty, kterou představuje.  
  
 **Podporované skalární datové typy:**  
  
|**Typ**|**Pořadí hodnot**|  
|-|-|  
|**Nedefinované**|Jedna hodnota: **nedefinovaná**|  
|**Null**|Jedna hodnota: **null**|  
|**Logická hodnota**|Hodnoty: **false**, **true**.|  
|**Číslo**|Dvojité přesné číslo s plovoucí desetinnou desetinnou hlavou, standard IEEE 754.|  
|**Řetězec**|Posloupnost nula nebo více znaků Unicode. Řetězce musí být uzavřeny v jednoduché nebo dvojité uvozovky.|  
|**Pole**|Posloupnost nula nebo více prvků. Každý prvek může být hodnotou libovolného skalárního datového typu, s výjimkou **undefined**.|  
|**Objekt**|Neuspořádaná sada nulových nebo více párů název/hodnota. Název je řetězec Unicode, hodnota může být libovolného skalárního datového typu, s výjimkou **Undefined**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntaxe
  
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
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argumenty
  
* `<undefined_constant>; Undefined`  
  
  Představuje nedefinovanou hodnotu typu Undefined.  
  
* `<null_constant>; null`  
  
  Představuje **hodnotu null** typu **Null**.  
  
* `<boolean_constant>`  
  
  Představuje konstantu typu Logická hodnota.  
  
* `false`  
  
  Představuje **false** hodnotu typu Logická hodnota.  
  
* `true`  
  
  Představuje **skutečnou** hodnotu typu Logická hodnota.  
  
* `<number_constant>`  
  
  Představuje konstantu.  
  
* `decimal_literal`  
  
  Desetinné literály jsou čísla reprezentovaná buď desítkovým zápisem, nebo vědeckýzápis.  
  
* `hexadecimal_literal`  
  
  Šestnáctkové literály jsou čísla reprezentovaná pomocí předpony 0x následovaná jednou nebo více šestnáctkovými číslicemi.  
  
* `<string_constant>`  
  
  Představuje konstantu typu String.  
  
* `string _literal`  
  
  Řetězcové literály jsou řetězce Unicode reprezentované posloupností nula nebo více znaků Unicode nebo řídicích sekvencí. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách (apostrof: ' ) nebo uvozovkách (uvozovky: ").  
  
  Následující únikové sekvence jsou povoleny:  
  
|**Úniková sekvence**|**Popis**|**znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|uvozovky (")|U+0022|  
|\\\ |reverzní solidus\\( )|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|Backspace|U+0008|  
|\f|informační kanál formuláře|U +000C|  
|\n|posuv řádků|U+000A|  
|\r|návrat na začátek řádku|U+000D|  
|\t|Kartě|U+0009|  
|\uXXXX|Znak Unicode definovaný 4 šestnáctkovými číslicemi.|U+XXXX|  

## <a name="next-steps"></a>Další kroky

- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
