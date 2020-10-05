---
title: 'Rychlý Start: použití Azure Service Bus front s Pythonem'
description: V tomto článku se dozvíte, jak pomocí Pythonu vytvářet, odesílat zprávy a přijímat zprávy z Azure Service Busch front.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: a09f20b2c392dbf219750a76e9570239227dc865
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89458557"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Rychlý Start: použití Azure Service Bus front s Pythonem

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

V tomto článku se dozvíte, jak pomocí Pythonu vytvářet, odesílat zprávy a přijímat zprávy z Azure Service Busch front. 

Další informace o knihovnách Azure Service Bus Pythonu najdete v tématu [knihovny Service Bus pro Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Service Bus obor názvů vytvořený podle kroků v [části rychlý Start: pomocí Azure Portal vytvořte Service Bus téma a odběry](service-bus-quickstart-topics-subscriptions-portal.md). Zkopírujte primární připojovací řetězec z obrazovky **zásady sdíleného přístupu** , která se použije později v tomto článku. 
- Python 3.4 x nebo vyšší s nainstalovaným balíčkem [Python Azure Service Bus][Python Azure Service Bus package] . Další informace najdete v příručce pro [instalaci Pythonu](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Vytvoření fronty

Objekt **ServiceBusClient** vám umožní pracovat s frontami. K programovému přístupu Service Bus přidejte do horní části souboru Pythonu následující řádek:

```python
from azure.servicebus import ServiceBusClient
```

Přidejte následující kód pro vytvoření objektu **ServiceBusClient** . Nahraďte `<connectionstring>` hodnotou Service Bus primárním připojovacím řetězcem. Tuto hodnotu můžete najít v části **zásady sdíleného přístupu** v oboru názvů Service Bus v [Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Následující kód používá `create_queue` metodu **ServiceBusClient** k vytvoření fronty s názvem `taskqueue` s výchozími nastaveními:

```python
sb_client.create_queue("taskqueue")
```

Pomocí možností můžete přepsat výchozí nastavení fronty, jako je například hodnota TTL (Time to Live) nebo maximální velikost tématu. Následující kód vytvoří frontu s názvem `taskqueue` maximální velikost fronty 5 GB a hodnotou TTL 1 minutou:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty

Chcete-li odeslat zprávu do fronty Service Bus, aplikace zavolá `send` metodu na objekt **ServiceBusClient** . Následující příklad kódu vytvoří klienta fronty a pošle zkušební zprávu do `taskqueue` fronty. Nahraďte `<connectionstring>` hodnotou Service Bus primárním připojovacím řetězcem. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Omezení velikosti zpráv a kvóty

Fronty Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv, které fronta může obsahovat, není nijak omezený, ale celková velikost zpráv, které fronta uchovává, je příliš velká. Velikost fronty můžete definovat při vytváření s horním limitem 5 GB. 

Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty

Klient fronty přijímá zprávy z fronty pomocí `get_receiver` metody v objektu **ServiceBusClient** . Následující příklad kódu vytvoří klienta fronty a přijme zprávu z `taskqueue` fronty. Nahraďte `<connectionstring>` hodnotou Service Bus primárním připojovacím řetězcem. 

```python
from azure.servicebus import QueueClient

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Použití parametru peek_lock

Volitelný `peek_lock` parametr `get_receiver` Určuje, zda Service Bus odstraní zprávy z fronty při jejich čtení. Výchozí režim pro přijímání zpráv je *PeekLock*nebo `peek_lock` nastaven na **hodnotu true**, která čte (prohlédne) a uzamkne zprávy bez jejich odstranění z fronty. Každá zpráva musí být pak explicitně dokončena, aby ji bylo možné odebrat z fronty.

Pokud chcete odstranit zprávy z fronty při jejich čtení, můžete nastavit `peek_lock` parametr `get_receiver` na **false (NEPRAVDA**). Odstraňování zpráv v rámci operace Receive je nejjednodušší model, ale funguje pouze v případě, že aplikace může tolerovat chybějící zprávy, pokud dojde k selhání. Pro pochopení tohoto chování Vezměte v úvahu situaci, ve které spotřebitel vydá žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Pokud se zpráva odstranila při obdržení, když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu přijatou před selháním.

Pokud vaše aplikace nemůže tolerovat zmeškaných zpráv, je příjem operace se dvěma fázemi. PeekLock najde další zprávu, která se má spotřebovat, zamkne ji, aby zabránila ostatním příjemcům v přijetí a vrátila ji do aplikace. Po zpracování nebo uložení zprávy aplikace dokončí druhou fázi procesu příjmu voláním `complete` metody objektu **Message** .  `complete`Metoda označí zprávu jako spotřebou a odebere ji z fronty.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Zpracování chyb aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nedokáže zpracovat zprávu, může zavolat `unlock` metodu na objekt **zprávy** . Service Bus odemkne zprávu v rámci fronty a zpřístupní ji tak, aby byla znovu přijata, a to buď pomocí stejné, nebo jiné náročné aplikace.

Je také časový limit pro zprávy uzamčený ve frontě. Pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku, například pokud dojde k selhání aplikace, Service Bus automaticky odemkne zprávu a zpřístupní ji pro příjem.

Pokud dojde k chybě aplikace po zpracování zprávy, ale před voláním `complete` metody, zpráva je znovu doručena do aplikace při restartu. Toto chování se často nazývá *zpracování nejméně jednou*. Každá zpráva je zpracována alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud váš scénář nemůže tolerovat duplicitní zpracování, můžete použít vlastnost **MessageID** zprávy, která zůstává při pokusůch o doručení konstantní, pro zpracování duplicitního doručování zpráv. 

> [!TIP]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer vám umožní připojit se k Service Busmu oboru názvů a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, a možnost testovat témata, fronty, odběry, služby přenosu, centra oznámení a centra událostí.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Service Busch front, najdete další informace v tématu [fronty, témata a předplatná][Queues, topics, and subscriptions] .

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
