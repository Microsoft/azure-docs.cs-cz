---
title: Jak používat fronty Azure Service Bus s Pythonem | Microsoft Docs
description: Naučte se používat fronty Azure Service Bus z Pythonu.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: aa0f243f4a5bc3d84c580b950bcf0ed7a78362e7
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Jak používat fronty Service Bus s Pythonem

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento článek popisuje, jak používat fronty Service Bus. Ukázky jsou napsané v Pythonu a použití [balíček Python Azure Service Bus][Python Azure Service Bus package]. Pokryté scénáře zahrnují **vytváření front, odesílání a přijímání zpráv**, a **odstraňování front**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> K instalaci Pythonu nebo [balíček Python Azure Service Bus][Python Azure Service Bus package], najdete v článku [Průvodce instalací Python](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Vytvoření fronty
**ServiceBusService** objektu umožňuje pracovat s fronty. Přidejte následující kód v horní jakéhokoliv Python souboru, ve kterém chcete programový přístupu k Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

Následující kód vytvoří **ServiceBusService** objektu. Nahraďte `mynamespace`, `sharedaccesskeyname`, a `sharedaccesskey` se váš obor názvů, název klíče sdíleného přístupového podpisu (SAS) a hodnotou.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Hodnoty pro název klíče SAS a hodnotu lze nalézt v [portál Azure] [ Azure portal] informace o připojení, nebo v sadě Visual Studio **vlastnosti** podokně při výběru službu Obor názvů sběrnice v Průzkumníku serveru (jak je uvedeno v předchozí části).

```python
bus_service.create_queue('taskqueue')
```

`create_queue` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení fronty například čas zprávy live (TTL) nebo maximální velikost fronty. Následující příklad nastaví na 5 GB a hodnota TTL na 1 minutu maximální velikost fronty:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
K odeslání zprávy do fronty Service Bus, vaše aplikace volání `send_queue_message` metodu **ServiceBusService** objektu.

Následující příklad ukazuje, jak odeslat testovací zprávu do fronty s názvem `taskqueue` pomocí `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty
Přijímání zpráv z fronty pomocí `receive_queue_message` metodu **ServiceBusService** objektu:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Zprávy jsou odstraněny z fronty, jako při jejich přečtení parametr `peek_lock` je nastaven na **False**. Můžete číst (funkce Náhled) a uzamčení zpráva bez odstranění z fronty nastavením parametru `peek_lock` k **True**.

Chování čtení a odstranění zprávy v rámci operace příjmu je nejjednodušší model a funguje nejlépe pro scénáře, ve kterých aplikace může tolerovat selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Pokud `peek_lock` parametr je nastaven na **True**, receive stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání **odstranit** metodu **zpráva** objekt. **Odstranit** metoda bude označí zprávu jako spotřebovávanou a odebrat ji z fronty.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak můžete volat **odemknutí** metodu **zpráva** objektu. To způsobí, že Service Bus zprávu odemkne ve frontě a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu přidružené zpráva uzamčená ve frontě, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak se Service Bus zprávu automaticky odemkne a zpřístupní ji pro další přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než **odstranit** metoda je volána, pak zpráva bude vysláním do aplikace odešle znovu. To se často označuje jako **zpracování nejméně jednou**, který je každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát stejnou zprávu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá **MessageId** vlastnosti zprávy, která zůstane konstantní mezi pokusy o doručení.

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy front Service Bus, najdete v těchto článcích Další informace.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

