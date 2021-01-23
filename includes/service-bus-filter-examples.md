---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742675"
---
## <a name="examples"></a>Příklady

### <a name="filter-on-system-properties"></a>Filtrovat podle vlastností systému
Chcete-li ve filtru odkazovat na vlastnost systému, použijte následující formát: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrovat podle vlastností zprávy
Tady jsou příklady použití vlastností zprávy ve filtru. K vlastnostem zprávy můžete přistupovat pomocí `user.property-name` nebo pouze `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrování vlastností zprávy se speciálními znaky
Pokud název vlastnosti zprávy obsahuje speciální znaky, uzavřete název vlastnosti pomocí dvojitých uvozovek ( `"` ). Například pokud je název vlastnosti `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , použijte ve filtru následující syntaxi. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrování vlastností zprávy pomocí číselných hodnot
Následující příklady ukazují, jak můžete ve filtrech používat vlastnosti s číselnými hodnotami. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtry založené na parametrech
Tady je několik příkladů použití filtrů založených na parametrech. V těchto příkladech `DataTimeMp` je vlastnost zprávy typu `DateTime` a `@dtParam` je parametr předaný do filtru jako `DateTime` objekt.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Použití IN a NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ukázku pro C# najdete v tématu [ukázky filtrů témat na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


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

### <a name="correlation-filter-using-correlationid"></a>Filtr korelace s použitím ID korelace

```csharp
new CorrelationFilter("Contoso");
```

Filtruje zprávy s `CorrelationID` nastavením na `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Filtr korelace pomocí vlastností systému a uživatele

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Je ekvivalentní: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

