---
title: Konstanty SQL v Azure Cosmos DB
description: Přečtěte si, jak se konstanty dotazů SQL v Azure Cosmos DB používají k vyjádření konkrétní hodnoty dat.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ebc1f66e68d4cf37546e7d33a9b723385330395c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100860"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB konstanty dotazů SQL  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Konstanta, která se označuje také jako literál nebo skalární hodnota, je symbol, který představuje konkrétní datovou hodnotu. Formát konstanty závisí na datovém typu hodnoty, kterou představuje.  
  
 **Podporované skalární datové typy:**  
  
|**Typ**|**Pořadí hodnot**|  
|-|-|  
|**Nedefinované**|Jedna hodnota: **Nedefinováno**|  
|**Null**|Jedna hodnota: **null**|  
|**Datového**|Hodnoty: **false** , **true** .|  
|**Číselná**|Číslo s plovoucí desetinnou čárkou a dvojitou přesností, Standard IEEE 754.|  
|**Řetězec**|Sekvence nula nebo více znaků Unicode. Řetězce musí být uzavřeny v jednoduchých nebo dvojitých uvozovkách.|  
|**Skupin**|Sekvence nula nebo více prvků. Každý prvek může být hodnota jakéhokoli skalárního datového typu, s výjimkou **nedefinovaného** .|  
|**Předmětů**|Neuspořádaná sada nula nebo více párů název/hodnota. Název je řetězec Unicode, hodnota může být jakýkoli skalární datový typ, s výjimkou **nedefinovaného** .|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
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
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Náhodné
  
* `<undefined_constant>; Undefined`  
  
  Představuje nedefinovanou hodnotu typu undefined.  
  
* `<null_constant>; null`  
  
  Reprezentuje hodnotu **null** typu s hodnotou **null** .  
  
* `<boolean_constant>`  
  
  Představuje konstantu typu Boolean.  
  
* `false`  
  
  Představuje **falešnou** hodnotu typu Boolean.  
  
* `true`  
  
  Představuje hodnotu **true** typu Boolean.  
  
* `<number_constant>`  
  
  Představuje konstantu.  
  
* `decimal_literal`  
  
  Desítkové literály jsou čísla reprezentovaná buď pomocí desítkového zápisu, nebo pomocí vědeckého zápisu.  
  
* `hexadecimal_literal`  
  
  Šestnáctkové literály jsou čísla reprezentovaná pomocí prefixu "0x" následovaný jednou nebo více šestnáctkovými číslicemi.  
  
* `<string_constant>`  
  
  Představuje konstantu typu String.  
  
* `string _literal`  
  
  Řetězcové literály jsou řetězce Unicode reprezentované sekvencí nula nebo více znaků Unicode nebo řídicí sekvence. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách (apostrof: ') nebo dvojité uvozovky (uvozovky: ").  
  
  Jsou povoleny následující řídicí sekvence:  
  
|**Řídicí sekvence**|**Popis**|**znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U + 0027|  
|\\"|uvozovky (")|U + 0022|  
|\\\ |obrácené lomítko ( \\ )|U + 005C|  
|\\/|lomítko (/)|U + 002F|  
|\b|BACKSPACE|U + 0008|  
|\f|informační kanál formuláře|U + 000C|  
|\n|čárový kanál|U + 000A|  
|\r|návrat na začátek řádku|U + 000D|  
|\t| tabulátor|U + 0009|  
|\uXXXX|Znak Unicode definovaný 4 šestnáctkovými číslicemi.|U + XXXX|  

## <a name="next-steps"></a>Další kroky

- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelování dat dokumentů](modeling-data.md)
