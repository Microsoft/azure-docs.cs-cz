---
title: Jak používat fronty služby Azure Service Bus pomocí Pythonu | Dokumentace Microsoftu
description: Zjistěte, jak používat fronty Azure Service Bus z Pythonu.
services: service-bus-messaging
documentationcenter: python
author: spelluru
manager: timlt
editor: ''
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 08/30/2018
ms.author: spelluru
ms.openlocfilehash: cc8fc62811fe019dcaf613a1c0b50d89183e6555
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406188"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Jak používat fronty služby Service Bus pomocí Pythonu

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento článek popisuje, jak používat fronty Service Bus. Ukázky jsou napsané v Pythonu a použití [balíček Python Azure Service Bus][Python Azure Service Bus package]. Mezi popsané scénáře patří **vytváření fronty, odesílání a přijímání zpráv**, a **odstranění front**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> K instalaci Pythonu nebo [balíček Python Azure Service Bus][Python Azure Service Bus package], najdete v článku [Průvodce instalací Pythonu](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Vytvoření fronty
**ServiceBusService** objektu umožňuje pracovat s frontami. Přidejte následující kód do horní jakéhokoli souboru Python, ve kterém chcete programovému přístupu ke službě Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

Následující kód vytvoří **ServiceBusService** objektu. Nahraďte `mynamespace`, `sharedaccesskeyname`, a `sharedaccesskey` s oborem názvů, název klíče sdíleného přístupového podpisu (SAS) a hodnotu.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Hodnoty pro název klíče SAS a hodnoty najdete v [webu Azure portal] [ Azure portal] informace o připojení, nebo v sadě Visual Studio **vlastnosti** podokna při výběru služby Obor názvů Service Bus v Průzkumníku serveru (jak je znázorněno v předchozí části).

```python
bus_service.create_queue('taskqueue')
```

`create_queue` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení fronta jako je například time to live (TTL) nebo maximální velikost fronty zprávy. Následující příklad nastaví maximální velikost fronty až 5 GB a hodnota TTL na 1 minutu:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Odeslat zprávu do fronty služby Service Bus, vaše aplikace volání `send_queue_message` metodu **ServiceBusService** objektu.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `taskqueue` pomocí `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [kvótách služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Přijme zprávy z fronty pomocí `receive_queue_message` metodu **ServiceBusService** objektu:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Zprávy jsou z fronty odstranit, protože jsou načteny při parametr `peek_lock` je nastavena na **False**. Může číst (Náhled) a uzamčení zprávy bez odstranění z fronty tak, že nastavíte parametr `peek_lock` k **True**.

Chování pro čtení a odstranění zprávy jako součást operace receive je nejjednodušší model a funguje nejlépe v situacích, ve kterých aplikace může tolerovat možnost, zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, ji budou pravděpodobně nenalezlo zprávu, která se spotřebovala před pádem vynechá.

Pokud `peek_lock` parametr je nastaven na **True**, receive stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním **odstranit** metodu **zpráva** objekt. **Odstranit** – metoda se označí zprávu jako spotřebovávanou a jeho odebrání z fronty.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat **odemknout** metodu **zpráva** objektu. To způsobí, že služba Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak se Service Bus zprávu automaticky odemkne a nastavte ji k dispozici pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **odstranit** metoda je volána, pak bude doručit víckrát do aplikace při restartování. To se často nazývá **zpracování nejméně jednou**, to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **MessageId** vlastnosti zprávy, která zůstanou konstantní pokusu o doručení.

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy front Service Bus, najdete v těchto článcích se dozvíte víc.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

