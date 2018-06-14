---
title: Reference syntaxe Azure Service Bus SQLFilter | Microsoft Docs
description: Podrobnosti o SQLFilter gramatika.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: ec9d728eb31eb979e82bfb53cf619f823750e65c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132163"
---
# <a name="sqlfilter-syntax"></a>Syntaxe SQLFilter

A *SqlFilter* objekt je instance [SqlFilter třída](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)a představuje výraz filtru na základě jazyka SQL, který se vyhodnotí proti [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter podporuje podmnožinu standardní SQL 92.  
  
 Toto téma obsahuje podrobnosti o SqlFilter gramatika.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumenty  
  
-   `<scope>`je volitelný řetězec označující oboru `<property_name>`. Platné hodnoty jsou `sys` nebo `user`. `sys` Hodnota označuje rozsah systémů kde `<property_name>` je název veřejné vlastnosti [BrokeredMessage třída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`označuje oboru uživatele kde `<property_name>` je klíč z [BrokeredMessage třída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) slovníku. `user`rozsah je výchozí obor, pokud `<scope>` není zadán.  
  
## <a name="remarks"></a>Poznámky

Pokus o přístup k systému neexistující vlastnost je k chybě při pokusu o přístup k neexistující uživatele vlastnost není chyba. Místo toho vlastnost uživatele neexistující interně vyhodnotí jako neznámou hodnotou. Neznámá hodnota považuje speciálně během vyhodnocení operátor.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  

 `<regular_identifier>`řetězec reprezentována následujícímu regulárnímu výrazu:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Tato gramatika znamená libovolný řetězec, který začíná písmenem a následuje jeden nebo více podtržítko nebo písmeno nebo číslice.  
  
`[:IsLetter:]`znamená libovolný znak Unicode, který je zařazený do kategorie jako písmeno kódování Unicode. `System.Char.IsLetter(c)`Vrátí `true` Pokud `c` písmeno kódování Unicode.  
  
`[:IsDigit:]`znamená libovolný znak Unicode, který je zařazený do kategorie jako desítková číslice. `System.Char.IsDigit(c)`Vrátí `true` Pokud `c` je číslice kódování Unicode.  
  
A `<regular_identifier>` nemůže být rezervované klíčové slovo.  
  
`<delimited_identifier>`je řetězec, který je uzavřena s levé nebo pravé hranaté závorky ([]). Pravou hranatou závorku je reprezentován jako dvě pravé hranaté závorky. Následují příklady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`je řetězec, který je uzavřena dvojitých uvozovek nahoře. Dvojité uvozovky v identifikátoru je reprezentován jako dva znaky uvozovek. Není doporučeno použít identifikátory v uvozovkách, protože můžete snadno Nezaměňovat s řetězcová konstanta. Pokud je to možné použijte s oddělovači identifikátor. Tady je příklad `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>vzor  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
`<pattern>`musí být výraz, který se vyhodnotí jako řetězec. Použije se jako vzor pro operátor LIKE.      Může obsahovat následující znaky:  
  
-   `%`: Řetězec nula nebo více znaků.  
  
-   `_`: Jakémukoliv jedinému znaku.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky  

`<escape_char>`musí být výraz, který se vyhodnotí jako řetězec o délce 1. Slouží jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` místo řetězec, který začíná `ABC`.  
  
## <a name="constant"></a>Konstantní  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`je řetězec čísel, která se nenacházejí v uvozovkách a neobsahují desetinných míst. Hodnoty jsou uloženy jako `System.Int64` interně a postupujte podle stejného rozsahu.  
  
     Toto jsou příklady dlouho konstanty:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`je řetězec čísel, která se nenacházejí v uvozovkách a obsahovat desetinné čárky. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejné rozsah nebo přesnosti.  
  
     V budoucí verzi, může být tento číslo uložené v na jiný datový typ pro podporu přesné číslo sémantiku, takže byste neměli spoléhat na skutečnost, základní datový typ je `System.Double` pro `<decimal_constant>`.  
  
     Následují příklady decimal konstant:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`je číslo napsaných v exponenciální notace. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejné rozsah nebo přesnosti. Následují příklady přibližnou číselné konstanty:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Poznámky  

Logická hodnota konstanty jsou reprezentované pomocí klíčová slova **TRUE** nebo **FALSE**. Hodnoty jsou uloženy jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Poznámky  

Řetězcové konstanty jsou uzavřené v jednoduchých uvozovkách a zahrnují všechny platné znaky kódování Unicode. Jednoduché uvozovky, vložené do řetězcová konstanta je reprezentována jako dvě jednoduché uvozovky.  
  
## <a name="function"></a>Funkce  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Poznámky
  
`newid()` Funkce vrátí **System.Guid** vygenerované `System.Guid.NewGuid()` metoda.  
  
`property(name)` Funkce vrátí hodnotu vlastnosti odkazuje `name`. `name` Hodnota může být libovolný platný výraz, který vrací řetězcovou hodnotu.  
  
## <a name="considerations"></a>Požadavky
  
Vezměte v úvahu následující [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) sémantiku:  
  
-   Názvy vlastností se velká a malá písmena.  
  
-   Operátory podle jazyka C# implicitní převod sémantiku kdykoli je to možné.  
  
-   Vlastnosti systému jsou veřejné vlastnosti v [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instance.  
  
    Vezměte v úvahu následující `IS [NOT] NULL` sémantiku:  
  
    -   `property IS NULL`vyhodnotí jako `true` Pokud vlastnost neexistuje nebo je hodnota vlastnosti `null`.  
  
### <a name="property-evaluation-semantics"></a>Sémantika vyhodnocení vlastnosti  
  
-   Vyvolá pokus o vyhodnocení vlastnost neexistující systému [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) výjimka.  
  
-   Vlastnost, která neexistuje interně vyhodnotí jako **neznámé**.  
  
 Neznámý vyhodnocení v aritmetické operátory:  
  
-   Pro binární operátory, pokud zadaný levé nebo pravé straně operandy vyhodnotí jako **neznámé**, potom je **neznámé**.  
  
-   Pro unární operátory, pokud operand vyhodnotí jako **neznámé**, potom je **neznámé**.  
  
 Neznámý vyhodnocení v binární porovnání operátory:  
  
-   Pokud v levé nebo pravé straně operandy vyhodnotí jako **neznámé**, potom je **neznámé**.  
  
 Neznámý vyhodnocení v `[NOT] LIKE`:  
  
-   Pokud operandem any vyhodnotí jako **neznámé**, potom je **neznámé**.  
  
 Neznámý vyhodnocení v `[NOT] IN`:  
  
-   Pokud je levý operand vyhodnoceny jako **neznámé**, potom je **neznámé**.  
  
 Neznámý vyhodnocení v **a** operátor:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Neznámý vyhodnocení v **nebo** operátor:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operátor sémantiku vazby
  
-   Operátory porovnání jako `>`, `>=`, `<`, `<=`, `!=`, a `=` použijte stejnou sémantiku jako vazby ve povýšení typ dat a implicitní převody operátor C#.  
  
-   Aritmetické operátory jako `+`, `-`, `*`, `/`, a `%` použijte stejnou sémantiku jako vazby ve povýšení typ dat a implicitní převody operátor C#.

## <a name="next-steps"></a>Další postup

- [Třída SQLFilter (rozhraní .NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter – třída (standardní .NET)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [SQLRuleAction – třída](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)