---
title: Reference k syntaxi Azure Service Bus SQLFilter | Microsoft Docs
description: Tento článek poskytuje podrobné informace o SQLFilter gramatiky. SqlFilter podporuje podmnožinu standardu SQL-92.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759659"
---
# <a name="sqlfilter-syntax"></a>Syntaxe SQLFilter

Objekt *SqlFilter* je instancí [třídy SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)a představuje výraz filtru založený na jazyce SQL, který je vyhodnocován proti [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter podporuje podmnožinu standardu SQL-92.  
  
 V tomto tématu jsou uvedeny podrobnosti o gramatice SqlFilter.  
  
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
  
-   `<scope>` je volitelný řetězec, který označuje rozsah `<property_name>`. Platné hodnoty jsou `sys` nebo `user`. Hodnota `sys` označuje obor systému, kde `<property_name>` je název veřejné vlastnosti [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` označuje obor uživatele, kde `<property_name>` je klíč slovníku [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . obor `user` je výchozím oborem, pokud není zadán `<scope>`.  
  
## <a name="remarks"></a>Poznámky

Pokus o přístup k neexistující systémové vlastnosti je chyba, ale pokus o přístup k neexistující vlastnosti uživatele není chyba. Místo toho je vlastnost neexistujícího uživatele interně vyhodnocena jako neznámá hodnota. Neznámá hodnota je zpracována speciálně při vyhodnocování operátoru.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  

 `<regular_identifier>` je řetězec reprezentovaný následujícím regulárním výrazem:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Tato gramatika znamená libovolný řetězec, který začíná písmenem a následuje za jedním nebo více podtržítkem, písmenem nebo číslicí.  
  
`[:IsLetter:]` znamená jakýkoli znak Unicode, který je zařazený do kategorie jako písmeno Unicode. `System.Char.IsLetter(c)` vrátí `true`, pokud `c` je písmeno Unicode.  
  
`[:IsDigit:]` znamená, že znak Unicode, který je zařazený jako desítková číslice. `System.Char.IsDigit(c)` vrátí `true`, pokud `c` je číslice Unicode.  
  
`<regular_identifier>` nemůže být rezervované klíčové slovo.  
  
`<delimited_identifier>` je libovolný řetězec, který je uzavřený pomocí levé nebo pravé hranaté závorky ([]). Pravá hranatá závorka je vyjádřena jako dvě pravé hranaté závorky. Níže jsou uvedeny příklady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` je libovolný řetězec, který je uzavřený pomocí dvojitých uvozovek. Dvojité uvozovky v identifikátoru jsou reprezentovány dvěma dvojitými uvozovkami. Nedoporučuje se používat identifikátory v uvozovkách, protože je lze snadno zaměňovat pomocí řetězcové konstanty. Pokud je to možné, použijte oddělený identifikátor. Následuje příklad `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>vzorku  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
`<pattern>` musí být výraz, který je vyhodnocen jako řetězec. Slouží jako vzor pro operátor LIKE.      Může obsahovat následující zástupné znaky:  
  
-   `%`: libovolný řetězec nula nebo více znaků.  
  
-   `_`: jakýkoli jeden znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky  

`<escape_char>` musí být výraz, který je vyhodnocen jako řetězec o délce 1. Slouží jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` namísto řetězce, který začíná `ABC`.  
  
## <a name="constant"></a>Konstanty  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` je řetězec čísel, který není uzavřen v uvozovkách a neobsahuje desetinná místa. Hodnoty se ukládají jako `System.Int64` interně a používají stejný rozsah.  
  
     Jedná se například o dlouhé konstanty:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` je řetězec čísel, který není uzavřen v uvozovkách a obsahuje desetinnou čárku. Hodnoty jsou uloženy jako `System.Double` interně a budou následovat po stejném rozsahu nebo přesnosti.  
  
     V budoucí verzi může být toto číslo Uloženo v jiném datovém typu, aby podporovalo přesnou sémantiku čísel, takže byste neměli spoléhat na skutečnost, že podkladový datový typ je `System.Double` `<decimal_constant>`.  
  
     Následují příklady desítkových konstant:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` je číslo napsané v matematickém zápisu. Hodnoty jsou uloženy jako `System.Double` interně a budou následovat po stejném rozsahu nebo přesnosti. Následující příklady představují přibližné číselné konstanty:  
  
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

Logické konstanty jsou reprezentovány klíčovými slovy **true** nebo **false**. Hodnoty jsou uloženy jako `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Poznámky  

Řetězcové konstanty jsou uzavřeny v jednoduchých uvozovkách a obsahují všechny platné znaky Unicode. Jednoduché uvozovky vložené v řetězcové konstantě jsou reprezentovány jako dvě jednoduché uvozovky.  
  
## <a name="function"></a>slouží  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Poznámky
  
Funkce `newid()` vrací hodnotu **System. GUID** vygenerovanou metodou `System.Guid.NewGuid()`.  
  
Funkce `property(name)` vrací hodnotu vlastnosti, na kterou odkazuje `name`. Hodnotou `name` může být libovolný platný výraz, který vrací řetězcovou hodnotu.  
  
## <a name="considerations"></a>Požadavky
  
Vezměte v úvahu následující sémantiku [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) :  
  
-   V názvech vlastností se nerozlišují malá a velká písmena.  
  
-   Pokud je C# to možné, operátory budou používat sémantiku implicitního převodu.  
  
-   Vlastnosti systému jsou veřejné vlastnosti zveřejněné v [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instancích.  
  
    Vezměte v úvahu následující `IS [NOT] NULL` sémantika:  
  
    -   `property IS NULL` se vyhodnotí jako `true`, pokud buď vlastnost neexistuje, nebo je hodnota vlastnosti `null`.  
  
### <a name="property-evaluation-semantics"></a>Sémantika vyhodnocení vlastností  
  
- Pokus vyhodnotit neexistující systémovou vlastnost vyvolá výjimku [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) .  
  
- Vlastnost, která neexistuje, je interně vyhodnocena jako **neznámá**.  
  
  Neznámé vyhodnocení v aritmetických operátorech:  
  
- V případě binárních operátorů, pokud je levá nebo pravá strana operandů vyhodnocena jako **neznámá**, je výsledek **Neznámý**.  
  
- U unárních operátorů, pokud je operand vyhodnocen jako **Neznámý**, je výsledek **Neznámý**.  
  
  Neznámé vyhodnocení v binárních relačních operátorech:  
  
- Pokud je levá nebo pravá strana operandů vyhodnocena jako **neznámá**, výsledek je **Neznámý**.  
  
  Neznámé vyhodnocení v `[NOT] LIKE`:  
  
- Pokud je jakýkoli operand vyhodnocen jako **Neznámý**, výsledek je **Neznámý**.  
  
  Neznámé vyhodnocení v `[NOT] IN`:  
  
- Pokud je levý operand vyhodnocen jako **Neznámý**, výsledek je **Neznámý**.  
  
  Neznámé vyhodnocení v operátoru **and** :  
  
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
  
 Neznámé vyhodnocení v operátoru **or** :  
  
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
  
### <a name="operator-binding-semantics"></a>Sémantika vazby operátoru
  
-   Operátory porovnání, jako `>`, `>=`, `<`, `<=`, `!=`a `=`, dodržují stejnou sémantiku jako vazba C# operátoru v propagačních akcích datového typu a implicitní převody.  
  
-   Aritmetické operátory, jako `+`, `-`, `*`, `/`a `%`, sledují stejnou sémantiku jako C# vazby operátoru v propagačních akcích datového typu a implicitní převody.

## <a name="next-steps"></a>Další kroky

- [SQLFilter – třída (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter – třída (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction – třída](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
