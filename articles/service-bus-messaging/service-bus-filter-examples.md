---
title: Nastavit filtry předplatných v Azure Service Bus | Microsoft Docs
description: Tento článek popisuje příklady definování filtrů a akcí v předplatných tématu Azure Service Bus.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654170"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Nastavit filtry předplatného (Azure Service Bus)
Tento článek poskytuje několik příkladů nastavení filtrů pro Service Bus odběry tématu. Koncepční informace o filtrech najdete v tématu [filtry](topic-filters.md).

## <a name="filter-on-system-properties"></a>Filtrovat podle vlastností systému
Chcete-li ve filtru odkazovat na vlastnost systému, použijte následující formát: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filtrovat podle vlastností zprávy
Tady jsou příklady použití vlastností zprávy ve filtru. K vlastnostem zprávy můžete přistupovat pomocí `user.property-name` nebo pouze `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filtrování vlastností zprávy se speciálními znaky
Pokud název vlastnosti zprávy obsahuje speciální znaky, uzavřete název vlastnosti pomocí dvojitých uvozovek ( `"` ). Například pokud je název vlastnosti `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , použijte ve filtru následující syntaxi. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filtrování vlastností zprávy pomocí číselných hodnot
Následující příklady ukazují, jak můžete ve filtrech používat vlastnosti s číselnými hodnotami. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Filtry založené na parametrech
Tady je několik příkladů použití filtrů založených na parametrech. V těchto příkladech `DataTimeMp` je vlastnost zprávy typu `DateTime` a `@dtParam` je parametr předaný do filtru jako `DateTime` objekt.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Použití IN a NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ukázku pro C# najdete v tématu [ukázky filtrů témat na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filter-using-correlationid"></a>Filtr korelace s použitím ID korelace

```csharp
new CorrelationFilter("Contoso");
```

Filtruje zprávy s `CorrelationID` nastavením na `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Filtr korelace pomocí vlastností systému a uživatele

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Je ekvivalentní: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Další kroky
Podívejte se na následující ukázky: 

- [Kurz pro odesílání a přijímání v rámci .NET Basic s filtry](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – filtry témat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Šablona Azure Resource Manageru](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)