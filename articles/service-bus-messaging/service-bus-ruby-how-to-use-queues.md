---
title: Použití Azure Service Bus front s Ruby
description: V tomto kurzu se naučíte vytvářet aplikace v Ruby pro posílání zpráv a příjem zpráv z Service Bus fronty.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336654"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Rychlý Start: jak používat Service Bus fronty s Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se naučíte vytvářet aplikace v Ruby pro posílání zpráv a příjem zpráv z Service Bus fronty. Ukázky jsou napsané v Ruby a využívají Azure gem.

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v části [použití Azure Portal k vytvoření článku Service Bus Queue](service-bus-quickstart-portal.md) .
    1. Přečtěte si rychlý **přehled** Service Busch **front**. 
    2. Vytvořte **obor názvů** Service Bus. 
    3. Získá **připojovací řetězec**. 

        > [!NOTE]
        > V tomto kurzu vytvoříte v oboru názvů Service Bus **frontu** pomocí Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak vytvořit frontu
Objekt **Azure:: ServiceBusService** vám umožní pracovat s frontami. Chcete-li vytvořit frontu, použijte `create_queue()` metodu. Následující příklad vytvoří frontu nebo vypíše všechny chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Můžete také předat objekt **Azure:: ServiceBus:: Queue** s dalšími možnostmi, které vám umožní přepsat výchozí nastavení fronty, jako je například čas zprávy na hodnotu Live nebo maximální velikost fronty. Následující příklad ukazuje, jak nastavit maximální velikost fronty na 5 GB a dobu do živého na 1 minutu:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Postup odesílání zpráv do fronty
Chcete-li odeslat zprávu do fronty Service Bus, aplikace zavolá `send_queue_message()` metodu v objektu **Azure:: ServiceBusService** . Zprávy odeslané do (a přijaté z) Service Bus fronty jsou objekty **Azure:: ServiceBus:: BrokeredMessage** a mají sadu standardních vlastností (například `label` a `time_to_live` ), slovník, který slouží k uložení vlastních vlastností specifických pro aplikaci, a tělo libovolných dat aplikace. Aplikace může tělo zprávy nastavit tak, že jako zprávu předáte řetězcovou hodnotu a všechny požadované standardní vlastnosti se naplní výchozími hodnotami.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `test-queue` pomocí `send_queue_message()` :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak přijmout zprávy z fronty
Zprávy jsou přijímány z fronty pomocí `receive_queue_message()` metody v objektu **Azure:: ServiceBusService** . Ve výchozím nastavení jsou zprávy přečtené a uzamčené bez odstranění z fronty. Můžete však odstranit zprávy z fronty, protože jsou čteny nastavením `:peek_lock` Možnosti na **hodnotu NEPRAVDA**.

Výchozí chování usnadňuje čtení a odstranění dvou fází operace, což také umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu Receive voláním `delete_queue_message()` metody a zadáním zprávy, která má být odstraněna jako parametr. `delete_queue_message()`Metoda označí zprávu jako spotřebou a odebere ji z fronty.

Pokud `:peek_lock` je parametr nastaven na **hodnotu false**, bude čtení a odstranění zprávy nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechá zprávu, která byla spotřebována před selháním.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávy pomocí `receive_queue_message()` . Tento příklad nejprve přijme a odstraní zprávu pomocí `:peek_lock` hodnoty nastavte na **false**, poté obdrží jinou zprávu a pak zprávu odstraní pomocí `delete_queue_message()` :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může zavolat `unlock_queue_message()` metodu v objektu **Azure:: ServiceBusService** . Toto volání způsobí, že Service Bus odemknout zprávu v rámci fronty a zpřístupní ji tak, aby byla znovu přijata, a to buď stejnou spotřebou aplikace, nebo jinou aplikací.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus automaticky odemkne zprávu a zpřístupní ji, aby byla k dispozici.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před `delete_queue_message()` zavoláním metody, bude zpráva doručena do aplikace při restartu. Tento proces se často nazývá *alespoň po zpracování*; To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často dosahuje pomocí `message_id` vlastnosti zprávy, která zůstává konstantní při pokusůch o doručení.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Naučili jste se základy front Service Bus, další informace se dozvíte na následujících odkazech.

* Přehled [front, témat a odběrů](service-bus-queues-topics-subscriptions.md).
* Navštivte úložiště [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) na GitHubu.

Srovnání mezi Azure Service Bus frontami popsanými v tomto článku a frontami Azure, které jsou popsány v článku [Jak používat úložiště Queue z Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) , najdete v tématu [fronty Azure a Azure Service Bus fronty – porovnání a kontrast](service-bus-azure-and-service-bus-queues-compared-contrasted.md) .

