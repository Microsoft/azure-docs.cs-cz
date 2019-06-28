---
title: Konstanty SQL ve službě Azure Cosmos DB
description: Další informace o konstanty SQL ve službě Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342432"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL dotazu konstanty  

 Konstanta, označované také jako literál nebo skalární hodnota, je symbol, který reprezentuje hodnotu konkrétního data. Formát konstantě závisí na datový typ, který představuje.  
  
 **Podporované skalárních datových typů:**  
  
|**Typ**|**Hodnoty pořadí**|  
|-|-|  
|**Nedefinovaný**|Jedna hodnota: **nedefinované**|  
|**Hodnotu Null**|Jedna hodnota: **null**|  
|**Datový typ Boolean**|Hodnoty: **false**, **true**.|  
|**Číslo**|Číslo dvojité přesnosti s plovoucí desetinnou čárkou, standardní IEEE 754.|  
|**řetězec**|Posloupnost nula nebo více znaků Unicode. Řetězce musí být uzavřen v jednoduchých nebo dvojitých uvozovkách.|  
|**Pole**|Posloupnost nula nebo více prvků. Každý prvek může být hodnota libovolného skalární datového typu, s výjimkou **Nedefinováno**.|  
|**objekt**|Neuspořádanou sadu párů název/hodnota nula nebo více. Název je řetězec znaků Unicode, hodnota může být libovolného skalární datového typu, s výjimkou **Nedefinováno**.|  
  
## <a name="bk_syntax"></a>Syntaxe
  
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
  
##  <a name="bk_arguments"></a> argumenty
  
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
|\\\ |zpětné lomítko (\\)|U+005C|  
|\\/|lomítko (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|Posun strany|U + 000C|  
|\n|Přechod na nový řádek|U + 000A|  
|\r|Návrat na začátek|U + 000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Znak Unicode definované 4 šestnáctkovými číslicemi.|U+XXXX|  

## <a name="next-steps"></a>Další postup

- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelování dat dokumentů](modeling-data.md)
