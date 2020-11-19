---
title: Reference k syntaxi Azure Service Bus SQLFilter | Microsoft Docs
description: Tento článek poskytuje podrobné informace o SQLFilter gramatiky. SqlFilter podporuje podmnožinu standardu SQL-92.
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 7f3c744b691e678ef18c8fa721ccfaecaee9c1e2
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888466"
---
# <a name="sqlfilter-syntax"></a>Syntaxe SQLFilter

Objekt *SqlFilter* je instancí [třídy SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)a představuje výraz filtru založený na jazyce SQL, který je vyhodnocován proti [`BrokeredMessage`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . SqlFilter podporuje podmnožinu standardu SQL-92.  
  
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
  
## <a name="arguments"></a>Arguments  
  
-   `<scope>` je volitelný řetězec, který označuje rozsah `<property_name>` . Platné hodnoty jsou `sys` nebo `user` . `sys`Hodnota označuje rozsah systému, kde `<property_name>` je název veřejné vlastnosti [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` označuje obor uživatele, kde `<property_name>` je klíč slovníku [třídy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` Obor je výchozím oborem, pokud `<scope>` není zadán.  
  
## <a name="remarks"></a>Poznámky

Pokus o přístup k neexistující systémové vlastnosti je chyba, zatímco pokus o přístup k neexistující vlastnosti uživatele není chyba. Místo toho je vlastnost neexistujícího uživatele interně vyhodnocena jako neznámá hodnota. Neznámá hodnota je zpracována speciálně při vyhodnocování operátoru.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Arguments  

 `<regular_identifier>` je řetězec reprezentovaný následujícím regulárním výrazem:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Tato gramatika znamená libovolný řetězec, který začíná písmenem a následuje za jedním nebo více podtržítkem, písmenem nebo číslicí.  
  
`[:IsLetter:]` označuje libovolný znak Unicode, který je zařazený do kategorie jako písmeno Unicode. `System.Char.IsLetter(c)` Vrátí, `true` Pokud `c` je písmeno Unicode.  
  
`[:IsDigit:]` znamená, že každý znak Unicode, který je zařazen jako desítkovou číslici. `System.Char.IsDigit(c)` Vrátí, `true` Pokud `c` je číslice Unicode.  
  
`<regular_identifier>`Klíčové slovo nemůže být rezervované.  
  
`<delimited_identifier>` je libovolný řetězec, který je uzavřený pomocí levé nebo pravé hranaté závorky ([]). Pravá hranatá závorka je vyjádřena jako dvě pravé hranaté závorky. Následují příklady `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` je libovolný řetězec, který je uzavřený pomocí dvojitých uvozovek. Dvojité uvozovky v identifikátoru jsou reprezentovány dvěma dvojitými uvozovkami. Nedoporučuje se používat identifikátory v uvozovkách, protože je možné snadno zaměňovat pomocí řetězcové konstanty. Pokud je to možné, použijte oddělený identifikátor. Tady je příklad `<quoted_identifier>` :  
  
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

`<escape_char>` musí se jednat o výraz, který se vyhodnotí jako řetězec o délce 1. Používá se jako řídicí znak pro operátor LIKE.  
  
 Například `property LIKE 'ABC\%' ESCAPE '\'` odpovídá `ABC%` místo řetězce, který začíná na `ABC` .  
  
## <a name="constant"></a> – konstanta  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Arguments  
  
-   `<integer_constant>` je řetězec čísel, který není uzavřen v uvozovkách a neobsahuje desetinná místa. Hodnoty se ukládají jako `System.Int64` interně a používají stejný rozsah.  
  
     Tady jsou příklady dlouhých konstant:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` je řetězec čísel, který není uzavřen v uvozovkách a obsahuje desetinnou čárku. Hodnoty se ukládají jako `System.Double` interně a používají stejný rozsah nebo přesnost.  
  
     V budoucí verzi může být toto číslo Uloženo v jiném datovém typu pro podporu přesné sémantiky čísel, takže byste neměli spoléhat na skutečnost, že základní datový typ je `System.Double` pro `<decimal_constant>` .  
  
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

Logické konstanty jsou reprezentovány klíčovými slovy **true** nebo **false**. Hodnoty jsou uloženy jako `System.Boolean` .  
  
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
  
`newid()`Funkce vrátí `System.Guid` vygenerovanou `System.Guid.NewGuid()` metodou.  
  
`property(name)`Funkce vrátí hodnotu vlastnosti, na kterou odkazuje `name` . `name`Hodnotou může být libovolný platný výraz, který vrací řetězcovou hodnotu.  
  
## <a name="considerations"></a>Co je potřeba vzít v úvahu
  
Vezměte v úvahu následující sémantiku [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) :  
  
-   V názvech vlastností se nerozlišují malá a velká písmena.  
  
-   Operátory dodržují sémantiku implicitního převodu jazyka C#, kdykoli je to možné.  
  
-   Vlastnosti systému jsou veřejné vlastnosti zveřejněné v [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instancích.  
  
    Vezměte v úvahu následující `IS [NOT] NULL` sémantiku:  
  
    -   `property IS NULL` je vyhodnocen jako, `true` Pokud buď vlastnost neexistuje, nebo je hodnota vlastnosti `null` .  
  
### <a name="property-evaluation-semantics"></a>Sémantika vyhodnocení vlastností  
  
- Pokus vyhodnotit neexistující systémovou vlastnost vyvolá výjimku [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) .  
  
- Vlastnost, která neexistuje, je interně vyhodnocena jako **neznámá**.  
  
  Neznámé vyhodnocení v aritmetických operátorech:  
  
- V případě binárních operátorů, pokud je levá nebo pravá strana operandů vyhodnocena jako **neznámá**, je výsledek **Neznámý**.  
  
- U unárních operátorů, pokud je operand vyhodnocen jako **Neznámý**, je výsledek **Neznámý**.  
  
  Neznámé vyhodnocení v binárních relačních operátorech:  
  
- Pokud je levá nebo pravá strana operandů vyhodnocena jako **neznámá**, výsledek je **Neznámý**.  
  
  Neznámé vyhodnocení v `[NOT] LIKE` :  
  
- Pokud je jakýkoli operand vyhodnocen jako **Neznámý**, výsledek je **Neznámý**.  
  
  Neznámé vyhodnocení v `[NOT] IN` :  
  
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
  
-   Relační operátory, jako například,,, `>` `>=` `<` `<=` , `!=` a `=` dodržují stejnou sémantiku jako vazba operátoru jazyka C# v propagačních akcích datových typů a implicitních převodech.  
  
-   Aritmetické operátory jako `+` , `-` , `*` , `/` a `%` sledují stejnou sémantiku jako vazba operátoru jazyka C# v propagačních akcích datových typů a implicitních převodech.


## <a name="examples"></a>Příklady

### <a name="set-rule-action-for-a-sql-filter"></a>Nastavit akci pravidla pro filtr SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>Filtr SQL pro systémovou vlastnost

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>Použití nebo 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Použití IN a NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ukázku pro C# najdete v tématu [ukázky filtrů témat na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="next-steps"></a>Další kroky

- [SQLFilter – třída (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter – třída (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction – třída](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
