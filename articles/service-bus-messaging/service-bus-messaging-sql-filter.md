---
title: Odkaz na syntaxi služby Azure Service Bus SQLFilter | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobnosti o sqlfilter gramatiky. SqlFilter podporuje podmnožinu standardu SQL-92.
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
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759659"
---
# <a name="sqlfilter-syntax"></a>Syntaxe SQLFilter

Objekt *SqlFilter* je instancí [třídy SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)a představuje výraz filtru založený na jazyce SQL, který je vyhodnocen proti [brokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter podporuje podmnožinu standardu SQL-92.  
  
 Toto téma obsahuje podrobnosti o gramatice SqlFilter.  
  
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
  
-   `<scope>`je volitelný řetězec označující rozsah `<property_name>`. Platné hodnoty `sys` `user`jsou nebo . Hodnota `sys` označuje rozsah `<property_name>` systému, kde je název veřejné vlastnosti [BrokeredMessage třídy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`označuje obor `<property_name>` uživatele, kde je klíč [BrokeredMessage třídy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) slovníku. `user`obor je výchozí `<scope>` obor, pokud není zadán.  
  
## <a name="remarks"></a>Poznámky

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
  
Tato gramatika znamená libovolný řetězec, který začíná písmenem a následuje jedno nebo více podtržítek/písmeno/číslice.  
  
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
  
     Toto jsou příklady dlouhých konstant:  
  
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

Logické konstanty jsou reprezentovány klíčovými slovy **PRAVDA** nebo **NEPRAVDA**. Hodnoty jsou uloženy jako `System.Boolean`.  
  
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
  
Zvažte následující sémantiku [sqlfilter:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Názvy vlastností nerozlišují malá a velká písmena.  
  
-   Operátory sledovat C# implicitní převod sémantiku, kdykoli je to možné.  
  
-   Vlastnosti systému jsou veřejné vlastnosti vystavené v instancích [BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)  
  
    Zvažte `IS [NOT] NULL` následující sémantiku:  
  
    -   `property IS NULL`je vyhodnocena, jako `true` by vlastnost neexistuje nebo `null`hodnota vlastnosti je .  
  
### <a name="property-evaluation-semantics"></a>Sémantiku hodnocení vlastností  
  
- Pokus o vyhodnocení neexistující vlastnostsystému vyvolá výjimku [FilterException.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Vlastnost, která neexistuje, je interně vyhodnocena jako **neznámá**.  
  
  Neznámé hodnocení u aritmetických operátorů:  
  
- U binárních operátorů, pokud je levá nebo pravá strana operandů vyhodnocena jako **neznámá**, výsledek není **znám**.  
  
- U unárních operátorů, pokud je operand vyhodnocen jako **neznámý**, výsledek není **znám**.  
  
  Neznámé vyhodnocení v binárních operátorech porovnání:  
  
- Pokud je levá a/nebo pravá strana operandů vyhodnocena jako **neznámá**, výsledek není **znám**.  
  
  Neznámé hodnocení `[NOT] LIKE`v :  
  
- Pokud je některý operand vyhodnocen jako **neznámý**, výsledek není **znám**.  
  
  Neznámé hodnocení `[NOT] IN`v :  
  
- Pokud je levý operand vyhodnocen jako **neznámý**, výsledek není **znám**.  
  
  Neznámé vyhodnocení v **operátoru AND:**  
  
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
  
 Neznámé hodnocení v operátoru **OR:**  
  
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
  
### <a name="operator-binding-semantics"></a>Sémantiku vázací operátora
  
-   Operátory porovnání, `>`jako `<=` `!=`je `=` například , `>=` `<`, , a postupujte podle stejné sémantiky jako operátor Jazyka C# v povýšení datového typu a implicitní převody.  
  
-   Aritmetické operátory, `-` `*`jako `/` `+`je například , , , a `%` postupujte podle stejné sémantiky jako operátor Jazyka C# v povýšení datového typu a implicitní převody.

## <a name="next-steps"></a>Další kroky

- [Třída SQLFilter (rozhraní.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Třída SQLFilter (standard.NET)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Třída SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
