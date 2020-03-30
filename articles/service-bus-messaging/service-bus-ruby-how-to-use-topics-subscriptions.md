---
title: 'Úvodní příručka: Jak používat témata služby Service Bus (Ruby)'
description: 'Úvodní příručka: Naučte se používat témata a předplatná service busu v Azure. Ukázky kódu jsou napsány pro aplikace Ruby.'
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718932"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Úvodní příručka: Jak používat témata a předplatná služby Service Bus s ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata service bus a předplatná z aplikací Ruby. Zahrnuté scénáře zahrnují:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv k tématu 
- Příjem zpráv z předplatného
- Odstranění témat a předplatných


## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody pro předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo se zaregistrovat k [bezplatnému účtu](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků na [úvodním panelu: Pomocí portálu Azure vytvořte téma služby Service Bus a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření **oboru názvů** Service Bus a získání **připojovacího řetězce**. 

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** tohoto tématu pomocí **ruby.** 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Vytvoření tématu
Objekt **Azure::ServiceBusService** umožňuje pracovat s tématy. Následující kód vytvoří objekt **Azure::ServiceBusService.** Chcete-li vytvořit téma, použijte metodu. `create_topic()` Následující příklad vytvoří téma nebo vytiskne všechny chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Můžete také předat **Objekt Azure::ServiceBus::Topic** s dalšími možnostmi, které umožňují přepsat výchozí nastavení tématu, jako je například čas zprávy na aktivní nebo maximální velikost fronty. Následující příklad ukazuje nastavení maximální velikosti fronty na 5 GB a dobu života na 1 minutu:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Vytvoření předplatných
Předplatná tématu se také vytvářejí pomocí objektu **Azure::ServiceBusService.** Odběry jsou pojmenovány a může mít volitelný filtr, který omezuje sadu zpráv doručované do virtuální fronty předplatného.

Ve výchozím nastavení jsou odběry trvalé. Nadále existují, dokud nebudou odstraněny buď oni, nebo téma, které jsou spojeny s. Pokud vaše aplikace obsahuje logiku pro vytvoření předplatného, měla by nejprve zkontrolovat, zda předplatné již existuje pomocí metody getSubscription.

Odběry můžete automaticky odstranit nastavením [vlastnosti AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).
Pokud není při vytvoření nového předplatného zadán žádný filtr, použije se filtr **MatchAll** (výchozí). Při použití filtru **MatchAll** jsou všechny zprávy publikované do tématu umístěny do virtuální fronty předplatného. Následující příklad vytvoří odběr s názvem "všechny zprávy" a používá výchozí **MatchAll** filtr.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry
Můžete také definovat filtry, které umožňují určit, které zprávy odeslané na téma by se měly zobrazit v rámci konkrétního předplatného.

Nejflexibilnější typ filtru podporované odběry je **Azure::ServiceBus::SqlFilter**, který implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další podrobnosti o výrazech, které lze použít s filtrem SQL, zkontrolujte syntaxi [SqlFilter.](service-bus-messaging-sql-filter.md)

Filtry můžete přidat k předplatnému pomocí `create_rule()` metody **objektu Azure::ServiceBusService.** Tato metoda umožňuje přidat nové filtry do existujícího předplatného.

Vzhledem k tomu, že výchozí filtr je použit automaticky pro všechny nové odběry, musíte nejprve odebrat výchozí filtr nebo **MatchAll** přepíše všechny ostatní filtry, které zadáte. Výchozí pravidlo můžete odebrat `delete_rule()` pomocí metody na objektu **Azure::ServiceBusService.**

Následující příklad vytvoří předplatné s názvem "high-messages" s **Azure::ServiceBus::SqlFilter,** který `message_number` vybere pouze zprávy, které mají vlastní vlastnost větší než 3:

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

Podobně následující příklad vytvoří předplatné `low-messages` s názvem **Azure::ServiceBus::SqlFilter,** který vybere `message_number` pouze zprávy, které mají vlastnost menší nebo rovnou 3:

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

Když je zpráva nyní `test-topic`odeslána , je vždy doručena `all-messages` příjemcům, kteří se přihlásili k odběru `high-messages` `low-messages` tématu, a selektivně doručena příjemcům, kteří se přihlásili k odběru a téma (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
Chcete-li odeslat zprávu do tématu service `send_topic_message()` bus, vaše aplikace musí použít metodu na **Azure::ServiceBusService** objektu. Zprávy odeslané do témat service bus jsou instance **Azure::ServiceBus::BrokeredMessage** objekty. **Azure::ServiceBus::BrokeredMessage** objekty mají sadu standardních `label` `time_to_live`vlastností (například a ), slovník, který se používá k uložení vlastní vlastnosti specifické pro aplikaci a tělo dat řetězce. Aplikace může nastavit text zprávy předáním hodnoty řetězce `send_topic_message()` metodě a všechny požadované standardní vlastnosti jsou naplněny výchozími hodnotami.

Následující příklad ukazuje, jak odeslat `test-topic`pět testovacích zpráv do aplikace . Hodnota `message_number` vlastní vlastnosti každé zprávy se liší podle iterace smyčky (určuje, které předplatné ji obdrží):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z předplatného
Zprávy jsou přijímány z `receive_subscription_message()` předplatného pomocí metody na **Azure::ServiceBusService** objektu. Ve výchozím nastavení jsou zprávy read(peak) a uzamčeny bez odstranění z předplatného. Zprávu z odběru můžete přečíst a `peek_lock` odstranit nastavením možnosti **false**.

Výchozí chování umožňuje čtení a odstranění dvoustupňové operace, což také umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi `delete_subscription_message()` procesu příjmu voláním metody a poskytnutím zprávy, která má být odstraněna jako parametr. Metoda `delete_subscription_message()` označí zprávu jako spotřebované a odebrat z předplatného.

Pokud `:peek_lock` je parametr nastaven na **hodnotu false**, čtení a odstranění zprávy se stane nejjednodušším modelem a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat nezpracování zprávy, když dojde k chybě. Zvažte scénář, ve kterém spotřebitel vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Vzhledem k tomu, že service bus označil zprávu jako spotřebované, pak při restartování aplikace a začne spotřebovávat zprávy znovu, má zmeškané zprávy, která byla spotřebována před selhání.

Následující příklad ukazuje, jak mohou být zprávy `receive_subscription_message()`přijímány a zpracovávány pomocí . Příklad nejprve obdrží a odstraní zprávu `low-messages` z `:peek_lock` odběru pomocí nastavena na **hodnotu** `high-messages` false , pak obdrží `delete_subscription_message()`další zprávu z a potom odstraní zprávu pomocí :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není z nějakého důvodu schopna zprávu `unlock_subscription_message()` zpracovat, může volat metodu na objektu **Azure::ServiceBusService.** Způsobí, že Service Bus odemknout zprávu v rámci předplatného a zpřístupnit ji k přijetí znovu, a to buď stejnou náročnou aplikací nebo jinou spotřebitelská aplikace.

K dispozici je také časový limit přidružený ke zprávě zamčené v rámci předplatného a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak Service Bus automaticky odemkne zprávu a k dispozici k jeho přijetí.

V případě, že aplikace dojde k chybě `delete_subscription_message()` po zpracování zprávy, ale před je volána metoda, pak zpráva je znovu doručena do aplikace při restartování. To je často nazýváno *alespoň jednou zpracování*; to znamená, že každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Tato logika je `message_id` často dosaženo pomocí vlastnost zprávy, která zůstává konstantní napříč pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů
Témata a odběry jsou trvalé, pokud [je nastavena vlastnost AutoDeleteOnIdle.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) Mohou být odstraněny buď prostřednictvím [portálu Azure][Azure portal] nebo programově. Následující příklad ukazuje, jak odstranit `test-topic`téma s názvem .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit `high-messages` odběr `test-topic` pojmenovaný z tématu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili základy témat service bus, postupujte podle těchto odkazů se dozvíte více.

* Viz [Fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Reference pro API pro [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Navštivte [azure sdk pro ruby](https://github.com/Azure/azure-sdk-for-ruby) úložiště na GitHubu.

[Azure portal]: https://portal.azure.com
