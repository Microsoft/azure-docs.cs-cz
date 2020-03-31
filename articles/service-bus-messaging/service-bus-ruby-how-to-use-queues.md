---
title: Jak používat fronty Azure Service Bus s Ruby
description: V tomto kurzu se dozvíte, jak vytvořit ruby aplikace pro odesílání zpráv a přijímat zprávy z fronty service bus.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760585"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Úvodní příručka: Jak používat fronty service bus s Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se dozvíte, jak vytvořit ruby aplikace pro odesílání zpráv a přijímat zprávy z fronty service bus. Ukázky jsou napsány v Ruby a použít drahokam Azure.

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v [článku Vytvoření fronty služby Service Bus pomocí webu Azure.](service-bus-quickstart-portal.md)
    1. Přečtěte si stručný **přehled** **front služby**Service Bus . 
    2. Vytvořte **obor názvů**service bus . 
    3. Získejte **připojovací řetězec**. 

        > [!NOTE]
        > Frontu v **queue** oboru názvů service bus vytvoříte pomocí ruby v tomto kurzu. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak vytvořit frontu
Objekt **Azure::ServiceBusService** umožňuje pracovat s frontami. Chcete-li vytvořit frontu, použijte metodu. `create_queue()` Následující příklad vytvoří frontu nebo vytiskne všechny chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Můžete také předat **Objekt Azure::ServiceBus::Queue** s dalšími možnostmi, které umožňují přepsat výchozí nastavení fronty, jako je například čas zprávy na živé nebo maximální velikost fronty. Následující příklad ukazuje, jak nastavit maximální velikost fronty na 5 GB a čas žít na 1 minutu:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Jak odesílat zprávy do fronty
Chcete-li odeslat zprávu do fronty service `send_queue_message()` bus, vaše aplikace volá metodu na **Azure::ServiceBusService** objektu. Zprávy odeslané (a přijaté z) fronty service bus jsou **Azure::ServiceBus::BrokeredMessage** objekty `label` a `time_to_live`mají sadu standardních vlastností (například a ), slovník, který se používá k uložení vlastní vlastnosti specifické pro aplikaci a tělo libovolných dat aplikace. Aplikace můžete nastavit text zprávy předáním řetězcové hodnoty jako zpráva a všechny požadované standardní vlastnosti jsou naplněny výchozí hodnoty.

Následující příklad ukazuje, jak odeslat testovací zprávu `test-queue` `send_queue_message()`do fronty s názvem pomocí :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak přijmout zprávy z fronty
Zprávy jsou přijímány z `receive_queue_message()` fronty pomocí metody na Objekt **Azure::ServiceBusService.** Ve výchozím nastavení jsou zprávy čteny a uzamčeny, aniž by byly odstraněny z fronty. Zprávy z fronty však můžete odstranit při jejich `:peek_lock` čtení nastavením možnosti **false**.

Výchozí chování umožňuje čtení a odstranění dvoustupňové operace, což také umožňuje podporovat aplikace, které nemohou tolerovat chybějící zprávy. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Po dokončení aplikace zpracování zprávy (nebo ukládá spolehlivě pro budoucí zpracování), dokončí druhou fázi `delete_queue_message()` procesu příjmu voláním metody a poskytnutím zprávy, která má být odstraněna jako parametr. Metoda `delete_queue_message()` označí zprávu jako spotřebovanou a odebere ji z fronty.

Pokud `:peek_lock` je parametr nastaven na **hodnotu false**, čtení a odstranění zprávy se stane nejjednodušším modelem a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat nezpracování zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že service bus označil zprávu jako spotřebované, když se aplikace restartuje a začne znovu spotřebovávat zprávy, bude mít zmeškané zprávy, která byla spotřebována před selháním.

Následující příklad ukazuje, jak přijímat a `receive_queue_message()`zpracovávat zprávy pomocí . Příklad nejprve obdrží a odstraní zprávu `:peek_lock` pomocí nastavena na **hodnotu false**, pak `delete_queue_message()`obdrží jinou zprávu a potom odstraní zprávu pomocí :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce není z nějakého důvodu schopna zprávu `unlock_queue_message()` zpracovat, může volat metodu na objektu **Azure::ServiceBusService.** Toto volání způsobí, že service bus odemknout zprávu ve frontě a zpřístupnit pro přijetí znovu, a to buď stejnou náročnou aplikací nebo jinou náročnou aplikací.

K dispozici je také časový limit přidružený ke zprávě zamčené ve frontě a pokud aplikace nezpracuje zprávu před vypršením časového limitu uzamčení (například pokud dojde k chybě aplikace), pak service bus automaticky odemkne zprávu a provede ji k dispozici k tomu, aby byly znovu přijaty.

V případě, že aplikace dojde k chybě `delete_queue_message()` po zpracování zprávy, ale před je volána metoda, pak zpráva je znovu doručena do aplikace při restartování. Tento proces se často nazývá *alespoň jednou zpracování*; to znamená, že každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Toho je často `message_id` dosaženo pomocí vlastnost zprávy, která zůstává konstantní napříč pokusy o doručení.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Naučili jste se základy front Service Bus, další informace se dozvíte na následujících odkazech.

* Přehled [front, témat a odběrů](service-bus-queues-topics-subscriptions.md).
* Navštivte [azure sdk pro ruby](https://github.com/Azure/azure-sdk-for-ruby) úložiště na GitHubu.

Porovnání mezi frontami Služby Azure service bus popsanými v tomto článku a frontami Azure popisovanými v článku [Jak používat úložiště front z Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) najdete v článku Fronty Azure a Fronty [služby Azure – porovnání a kontrastní](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

