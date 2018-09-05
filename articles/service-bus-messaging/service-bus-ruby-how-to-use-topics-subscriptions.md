---
title: Jak používat témata služby Service Bus (Ruby) | Dokumentace Microsoftu
description: Zjistěte, jak používat témata a odběry Service Bus v Azure. Ukázky kódu jsou napsané pro aplikace Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: spelluru
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 7370de72c0015314fb083b6705d5275f0acc4fc4
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698195"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Jak používat témata a odběry Service Bus pomocí Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata služby Service Bus a odběrů z aplikací v Ruby. Mezi popsané scénáře patří **vytváření témat a odběrů, vytváření filtrů odběrů, odesílání zpráv** do tématu, **příjem zpráv z odběru**, a **odstranění témata a odběry**. Další informace o témata a odběry, najdete v článku [další kroky](#next-steps) oddílu.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Vytvoření tématu
**Azure::ServiceBusService** objektu umožňuje pracovat s tématy. Následující kód vytvoří **Azure::ServiceBusService** objektu. Pokud chcete vytvořit téma, použijte `create_topic()` metody. Následující příklad vytvoří téma nebo vytiskne chyby, pokud tam nějaké jsou.

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

## <a name="create-subscriptions"></a>Vytvořit odběry
Odběry témat taky jsou vytvořeny pomocí **Azure::ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, který omezuje sadu doručování zpráv do virtuální fronty odběru.

Předplatná jsou trvalé a bude pokračovat až do obou nebo téma, které jsou přidružené, se odstraní. Pokud vaše aplikace obsahuje logiku pro vytvoření odběru, měli by nejdřív zkontrolovat, pokud předplatné už existuje. pomocí metody metody getsubscription technologie.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Filtr **MatchAll** je výchozí filtr, který se použije v případě, že při vytváření nového odběru nezadáte žádný filtr. Když **MatchAll** se používá filtr, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem "všechny zprávy" a používá výchozí **MatchAll** filtru.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také Definujte filtry, které vám umožní určit, které zprávy odeslané do tématu se měla zobrazit v rámci konkrétního předplatného.

Nejflexibilnější filtr předplatných je **Azure::ServiceBus::SqlFilter**, která implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o výrazech, které lze použít s filtrem SQL, přečtěte si [SqlFilter](service-bus-messaging-sql-filter.md) syntaxe.

Můžete přidat filtry na předplatné s použitím `create_rule()` metodu **Azure::ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

Protože výchozí filtr automaticky platí pro všechna nová předplatná, musíte nejdřív odebrat výchozí filtr, nebo **MatchAll** přepíše ostatní filtry, můžete zadat. Výchozí pravidla můžete odebrat pomocí `delete_rule()` metodu **Azure::ServiceBusService** objektu.

Následující příklad vytvoří odběr s názvem "vysoce zprávy" s **Azure::ServiceBus::SqlFilter** , který vybere jen zprávy, které mají vlastní `message_number` vlastnost větší než 3:

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

Obdobně následující příklad vytvoří odběr s názvem `low-messages` s **Azure::ServiceBus::SqlFilter** , který vybere jen zprávy, které mají `message_number` vlastnost menší než nebo rovna na 3:

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

Když je teď odeslána zpráva `test-topic`, je vždy doručena příjemcům `all-messages` odběru tématu a selektivně příjemcům `high-messages` a `low-messages` odběry tématu (v závislosti na obsah zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Odeslat zprávu do tématu služby Service Bus, vaše aplikace musí používat `send_topic_message()` metodu **Azure::ServiceBusService** objektu. Zprávy odeslané do témat Service Bus jsou instance **Azure::ServiceBus::BrokeredMessage** objekty. **Azure::ServiceBus::BrokeredMessage** objekty mají sadu standardních vlastností (jako například `label` a `time_to_live`), slovník používaný pro udržení vlastních vlastností specifické pro aplikace a tělo s daty řetězec. Aplikace může tělo zprávy nastavit předáním řetězcové hodnoty `send_topic_message()` metoda a jakékoliv nezbytné vlastnosti standardní vyplněn prostředkem výchozí hodnoty.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `test-topic`. Všimněte si, `message_number` hodnotu vlastní vlastnosti každé zprávy se liší v iteraci smyčky (to určuje, jaké předplatné obdrží):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru
Přijme zprávy z odběru pomocí `receive_subscription_message()` metodu **Azure::ServiceBusService** objektu. Ve výchozím nastavení zprávy jsou read(peak) a uzamčen, aniž by byl z předplatného. Může číst a odstranit zprávu z předplatného tak, že nastavíte `peek_lock` umožňuje **false**.

Výchozí chování umožňuje čtení a odstranění dvoufázová operaci, která také umožňuje podporu aplikací, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním `delete_subscription_message()` metoda a poskytující zprávu, která se má odstranit jako parametr. `delete_subscription_message()` Metoda se označí zprávu jako spotřebovávanou a odeberte z předplatného.

Pokud `:peek_lock` parametr je nastaven na **false**, čtení a odstranění stane nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

Následující příklad ukazuje, jak lze přijímat zprávy a zpracovaná pomocí `receive_subscription_message()`. V příkladu nejdřív přijme a odstraní zprávu z `low-messages` předplatné s použitím `:peek_lock` nastavena na **false**, pak obdrží jinou zprávu od `high-messages` a odstraní zprávu pomocí `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlock_subscription_message()` metodu **Azure::ServiceBusService** objektu. To způsobí, že Service Bus zprávu odemkne v odběru a zpřístupní ji pro další přijetí, buďto stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu zpráva uzamčená v odběru, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak odemkne služby Service Bus zprávu automaticky a zpřístupní ji pro přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete_subscription_message()` metoda je volána, pak je víckrát do aplikace při restartování. To se často nazývá *zpracování nejméně jednou*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Tato logika se často opírá `message_id` vlastnosti zprávy, která zůstanou konstantní pokusu o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé a musí být explicitně odstranit prostřednictvím [webu Azure portal] [ Azure portal] nebo prostřednictvím kódu programu. Následující příklad ukazuje, jak odstranit téma s názvem `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit odběr s názvem `high-messages` z `test-topic` tématu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy témat sběrnice Service Bus, použijte tyto odkazy na další informace.

* Zobrazit [fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Reference pro API pro [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Přejděte [sady Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) úložišti na Githubu.

[Azure portal]: https://portal.azure.com
