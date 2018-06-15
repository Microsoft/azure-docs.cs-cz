---
title: Jak používat fronty Azure Service Bus s Ruby | Microsoft Docs
description: Naučte se používat fronty Service Bus v Azure. Ukázky kódu jsou vytvořeny v Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 357a7277dd42b6973cf35a9f642b8eec36a745e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868442"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Jak používat fronty Service Bus s Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tato příručka popisuje, jak používat fronty Service Bus. Ukázky jsou napsané v Ruby a používají Azure gem. Pokryté scénáře zahrnují **vytváření front, odesílání a přijímání zpráv**, a **odstraňování front**. Další informace o fronty Service Bus, najdete v článku [další kroky](#next-steps) části.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Postup vytvoření fronty
**Azure::ServiceBusService** objektu umožňuje pracovat s fronty. Chcete-li vytvořit frontu, použijte `create_queue()` metoda. Následující příklad vytvoří frontu nebo vytiskne případné chyby.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Můžete také předat **Azure::ServiceBus::Queue** objektu s další možnosti, které můžete přepsat výchozí nastavení fronty, jako je například čas zprávy do fronty za provozu nebo maximální velikost. Následující příklad ukazuje, jak nastavit maximální velikost fronty 5 GB a čas TTL na 1 minutu:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Odesílání zpráv do fronty
K odeslání zprávy do fronty Service Bus, vaše aplikace volání `send_queue_message()` metodu **Azure::ServiceBusService** objektu. Zprávy odeslané do (a přijaté z) jsou fronty Service Bus **Azure::ServiceBus::BrokeredMessage** objekty a mají sadu standardních vlastností (jako například `label` a `time_to_live`), slovník, který se používá k ukládání vlastní vlastnosti specifické pro aplikace a tělo s libovolnými aplikačními daty. Aplikace může tělo zprávy nastavit předáním řetězcovou hodnotu jako zprávu, a jakékoliv nezbytné vlastnosti, standardní se naplní výchozí hodnoty.

Následující příklad ukazuje, jak odeslat testovací zprávu do fronty s názvem `test-queue` pomocí `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak přijmout zprávy z fronty
Přijímání zpráv z fronty pomocí `receive_queue_message()` metodu **Azure::ServiceBusService** objektu. Zprávy jsou ve výchozím nastavení, přečtěte si a uzamčení bez odstraňuje z fronty. Však můžete odstranit zprávy z fronty se čtou nastavením `:peek_lock` možnost k **false**.

Výchozí chování umožňuje čtení a odstraňování dvoufázová operaci, která také umožňuje podpora aplikací, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání `delete_queue_message()` metoda a poskytující zprávu odstranit jako parametr. `delete_queue_message()` Metoda bude označí zprávu jako spotřebovávanou a odebrat ji z fronty.

Pokud `:peek_lock` parametr je nastaven na **false**, čtení a odstranění zprávy stane nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovávanou, když se aplikace restartuje a začne znovu přijímat zprávy, se neuskutečnily zprávu, která se spotřebovala před havárii.

Následující příklad ukazuje, jak přijímat a zpracovávat zprávy pomocí `receive_queue_message()`. V příkladu nejprve přijme a odstraní zprávu pomocí `:peek_lock` nastavena na **false**, pak přijetí další zprávu a poté se odstraní zprávu pomocí `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlock_queue_message()` metodu **Azure::ServiceBusService** objektu. Toto volání způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak Service Bus zprávu automaticky odemkne a ji zpřístupní k přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete_queue_message()` metoda je volána, pak zprávy je víckrát do aplikace odešle znovu. Tento proces se často nazývá *zpracování nejméně jednou*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát stejnou zprávu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá `message_id` vlastnosti zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="next-steps"></a>Další kroky
Naučili jste se základy front Service Bus, další informace se dozvíte na následujících odkazech.

* Přehled [fronty, témata a odběry](service-bus-queues-topics-subscriptions.md).
* Přejděte [Azure SDK pro Ruby](https://github.com/Azure/azure-sdk-for-ruby) úložišti na Githubu.

Porovnání mezi fronty Azure Service Bus, které jsou popsané v tomto článku a fronty Azure, které jsou popsané v [postup používání úložiště Queue z Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) článku najdete v tématu [fronty Azure a Azure fronty služby Service Bus - porovnání a Rozdíl od aktualizovaného](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

