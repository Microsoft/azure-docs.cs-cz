---
title: Jak používat fronty služby Azure Service Bus pomocí Ruby | Dokumentace Microsoftu
description: Naučte se používat fronty Service Bus v Azure. Ukázky kódu napsané v Ruby.
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
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 48f60b7c07cc16b4d9994d5644069fdcb4881e0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991878"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Jak používat fronty služby Service Bus pomocí Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se dozvíte, jak vytvářet aplikace Ruby na odesílání a příjem zpráv z fronty Service Bus. Ukázky jsou napsané v Ruby a používají Azure gem.

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete si aktivovat váš [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v [použijte Azure portal můžete vytvořit frontu služby Service Bus](service-bus-quickstart-portal.md) článku.
    1. Přečtěte si rychlé **přehled** služby Service Bus **fronty**. 
    2. Vytvoření služby Service Bus **obor názvů**. 
    3. Získejte **připojovací řetězec**. 

        > [!NOTE]
        > Vytvoříte **fronty** v oboru názvů služby Service Bus s použitím prostředí Ruby v tomto kurzu. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak vytvořit frontu
**Azure::ServiceBusService** objektu umožňuje pracovat s frontami. Chcete-li vytvořit frontu, použijte `create_queue()` metody. Následující příklad vytvoří frontu nebo vytiskne všechny chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Můžete také předat **Azure::ServiceBus::Queue** objekt s další možnosti, které můžete přepsat výchozí nastavení fronty, jako je například čas zprávy do fronty za provozu nebo maximální velikost. Následující příklad ukazuje, jak nastavit maximální velikost fronty 5 GB a čas TTL na 1 minutu:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Postup odesílání zpráv do fronty
Odeslat zprávu do fronty služby Service Bus, vaše aplikace volání `send_queue_message()` metodu **Azure::ServiceBusService** objektu. Zprávy odeslané do (a přijatých z) služby Service Bus jsou fronty **Azure::ServiceBus::BrokeredMessage** objektů a mají sadu standardních vlastností (jako například `label` a `time_to_live`), slovník, který se používá k ukládání vlastní vlastnosti specifické pro aplikace a tělo libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit předáním řetězce hodnoty jako zpráva a jakékoliv nezbytné vlastnosti standardní se naplní výchozími hodnotami.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `test-queue` pomocí `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak přijmout zprávy z fronty
Přijme zprávy z fronty pomocí `receive_queue_message()` metodu **Azure::ServiceBusService** objektu. Zprávy jsou ve výchozím nastavení, přečtěte si a uzamčen bez odstranění z fronty. Však můžete odstranit zprávy z fronty jsou načteny tak, že nastavíte `:peek_lock` umožňuje **false**.

Výchozí chování umožňuje čtení a odstranění dvoufázová operaci, která také umožňuje podporu aplikací, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním `delete_queue_message()` metoda a poskytující zprávu, která se má odstranit jako parametr. `delete_queue_message()` – Metoda se označí zprávu jako spotřebovávanou a jeho odebrání z fronty.

Pokud `:peek_lock` parametr je nastaven na **false**, čtení a odstranění stane nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus už ale zprávu jako spotřebovávanou, když se aplikace restartuje a začne znovu přijímat zprávy označila, ho neuskutečnily zprávu, která se spotřebovala před pádem vynechá.

Následující příklad ukazuje, jak pro příjem a zpracování zpráv s použitím `receive_queue_message()`. V příkladu nejdřív přijme a odstraní zprávu s použitím `:peek_lock` nastavena na **false**, pak další zprávu přijme a odstraní zprávu pomocí `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlock_queue_message()` metodu **Azure::ServiceBusService** objektu. Toto volání způsobí, že služba Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), Service Bus zprávu automaticky odemkne a díky tomu k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete_queue_message()` metoda je volána, pak je víckrát do aplikace při restartování. Tento proces se často nazývá *zpracování nejméně jednou*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá `message_id` vlastnosti zprávy, která zůstává konstantní pokusu o doručení.

> [!NOTE]
> Můžete spravovat prostředky služby Service Bus s [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Exploreru umožňuje uživatelům připojit k oboru názvů služby Service Bus a správě entit pro zasílání zpráv snadno způsobem. Tento nástroj nabízí pokročilé funkce, například funkce importu/exportu nebo možnost otestovat tématu, fronty, předplatná, služby pro přenos přes, notification hubs a centra událostí. 

## <a name="next-steps"></a>Další postup
Naučili jste se základy front Service Bus, další informace se dozvíte na následujících odkazech.

* Přehled [fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Přejděte [sady Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) úložišti na Githubu.

Pro srovnání front Azure Service Bus, které jsou popsané v tomto článku a fronty služby Azure popsané v [postupy používání úložiště Queue z Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) článek, naleznete v tématu [front Azure a fronty Azure Service Bus – porovnání a Rozdíly](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

