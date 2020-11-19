---
title: Azure Service Bus filtry témat | Microsoft Docs
description: Tento článek vysvětluje, jak můžou předplatitelé definovat, které zprávy chce přijímat z tématu zadáním filtrů.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 04ae585c42f8acfbf338bf23befb32a5521fcf57
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889027"
---
# <a name="topic-filters-and-actions"></a>Akce a filtry témat

Odběratelé mohou definovat zprávy, které chtějí z tématu přijímat. Tyto zprávy se určují ve formě jednoho nebo více pojmenovaných pravidel odběru. Každé pravidlo se skládá z podmínky, která vybere konkrétní zprávy a akci, která vydává poznámku vybrané zprávě. Pro každou odpovídající podmínku pravidla odběr vytvoří kopii zprávy, která může pro každé odpovídající pravidlo obsahovat jiné poznámky.

Každé nově vytvořené předplatné tématu má počáteční výchozí pravidlo předplatného. Pokud explicitně neurčíte podmínku filtru pro pravidlo, je použit filtr na **true** , který umožňuje výběr všech zpráv v rámci předplatného. K výchozímu pravidlu není přidružena žádná akce anotace.

Service Bus podporuje tři podmínky filtru:

-   *Logické filtry* – **TrueFilter** a **FalseFilter** buď způsobí, že se pro odběr vybraly všechny přicházející zprávy (**true**) nebo žádná z doručených zpráv (**false**). Tyto dva filtry jsou odvozeny z filtru SQL. 

-   *Filtry SQL* – **SqlFilter** obsahuje podmíněný výraz podobný SQL, který se vyhodnocuje ve zprostředkovateli proti uživatelsky definovaným vlastnostem a vlastnostem systému. Všechny vlastnosti systému musí být `sys.` v podmíněném výrazu předponou. [Podmnožina jazyka SQL pro podmínky filtru](service-bus-messaging-sql-filter.md) testy pro existenci vlastností ( `EXISTS` ), hodnot null ( `IS NULL` ), logických operátorů not/a/nebo, relačních operátorů, jednoduchého číselného aritmetického zpracování a jednoduchých textových vzorů `LIKE` .

-   *Filtry korelace* – **CorrelationFilter** obsahuje sadu podmínek, které se shodují s jednou nebo více uživateli a vlastnostmi systému, které přicházejí. Běžné použití se shoduje s vlastností **ID korelace** , ale aplikace může také zvolit shodu s následujícími vlastnostmi:

    - **Třída**
     - **Popisek**
     - **Parametr**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Schopn**
     - jakékoli uživatelsky definované vlastnosti. 
     
     Shoda existuje, pokud je hodnota doručené zprávy pro vlastnost rovna hodnotě zadané ve filtru korelace. U řetězcových výrazů porovnávání rozlišuje velká a malá písmena. Pokud zadáte více vlastností shody, filtr je zkombinuje jako logický operátor AND, což znamená, že aby se filtr shodoval, všechny podmínky se musí shodovat.

Všechny filtry vyhodnocují vlastnosti zprávy. Filtry nemůžou vyhodnotit tělo zprávy.

Složitá pravidla filtru vyžadují kapacitu zpracování. Konkrétně použití pravidel filtru SQL způsobí snížení celkové propustnosti zpráv na úrovni předplatného, tématu a oboru názvů. Kdykoli je to možné, aplikace by měly zvolit filtry korelace přes filtry podobné SQL, protože jsou mnohem efektivnější ve zpracování a mají méně vliv na propustnost.

## <a name="actions"></a>Akce

Pomocí podmínek filtru SQL můžete definovat akci, která může opatřit poznámky zprávou přidáním, odebráním nebo nahrazením vlastností a jejich hodnot. Akce [používá výraz podobný jazyku SQL](service-bus-messaging-sql-filter.md) , který je volně vylibové na SYNTAXI příkazu SQL Update. Tato akce se provádí ve zprávě poté, co byla shodná a před tím, než je vybrána zpráva v rámci předplatného. Změny vlastností zprávy jsou pro zprávu zkopírovanou do předplatného soukromé.

## <a name="usage-patterns"></a>Vzory využití

Nejjednodušším scénářem použití pro téma je to, že každé předplatné získá kopii každé zprávy odeslané do tématu, které povoluje vzor všesměrového vysílání.

Filtry a akce umožňují dvě další skupiny vzorů: vytváření oddílů a směrování.

Vytváření oddílů používá filtry k distribuci zpráv v několika stávajících předplatných tématu, a to předvídatelným a vzájemně se vylučujícím způsobem. Vzor dělení se používá, když je systém rozložen na kapacitu pro zpracování mnoha různých kontextů v funkčně identických oddílech, které každý drží podmnožinu celkových dat; například informace o profilu zákazníka. Při vytváření oddílů odesílá Vydavatel zprávu do tématu bez nutnosti znát model dělení. Zpráva se pak přesune do správného předplatného, ze kterého ji pak může načíst obslužná rutina zprávy oddílu.

Směrování používá filtry k distribuci zpráv mezi předplatnými tématu, ale ne nutně exkluzivní. Ve spojení s funkcí [automatického předávání](service-bus-auto-forwarding.md) je možné pomocí filtrů v rámci oboru názvů Service Bus v rámci oblasti Azure vytvořit komplexní grafy směrování v rámci oboru názvů pro distribuci zpráv. Když Azure Functions nebo Azure Logic Apps funguje jako most mezi Azure Service Bus obory názvů, můžete vytvořit komplexní globální topologie s přímou integrací do obchodních aplikací.

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
sys.Label LIKE '%bus%' OR user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Použití IN a NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ukázku v jazyce C# s použitím těchto filtrů najdete v tématu [ukázky filtrů témat na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).

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


> [!NOTE]
> Vzhledem k tomu, že Azure Portal nyní podporuje funkce Průzkumníka Service Bus, je možné vytvořit nebo upravit filtry předplatného na portálu. 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující ukázky: 

- [Kurz pro odesílání a přijímání v rámci .NET Basic s filtry](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – filtry témat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Šablona Azure Resource Manageru](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)