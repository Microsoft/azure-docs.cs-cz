---
title: 'Rychlý Start: jak používat Service Bus témata (Ruby)'
description: 'Rychlý Start: Naučte se používat Service Bus témata a předplatná v Azure. Ukázky kódu jsou napsané pro aplikace Ruby.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88067558"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Rychlý Start: jak používat Service Bus témata a předplatná s Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat Service Bus témata a odběry z aplikací Ruby. Mezi zahrnuté scénáře patří:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv do tématu 
- Příjem zpráv z předplatného
- Odstraňování témat a předplatných


## <a name="prerequisites"></a>Předpoklady
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v [rychlém startu: použijte Azure Portal k vytvoření Service Bus tématu a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**. 

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** k tématu pomocí **Ruby** . 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Vytvoření tématu
Objekt **Azure:: ServiceBusService** vám umožní pracovat s tématy. Následující kód vytvoří objekt **Azure:: ServiceBusService** . Chcete-li vytvořit téma, použijte `create_topic()` metodu. Následující příklad vytvoří téma nebo vytiskne všechny chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Můžete také předat objekt **Azure:: ServiceBus:: téma** s dalšími možnostmi, které vám umožní přepsat výchozí nastavení tématu, jako je například doba zprávy na hodnotu Live nebo maximální velikost fronty. Následující příklad ukazuje nastavení maximální velikosti fronty na 5 GB a dobu do živého na 1 minutu:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Vytvoření předplatných
Odběry témat se také vytvářejí pomocí objektu **Azure:: ServiceBusService** . Odběry jsou pojmenovány a mohou mít volitelný filtr, který omezuje sadu zpráv dodaných do virtuální fronty odběru.

Ve výchozím nastavení jsou předplatná trvalá. Budou i nadále existovat, dokud je neodstraní ani v tématu, ke kterému jsou přidruženy. Pokud vaše aplikace obsahuje logiku pro vytvoření předplatného, měli byste nejdřív ověřit, jestli předplatné už existuje, pomocí metody getsubscription.

Odběry můžete nechat automaticky odstranit nastavením [vlastnosti AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Pokud při vytvoření nového předplatného není zadaný žádný filtr, použije se filtr **MatchAll** (výchozí). Když se použije filtr **MatchAll** , všechny zprávy publikované v tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem All-Messages a použije výchozí filtr **MatchAll** .

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také definovat filtry, které vám umožní určit, které zprávy odeslané do tématu se mají zobrazit v rámci konkrétního předplatného.

Nejpružnější typ filtru podporovaný předplatnými je **Azure:: ServiceBus:: SqlFilter**, který implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazech, které lze použít s filtrem SQL, najdete v syntaxi [SqlFilter](service-bus-messaging-sql-filter.md) .

Do předplatného můžete přidat filtry pomocí `create_rule()` metody objektu **Azure:: ServiceBusService** . Tato metoda umožňuje přidat nové filtry do stávajícího předplatného.

Vzhledem k tomu, že výchozí filtr je automaticky použit pro všechna nová předplatná, musíte nejprve odebrat výchozí filtr, jinak **MatchAll** přepíše všechny další filtry, které můžete zadat. Výchozí pravidlo můžete odebrat pomocí `delete_rule()` metody v objektu **Azure:: ServiceBusService** .

Následující příklad vytvoří předplatné s názvem "vysoké zprávy" pomocí příkazu **Azure:: ServiceBus:: SqlFilter** , který vybere pouze zprávy, které mají vlastní `message_number` vlastnost větší než 3:

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

Podobně následující příklad vytvoří odběr s názvem `low-messages` s parametrem **Azure:: ServiceBus:: SqlFilter** , který vybere pouze zprávy, které mají `message_number` vlastnost menší nebo rovnou 3:

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

Když se do zprávy pošle zpráva `test-topic` , je vždycky Doručená příjemcům, kteří se přihlásili k odběru `all-messages` tématu, a selektivně doručovat příjemcům v `high-messages` `low-messages` předplatných a předplatných témat a (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Chcete-li odeslat zprávu do Service Bus téma, musí vaše aplikace používat `send_topic_message()` metodu v objektu **Azure:: ServiceBusService** . Zprávy odeslané do Service Bus témata jsou instancemi objektů **Azure:: ServiceBus:: BrokeredMessage** . Objekty **Azure:: ServiceBus:: BrokeredMessage** mají sadu standardních vlastností (například `label` a `time_to_live` ), slovník, který slouží k uložení vlastních vlastností specifických pro aplikaci, a tělo řetězcových dat. Aplikace může tělo zprávy nastavit tak, že do metody předáte řetězcovou hodnotu `send_topic_message()` a všechny požadované standardní vlastnosti se naplní výchozími hodnotami.

Následující příklad ukazuje, jak odeslat pět testovacích zpráv do `test-topic` . `message_number`Hodnota vlastní vlastnosti každé zprávy se liší v iteraci smyčky (určuje, které předplatné ho obdrží):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
Zprávy z předplatného jsou přijímány pomocí `receive_subscription_message()` metody v objektu **Azure:: ServiceBusService** . Ve výchozím nastavení se zprávy čtou (ve špičce) a zamčené bez jejich odstranění z předplatného. Zprávu z předplatného můžete číst a odstranit nastavením `peek_lock` Možnosti na **hodnotu NEPRAVDA**.

Výchozí chování usnadňuje čtení a odstranění dvou fází operace, což také umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu Receive voláním `delete_subscription_message()` metody a zadáním zprávy, která má být odstraněna jako parametr. `delete_subscription_message()`Metoda označí zprávu jako spotřebou a odebere ji z předplatného.

Pokud `:peek_lock` je parametr nastaven na **hodnotu false**, bude čtení a odstranění zprávy nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy, když dojde k selhání. Vezměte v úvahu scénář, ve kterém příjemce vydá žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, pak když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu, která byla spotřebována před selháním.

Následující příklad ukazuje, jak lze přijímat a zpracovávat zprávy pomocí `receive_subscription_message()` . Příklad nejprve přijme a odstraní zprávu z `low-messages` předplatného pomocí `:peek_lock` hodnoty nastavit na **hodnotu false**, poté obdrží jinou zprávu z `high-messages` a pak zprávu odstraní pomocí `delete_subscription_message()` :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může zavolat `unlock_subscription_message()` metodu v objektu **Azure:: ServiceBusService** . Způsobuje Service Bus odemčení zprávy v rámci předplatného a její zpřístupnění pro opětovné přijetí, a to buď pomocí stejné aplikace, nebo jiné náročné aplikace.

Je také časový limit přidružený ke zprávě uzamčený v rámci předplatného a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus automaticky odemkne zprávu a zpřístupní ji, aby byla k dispozici.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před `delete_subscription_message()` zavoláním metody, bude zpráva doručena do aplikace při restartu. Často se nazývá *alespoň po zpracování*; To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Tato logika se často dosahuje pomocí `message_id` vlastnosti zprávy, která zůstává konstantní při pokusůch o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé, pokud není nastavena [vlastnost AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) . Je možné je odstranit pomocí [Azure Portal][Azure portal] nebo programově. Následující příklad ukazuje, jak odstranit téma s názvem `test-topic` .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód demonstruje, jak odstranit předplatné s názvem `high-messages` z `test-topic` tématu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy Service Bus témata, získáte další informace na následujících odkazech.

* Viz [fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Reference pro API pro [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Navštivte úložiště [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) na GitHubu.

[Azure portal]: https://portal.azure.com