---
title: Odkaz syntaxe SQLRuleAction v Azure Service Bus
description: Tento článek poskytuje referenci pro SQLRuleAction syntax. Akce jsou napsány v syntaxi založené na jazyku SQL, která je provedena proti zprostředkované zprávě.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 61fa6e046b4d4a0ba91bf8608c846755026d07ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341586"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Reference syntaxe SQLRuleAction pro Azure Service Bus

*SqlRuleAction* je instancí třídy [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) a představuje sadu akcí napsaných v SYNTAXI založené na jazyce SQL, která se provádí proti [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
V tomto článku jsou uvedeny podrobnosti o gramatické akci pravidla SQL.  
  
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
  
-   `<scope>` je volitelný řetězec, který označuje rozsah `<property_name>` . Platné hodnoty jsou `sys` nebo `user` . `sys`Hodnota označuje rozsah systému, kde `<property_name>` je název veřejné vlastnosti [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` označuje obor uživatele, kde `<property_name>` je klíč slovníku [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` Obor je výchozím rozsahem, pokud není `<scope>` zadán.  
  
### <a name="remarks"></a>Poznámky  

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
  
 To znamená, že všechny řetězce, které začínají písmenem a jsou následovány jedním nebo více podtržítkem/písmenem nebo číslicí.  
  
 `[:IsLetter:]` označuje libovolný znak Unicode, který je zařazený do kategorie jako písmeno Unicode. `System.Char.IsLetter(c)` Vrátí, `true` Pokud `c` je písmeno Unicode.  
  
 `[:IsDigit:]` znamená, že každý znak Unicode, který je zařazen jako desítkovou číslici. `System.Char.IsDigit(c)` Vrátí, `true` Pokud `c` je číslice Unicode.  
  
 `<regular_identifier>`Klíčové slovo nemůže být rezervované.  
  
 `<delimited_identifier>` je libovolný řetězec, který je uzavřený pomocí levé nebo pravé hranaté závorky ([]). Pravá hranatá závorka je vyjádřena jako dvě pravé hranaté závorky. Následují příklady `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` je libovolný řetězec, který je uzavřený pomocí dvojitých uvozovek. Dvojité uvozovky v identifikátoru jsou reprezentovány dvěma dvojitými uvozovkami. Nedoporučuje se používat identifikátory v uvozovkách, protože je lze snadno zaměňovat pomocí řetězcové konstanty. Pokud je to možné, použijte oddělený identifikátor. Zde je příklad `<quoted_identifier>` :  
  
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
  
-   `%`: Libovolný řetězec nula nebo více znaků.  
  
-   `_`: Libovolný jeden znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Poznámky
  
 `<escape_char>` musí se jednat o výraz, který se vyhodnotí jako řetězec o délce 1. Slouží jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` místo řetězce, který začíná na `ABC` .  
  
## <a name="constant"></a> – konstanta  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` je řetězec čísel, který není uzavřen v uvozovkách a neobsahuje desetinná místa. Hodnoty se ukládají jako `System.Int64` interně a používají stejný rozsah.  
  
     Následují příklady dlouhých konstant:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` je řetězec čísel, který není uzavřen v uvozovkách a obsahuje desetinnou čárku. Hodnoty se ukládají jako `System.Double` interně a používají stejný rozsah nebo přesnost.  
  
     V budoucí verzi může být toto číslo Uloženo v jiném datovém typu, aby podporovalo přesnou sémantiku čísel, takže byste neměli spoléhat na skutečnost, že základní datový typ je `System.Double` `<decimal_constant>` .  
  
     Následují příklady desítkových konstant:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` je číslo napsané v matematickém zápisu. Hodnoty se ukládají jako `System.Double` interně a používají stejný rozsah nebo přesnost. Následující příklady představují přibližné číselné konstanty:  
  
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
  
Logické konstanty jsou reprezentovány klíčovými slovy `TRUE` nebo `FALSE` . Hodnoty jsou uloženy jako `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Poznámky
  
Řetězcové konstanty jsou uzavřeny v jednoduchých uvozovkách a obsahují všechny platné znaky Unicode. Jednoduché uvozovky vložené v řetězcové konstantě jsou reprezentovány jako dvě jednoduché uvozovky.  
  
## <a name="function"></a> – funkce  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Poznámky  

`newid()`Funkce vrátí hodnotu **System. GUID** vygenerovanou `System.Guid.NewGuid()` metodou.  
  
`property(name)`Funkce vrátí hodnotu vlastnosti, na kterou odkazuje `name` . `name`Hodnotou může být libovolný platný výraz, který vrací řetězcovou hodnotu.  
  
## <a name="considerations"></a>Důležité informace

- SADA se používá k vytvoření nové vlastnosti nebo aktualizaci hodnoty existující vlastnosti.
- K odebrání vlastnosti se používá odebrání.
- Pokud je to možné, je nastavení provedeno implicitní převod, pokud je typ výrazu jiný a existující typ vlastnosti.
- Akce se nezdařila, pokud nebyly odkazovány na neexistující systémové vlastnosti.
- Akce se nezdařila, pokud nebyly odkazovány na neexistující vlastnosti uživatele.
- Neexistující vlastnost uživatele je vyhodnocena jako "neznámá" interně za stejnou sémantikou jako [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) při vyhodnocování operátorů.

## <a name="next-steps"></a>Další kroky

- [SQLRuleAction – třída](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter – třída](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
