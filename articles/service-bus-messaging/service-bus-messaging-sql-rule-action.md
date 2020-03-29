---
title: Odkaz na syntaxi SQLRuleAction v Azure Service Bus
description: Tento článek obsahuje odkaz na syntaxi SQLRuleAction. Akce jsou zapsány v syntaxi založené na jazyce SQL, která se provádí proti zprostředkované zprávy.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759624"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Odkaz na syntaxi SQLRuleAction pro Službu Azure Service Bus

A *SqlRuleAction* je instance třídy [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) a představuje sadu akcí napsaných v syntaxi založené na jazyce SQL, která se provádí proti [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
V tomto článku jsou uvedeny podrobnosti o gramatice akce pravidla SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>`je volitelný řetězec označující rozsah `<property_name>`. Platné hodnoty `sys` `user`jsou nebo . Hodnota `sys` označuje rozsah `<property_name>` systému, kde je název veřejné vlastnosti [BrokeredMessage Class](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`označuje obor `<property_name>` uživatele, kde je klíč [BrokeredMessage třídy slovníku.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`obor je výchozí `<scope>` obor, pokud není zadán.  
  
### <a name="remarks"></a>Poznámky  

Pokus o přístup k neexistující vlastnosti systému je chyba, zatímco pokus o přístup k neexistující vlastnosti uživatele není chyba. Místo toho je neexistující vlastnost uživatele interně vyhodnocena jako neznámá hodnota. Neznámá hodnota je zpracována speciálně během hodnocení operátora.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>`je řetězec reprezentované následujícím regulárním výrazem:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 To znamená, že každý řetězec, který začíná písmenem a následuje jeden nebo více podtržítek/písmeno/číslice.  
  
 `[:IsLetter:]`znamená libovolný znak Unicode, který je zařazen do kategorie písmenunicode. `System.Char.IsLetter(c)``true` vrátí, `c` pokud je písmeno Unicode.  
  
 `[:IsDigit:]`se rozumí libovolný znak Unicode, který je zařazen do kategorie desetinné číslice. `System.Char.IsDigit(c)``true` vrátí, `c` pokud je číslice Unicode.  
  
 A `<regular_identifier>` nemůže být vyhrazené klíčové slovo.  
  
 `<delimited_identifier>`je libovolný řetězec, který je uzavřen s levou/pravou hranatou závorkou ([]). Pravá hranatá závorka je reprezentována jako dvě pravé hranatá závorky. Následují příklady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`je libovolný řetězec, který je uzavřen s uvozovkami. Dvojitá uvozovka v identifikátoru je reprezentována jako dvě dvojité uvozovky. Nedoporučuje se používat identifikátory v uvozovkách, protože je lze snadno zaměnit s řetězcovou konstantou. Pokud je to možné, použijte oddělený identifikátor. Následuje příklad : `<quoted_identifier>`  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Vzor  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
 `<pattern>`musí být výraz, který je vyhodnocen jako řetězec. Používá se jako vzor pro operátor LIKE.      Může obsahovat následující zástupné znaky:  
  
-   `%`: Libovolný řetězec s nulovými nebo více znaky.  
  
-   `_`: Libovolný jednotlivý znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
 `<escape_char>`musí být výraz, který je vyhodnocen jako řetězec délky 1. Používá se jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` spíše než řetězec, `ABC`který začíná .  
  
## <a name="constant"></a> – konstanta  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`je řetězec čísel, která nejsou uzavřena v uvozovkách a neobsahují desetinné čárky. Hodnoty jsou uloženy jako `System.Int64` interně a postupujte podle stejného rozsahu.  
  
     Následují příklady dlouhých konstant:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`je řetězec čísel, které nejsou uzavřeny v uvozovkách a obsahují desetinnou čárku. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejného rozsahu/přesnosti.  
  
     V budoucí verzi může být toto číslo uloženo v jiném datovém typu, aby podporovalo sémantiku přesného čísla, takže byste neměli spoléhat na skutečnost, že základní datový typ je `System.Double` pro `<decimal_constant>`.  
  
     Následují příklady desetinných konstant:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`je číslo napsané ve vědeckém zápisu. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejného rozsahu/přesnosti. Následují příklady přibližných číselných konstant:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Poznámky
  
Logické konstanty jsou reprezentovány `TRUE` klíčovými slovy nebo `FALSE`. Hodnoty jsou uloženy jako `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Poznámky
  
Konstanty řetězců jsou uzavřeny v jednoduchých uvozovkách a obsahují všechny platné znaky Unicode. Jedna uvozovka vložená do řetězcové konstanty je reprezentována jako dvě jednoduché uvozovky.  
  
## <a name="function"></a> – funkce  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Poznámky  

Funkce `newid()` vrátí **System.Guid** generované `System.Guid.NewGuid()` metodou.  
  
Funkce `property(name)` vrátí hodnotu vlastnosti, `name`na kterou odkazuje . Hodnota `name` může být libovolný platný výraz, který vrací hodnotu řetězce.  
  
## <a name="considerations"></a>Požadavky

- SET se používá k vytvoření nové vlastnosti nebo aktualizaci hodnoty existující vlastnosti.
- REMOVE se používá k odebrání vlastnosti.
- SET provede implicitní převod, pokud je to možné, pokud typ výrazu a existující typ vlastnosti se liší.
- Akce se nezdaří, pokud byly odkazovány na neexistující vlastnosti systému.
- Akce se nezdaří, pokud byly odkazovány na neexistující uživatelské vlastnosti.
- Neexistující vlastnost uživatele je interně vyhodnocena jako "Neznámý", která při vyhodnocování operátorů sleduje stejnou sémantiku jako [SQLFilter.](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)

## <a name="next-steps"></a>Další kroky

- [Třída SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Třída SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
