---
title: 'Úvodní příručka: Použití front Azure Service Bus v Pythonu'
description: Tento článek ukazuje, jak pomocí Pythonu vytvářet, odesílat zprávy a přijímat zprávy z front Azure Service Bus.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478654"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Úvodní příručka: Použití front Azure Service Bus v Pythonu

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento článek ukazuje, jak pomocí Pythonu vytvářet, odesílat zprávy a přijímat zprávy z front Azure Service Bus. 

Další informace o knihovnách service bus Pythonu Azure najdete v [tématu Knihovny service bus pro Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. Můžete aktivovat [výhody pro předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Obor názvů Service Bus vytvořený podle kroků na [úvodním panelu: Pomocí portálu Azure vytvořte téma služby Service Bus a předplatná](service-bus-quickstart-topics-subscriptions-portal.md). Zkopírujte primární připojovací řetězec z obrazovky **Zásady sdíleného přístupu,** který se použije později v tomto článku. 
- Python 3.4x nebo vyšší s nainstalovaným balíčkem [service bus Pythonu Azure.][Python Azure Service Bus package] Další informace naleznete v [Průvodci instalací pythonu](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Vytvoření fronty

Objekt **ServiceBusClient** umožňuje pracovat s frontami. Chcete-li programově přistupovat ke službě Service Bus, přidejte v horní části souboru Pythonu následující řádek:

```python
from azure.servicebus import ServiceBusClient
```

Přidejte následující kód k vytvoření objektu **ServiceBusClient.** Nahraďte `<connectionstring>` primární hodnotou připojovacího řetězce service bus. Tuto hodnotu najdete v části **Zásady sdíleného přístupu** v oboru názvů Service Bus na [webu Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Následující kód používá `create_queue` metodu **ServiceBusClient** k vytvoření `taskqueue` fronty s názvem s výchozím nastavením:

```python
sb_client.create_queue("taskqueue")
```

Pomocí možností můžete přepsat výchozí nastavení fronty, například čas zprávy do života (TTL) nebo maximální velikost tématu. Následující kód vytvoří frontu volanou `taskqueue` s maximální velikostí fronty 5 GB a hodnotou TTL 1 minutu:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty

Chcete-li odeslat zprávu do fronty služby Service Bus, aplikace volá metodu `send` na objektu **ServiceBusClient.** Následující příklad kódu vytvoří klienta fronty a `taskqueue` odešle zkušební zprávu do fronty. Nahraďte `<connectionstring>` primární hodnotou připojovacího řetězce service bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Omezení velikosti a kvóty zpráv

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv, které může fronta obsahovat, není nijak omezen, ale je omezencelkový počet zpráv, které fronta obsahuje. Velikost fronty můžete definovat v době vytvoření s horním limitem 5 GB. 

Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Klient fronty přijímá zprávy z fronty `get_receiver` pomocí metody na objektu **ServiceBusClient.** Následující příklad kódu vytvoří klienta fronty a `taskqueue` obdrží zprávu z fronty. Nahraďte `<connectionstring>` primární hodnotou připojovacího řetězce service bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Použití peek_lock parametru

Volitelný `peek_lock` parametr `get_receiver` určuje, zda Service Bus odstraní zprávy z fronty při jejich čtení. Výchozí režim pro příjem zpráv je `peek_lock` *PeekLock*nebo nastavena na **True**, který čte (náhledy) a uzamkne zprávy bez jejich odstranění z fronty. Každá zpráva pak musí být explicitně dokončena, aby byla odebrána z fronty.

Chcete-li odstranit zprávy z fronty při jejich `peek_lock` čtení, `get_receiver` můžete nastavit parametr **False**. Odstranění zpráv jako součást operace příjmu je nejjednodušší model, ale funguje pouze v případě, že aplikace může tolerovat chybějící zprávy, pokud dojde k selhání. Chcete-li pochopit toto chování, zvažte scénář, ve kterém spotřebitel vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Pokud byla zpráva odstraněna při přijetí, když se aplikace restartuje a začne znovu spotřebovávat zprávy, zmeškala zprávu, kterou obdržela před selháním.

Pokud vaše aplikace nemůže tolerovat zmeškané zprávy, příjem je dvoustupňová operace. PeekLock najde další zprávu, která má být spotřebována, uzamkne ji zabránit ostatním spotřebitelům v přijímání a vrátí ji do aplikace. Po zpracování nebo uložení zprávy aplikace dokončí druhou fázi procesu příjmu `complete` voláním metody na **Message** objektu.  Metoda `complete` označí zprávu jako spotřebované a odebere ji z fronty.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Zpracování selhání aplikací a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce nemůže znějakého důvodu zpracovat zprávu, může volat metodu `unlock` na **Message** objektu. Service Bus odemkne zprávu ve frontě a zpřístupní ji k přijetí znovu, buď stejnou nebo jinou náročnou aplikací.

K dispozici je také časový čas pro zprávy zamčené ve frontě. Pokud aplikace nepodaří zpracovat zprávu před vypršením časového limitu uzamčení, například pokud dojde k chybě aplikace, service bus automaticky odemkne zprávu a zpřístupní ji k přijetí znovu.

Pokud dojde k chybě aplikace po zpracování `complete` zprávy, ale před voláním metody, zpráva je znovu doručena do aplikace při restartování. Toto chování se často nazývá *At-least-once Processing*. Každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud váš scénář nemůže tolerovat duplicitní zpracování, můžete použít **MessageId** vlastnost zprávy, která zůstává konstantní napříč pokusy o doručení, ke zpracování duplicitní doručení zprávy. 

> [!TIP]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Aplikace Service Bus Explorer umožňuje připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu a možnost testovat témata, fronty, předplatná, přenosové služby, centra oznámení a centra událostí.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy front service bus, najdete [v tématu Fronty, témata a předplatná][Queues, topics, and subscriptions] další informace.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
