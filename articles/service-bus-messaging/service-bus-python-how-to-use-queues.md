---
title: Jak používat fronty Azure Service Bus v Pythonu | Microsoft Docs
description: Naučte se používat Azure Service Bus fronty z Pythonu.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 9bb53a8e68866e2ed346277171e2706f5907e8af
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141916"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Použití front Service Bus s Pythonem

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto kurzu se naučíte vytvářet aplikace v Pythonu pro posílání zpráv a příjem zpráv z Service Bus fronty. 

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v části [použití Azure Portal k vytvoření článku Service Bus Queue](service-bus-quickstart-portal.md) .
    1. Přečtěte si rychlý **přehled** Service Busch **front**. 
    2. Vytvořte **obor názvů**Service Bus. 
    3. Získá **připojovací řetězec**. 

        > [!NOTE]
        > V tomto kurzu vytvoříte **frontu** v oboru názvů Service Bus pomocí Pythonu. 
1. Instalace Pythonu nebo [balíčku Azure Service Bus Pythonu][Python Azure Service Bus package]najdete v [příručce pro instalaci Pythonu](/azure/python/python-sdk-azure-install). [Tady](/python/api/overview/azure/servicebus?view=azure-python)najdete úplnou dokumentaci k sadě Service Bus Python SDK.

## <a name="create-a-queue"></a>Vytvoření fronty
Objekt **ServiceBusClient** vám umožní pracovat s frontami. Do horní části každého souboru Pythonu, do kterého chcete programově přistupovat, přidejte následující kód Service Bus:

```python
from azure.servicebus import ServiceBusClient
```

Následující kód vytvoří objekt **ServiceBusClient** . Nahraďte `<CONNECTION STRING>` řetězcem ServiceBus ConnectionString.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Hodnoty pro název a hodnotu klíče SAS najdete v informacích o [Azure Portal][Azure portal] připojení, nebo v podokně **vlastnosti** sady Visual Studio při výběru Service Bus oboru názvů v Průzkumník serveru (jak je znázorněno v předchozí části).

```python
sb_client.create_queue("taskqueue")
```

`create_queue` Metoda také podporuje další možnosti, které umožňují přepsat výchozí nastavení fronty, jako je například hodnota TTL (Time to Live) nebo maximální velikost fronty. Následující příklad nastaví maximální velikost fronty na 5 GB a hodnotu TTL na 1 minutu:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Další informace najdete v [dokumentaci k pythonu Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
Chcete-li odeslat zprávu do fronty Service Bus, vaše aplikace zavolá `send` metodu `ServiceBusClient` objektu.

Následující příklad ukazuje, jak odeslat zkušební zprávu do fronty s názvem `taskqueue` pomocí: `send_queue_message`

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených ve frontě není omezený, ale celková velikost zpráv držených ve frontě omezená je. Velikost fronty se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

Další informace najdete v [dokumentaci k pythonu Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
Zprávy jsou přijímány z fronty pomocí `get_receiver` metody `ServiceBusService` objektu:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Další informace najdete v [dokumentaci k pythonu Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).


Pokud je parametr `peek_lock` nastaven na **hodnotu false**, budou zprávy z fronty odstraněny, protože jsou čteny. Můžete číst (prohlížet) a uzamknout zprávu bez jejich odstranění z fronty nastavením parametru `peek_lock` na **hodnotu true**.

Chování při čtení a odstranění zprávy jako součást operace Receive je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávání zprávy v případě selhání. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus bude označena jako spotřebovaná zpráva, pak když se aplikace znovu spustí a začne znovu spotřebovávat zprávy, vynechá se zpráva, která byla spotřebována před chybou.

Pokud je parametr nastaven na **hodnotu true**, obdrží se operace se dvěma fázemi, což umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. `peek_lock` Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu Receive voláním metody **Delete** u objektu **Message** . Metoda **Delete** označí zprávu jako spotřebou a odebere ji z fronty.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv
Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může volat metodu **Unlock** objektu **zprávy** . To způsobí, že Service Bus odemkne zprávu v rámci fronty a zpřístupní ji pro opětovné přijetí, a to buď pomocí stejné aplikace, nebo jiné náročné aplikace.

Je také časový limit přidružený ke zprávě uzamčený ve frontě a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud dojde k selhání aplikace), Service Bus bude automaticky odemknout a nastavit k dispozici pro opětovné přijetí.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před zavoláním metody **Delete** , bude zpráva doručena do aplikace při restartu. Tato situace se často nazývá **nejméně jednou při zpracování**, to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva znovu doručena. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. To se často opírá o vlastnost zprávy **MessageId**, která je při každém pokusu o doručení stejné zprávy stejná.

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy Service Busch front, najdete další informace v těchto článcích.

* [Fronty, témata a odběry][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

