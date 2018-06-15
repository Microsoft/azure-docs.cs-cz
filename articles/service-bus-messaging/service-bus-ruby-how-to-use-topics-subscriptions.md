---
title: Jak používat témata Service Bus (Ruby) | Microsoft Docs
description: Naučte se používat témata a odběry Service Bus v Azure. Ukázky kódu jsou napsané pro poznámky Ruby aplikace.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926792"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Jak používat témata a odběry Service Bus s Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata Service Bus a odběry z Ruby aplikací. Pokryté scénáře zahrnují **vytváření témat a odběrů, vytváření filtrů odběrů, odesílání zpráv** do tématu, **přijímání zpráv z odběru**, a **odstranění témat a odběrů**. Další informace o témat a odběrů, najdete v článku [další kroky](#next-steps) části.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Vytvoření tématu
**Azure::ServiceBusService** objektu umožňuje pracovat s tématy. Následující kód vytvoří **Azure::ServiceBusService** objektu. Chcete-li vytvořit téma, použijte `create_topic()` metoda. Následující příklad vytvoří téma nebo vytiskne chyby, pokud jsou k dispozici.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Můžete také předat **Azure::ServiceBus::Topic** objekt s další možnosti, které vám umožní přepsat výchozí nastavení téma například čas zprávy do fronty za provozu nebo maximální velikost. Následující příklad ukazuje, nastavení maximální velikost fronty 5 GB a čas TTL na 1 minutu:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Vytvořte odběr
Odběry témat taky jsou vytvořeny pomocí **Azure::ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv doručených do virtuální fronty odběru.

Odběry jsou trvalé a bude dál existovat, dokud buď jejich nebo tématu jsou spojeny s, se odstraní. Pokud vaše aplikace obsahuje logiku pro vytvoření odběru, musí nejprve zkontrolujte Pokud předplatné už existuje. pomocí metody getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr, který se použije v případě, že při vytváření nového odběru nezadáte žádný filtr. Když **MatchAll** filtr se používá, všechny zprávy publikované do tématu jsou umístěny do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem "všechna zprávy" a používá výchozí **MatchAll** filtru.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také definovat filtry, které vám umožní určit, které by měl zprávy odeslané do tématu zobrazit do konkrétní předplatné.

Nejflexibilnější filtr, který odběry podporují je **Azure::ServiceBus::SqlFilter**, který implementuje je podmnožinou SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazy, které lze použít s filtrem SQL, projděte si [SqlFilter](service-bus-messaging-sql-filter.md) syntaxe.

Filtry k odběru můžete přidat pomocí `create_rule()` metodu **Azure::ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

Vzhledem k tomu, že výchozí filtr automaticky použije na všechny nové odběry, je nutno nejprve odstranit výchozí filtr, nebo **MatchAll** přepíše ostatní filtry, můžete určit. Výchozí pravidla můžete odebrat pomocí `delete_rule()` metodu **Azure::ServiceBusService** objektu.

Následující příklad vytvoří odběr s názvem "vysoce zprávy" se **Azure::ServiceBus::SqlFilter** který vybere jen zprávy, které mají vlastní `message_number` vlastnost větší než 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Podobně platí, tento příklad vytvoří odběr s názvem `low-messages` s **Azure::ServiceBus::SqlFilter** který vybere jen zprávy, které mají `message_number` vlastnost menší než nebo rovna na 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Když je nyní odeslána zpráva `test-topic`, je vždy doručena příjemci `all-messages` odběr tématu a selektivně příjemcům přihlásit k odběru `high-messages` a `low-messages` odběry témat (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
K odeslání zprávy do tématu Service Bus, musí vaše aplikace používat `send_topic_message()` metodu **Azure::ServiceBusService** objektu. Zprávy odeslané do témat Service Bus jsou instance **Azure::ServiceBus::BrokeredMessage** objekty. **Azure::ServiceBus::BrokeredMessage** objekty mají sadu standardních vlastností (jako například `label` a `time_to_live`), slovník používaný pro udržení vlastních vlastností specifické pro aplikaci a obsah řetězec dat. Aplikace může tělo zprávy nastavit předáním řetězcové hodnoty `send_topic_message()` metoda a všechny nezbytné standardní vlastnosti vyplní výchozí hodnoty.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `test-topic`. Všimněte si, že `message_number` hodnotu vlastní vlastnosti každé zprávy se liší na iteraci smyčky (to určuje, jaké předplatné přijetí):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Přijímání zpráv z odběru pomocí `receive_subscription_message()` metodu **Azure::ServiceBusService** objektu. Ve výchozím nastavení zprávy jsou read(peak) a uzamčení bez odstranění z předplatného. Můžete číst a odstranění zprávy z odběru nastavením `peek_lock` možnost k **false**.

Výchozí chování umožňuje čtení a odstraňování dvoufázová operaci, která také umožňuje podpora aplikací, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání `delete_subscription_message()` metoda a poskytující zprávu odstranit jako parametr. `delete_subscription_message()` Metoda bude označí zprávu jako spotřebovávanou a odebrat ji z odběru.

Pokud `:peek_lock` parametr je nastaven na **false**, čtení a odstranění zprávy stane nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Následující příklad ukazuje, jak můžete obdržet zprávy a zpracování pomocí `receive_subscription_message()`. V příkladu nejprve přijme a odstraní zprávu z `low-messages` předplatné pomocí `:peek_lock` nastavena na **false**, pak obdrží další zprávu z `high-messages` a poté se odstraní zprávu pomocí `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlock_subscription_message()` metodu **Azure::ServiceBusService** objektu. To způsobí, že Service Bus zprávu odemkne v odběru a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu zpráva uzamčená v odběru, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak odemknutím Service Bus zprávu automaticky a zpřístupní ji pro přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete_subscription_message()` metoda je volána, pak zprávy je víckrát do aplikace odešle znovu. To se často označuje jako *zpracování nejméně jednou*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát stejnou zprávu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Tato logika se často opírá `message_id` vlastnosti zprávy, která zůstane konstantní mezi pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé a musí být explicitně odstranit buď prostřednictvím [portál Azure] [ Azure portal] nebo prostřednictvím kódu programu. Následující příklad ukazuje, jak odstranit téma s názvem `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit odběr s názvem `high-messages` z `test-topic` tématu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy témat sběrnice Service Bus, postupujte podle následujících odkazech na další informace.

* V tématu [fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Reference pro API pro [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Přejděte [Azure SDK pro Ruby](https://github.com/Azure/azure-sdk-for-ruby) úložišti na Githubu.

[Azure portal]: https://portal.azure.com
