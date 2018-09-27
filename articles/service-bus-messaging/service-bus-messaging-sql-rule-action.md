---
title: Referenční příručka syntaxe SQLRuleAction v Azure | Dokumentace Microsoftu
description: Podrobnosti o SQLRuleAction gramatiky.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: cd5bc1fb959872cfb98236feb5c32517ac7472ac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394178"
---
# <a name="sqlruleaction-syntax"></a>Syntaxe SQLRuleAction

A *SqlRuleAction* je instance [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) třídy a představuje sadu akcí, které jsou napsané v jazyce SQL na základě syntaxi, která se provádí proti [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Tento článek obsahuje podrobnosti o gramatice akce pravidla SQL.  
  
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
  
-   `<scope>` je volitelný řetězec označující oboru `<property_name>`. Platné hodnoty jsou `sys` nebo `user`. `sys` Hodnota označuje obor systému kde `<property_name>` je veřejná vlastnost název [BrokeredMessage třídy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` Určuje obor uživatele kde `<property_name>` je klíč [BrokeredMessage třídy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) slovníku. `user` Obor je výchozí obor, pokud `<scope>` není zadán.  
  
### <a name="remarks"></a>Poznámky  

Pokus o přístup k systému neexistující vlastnost se o chybu při pokusu o přístup k vlastnosti uživatele neexistuje, není to chyba. Místo toho vlastnost uživatele neexistující interně vyhodnotí jako neznámou hodnotu. Neznámá hodnota považuje speciálně během vyhodnocení operátoru.  
  
## <a name="propertyname"></a>%{Property_Name/  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  
 `<regular_identifier>` představuje řetězec následující regulární výraz:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 To znamená, že jakýkoli řetězec, který začíná písmenem a postupuje podle jednoho nebo více podtržítka/písmeno nebo číslice.  
  
 `[:IsLetter:]` znamená, že libovolný znak Unicode, který je zařazený do kategorie jako Unicode písmeno. `System.Char.IsLetter(c)` Vrátí `true` Pokud `c` písmeno kódování Unicode.  
  
 `[:IsDigit:]` znamená, že libovolný znak Unicode, který je zařazený do kategorie jako desítková číslice. `System.Char.IsDigit(c)` Vrátí `true` Pokud `c` číslice sady Unicode.  
  
 A `<regular_identifier>` nemůže být rezervované klíčové slovo.  
  
 `<delimited_identifier>` je jakýkoli řetězec, který je součástí levé/pravé hranaté závorky ([]). Pravá hranatá závorka je vyjádřena jako dvě pravé hranaté závorky. Následují příklady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` je jakýkoli řetězec, který je uzavřen pomocí uvozovek. Dvojitá uvozovka v identifikátoru je reprezentován dvěma znaky uvozovek. Není doporučeno používat identifikátory v uvozovkách, protože ho můžete snadno zaměnitelná s řetězcová konstanta. Pokud je to možné použijte s oddělovači identifikátor. Následuje příklad `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Vzor  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
 `<pattern>` musí být výraz, který je vyhodnocen jako řetězec. Slouží jako vzor pro operátor LIKE.      Může obsahovat následující znaky:  
  
-   `%`: Řetězec nula nebo více znaků.  
  
-   `_`: Jakémukoli jednomu znaku.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
 `<escape_char>` musí být výraz, který se vyhodnotí jako řetězec s délkou 1. Slouží jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` místo řetězec, který začíná `ABC`.  
  
## <a name="constant"></a>Konstanty  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` je řetězec čísla, které nejsou uzavřeny v uvozovkách a neobsahují desetinné čárky. Hodnoty jsou uloženy jako `System.Int64` interně a postupujte podle stejného rozsahu.  
  
     Následují příklady dlouhé konstant:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` je řetězec čísel, které nejsou uzavřeny v uvozovkách a obsahovat desetinné čárky. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejného rozsahu/přesnosti.  
  
     V budoucí verzi se toto číslo může být uložen v jiný datový typ pro podporu přesný počet sémantiku, takže byste neměli spoléhat na skutečnost základní datový typ je `System.Double` pro `<decimal_constant>`.  
  
     Následují příklady desítkové konstanty:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` je počet napsaných v vědecký zápis. Hodnoty jsou uloženy jako `System.Double` interně a postupujte podle stejného rozsahu/přesnosti. Následují příklady přibližné číselné konstanty:  
  
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
  
Logické konstanty jsou reprezentovány klíčová slova `TRUE` nebo `FALSE`. Hodnoty jsou uloženy jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Poznámky
  
Řetězcové konstanty jsou uzavřeny v jednoduchých uvozovkách a obsahovat libovolné platné znaky Unicode. Jednoduché uvozovky součástí řetězcová konstanta je reprezentována jako dvě jednoduché uvozovky.  
  
## <a name="function"></a>Funkce  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Poznámky  

`newid()` Vrací funkce **System.Guid** generovaných `System.Guid.NewGuid()` metody.  
  
`property(name)` Funkce vrací hodnotu pro vlastnost odkazuje `name`. `name` Hodnota může být libovolný platný výraz, který vrátí hodnotu řetězce.  
  
## <a name="considerations"></a>Požadavky

- Sada se používá k vytvoření nové vlastnosti nebo aktualizujte hodnotu existující vlastnosti.
- ODEBRAT slouží k odebrání vlastnosti.
- Sada provede implicitní převod Pokud je to možné, když typ výrazu a existující typ vlastnosti se liší.
- Akce selže, pokud bylo odkazováno vlastnosti systému neexistuje.
- Akce nezdaří, pokud bylo odkazováno vlastnosti uživatele neexistuje.
- Vlastnost neexistující uživatele je vyhodnocen jako "Neznámá" interně, následující stejnou sémantiku jako [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) při vyhodnocování operátory.

## <a name="next-steps"></a>Další postup

- [Třída SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Třída SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
