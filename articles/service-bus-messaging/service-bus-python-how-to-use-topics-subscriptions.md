---
title: 'Rychlý Start: použití témat Azure Service Bus a předplatných v Pythonu'
description: Naučte se používat Azure Service Bus témata a odběry z Pythonu.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 94a49b31139947c6323ab391b78ecd03ee911e0a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748505"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Rychlý Start: použití témat Service Bus a předplatných v Pythonu

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat Python s Azure Service Bus tématem a odběry. Ukázky používají balíček [Azure Python SDK][Azure Python package] k těmto akcím: 

- Témata a odběry můžete vytvářet v tématech.
- Vytváření filtrů a pravidel předplatného
- Posílání zpráv do témat 
- Přijímání zpráv z předplatných
- Odstranění témat a odběrů

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Service Bus obor názvů vytvořený podle kroků v [části rychlý Start: pomocí Azure Portal vytvořte Service Bus téma a odběry](service-bus-quickstart-topics-subscriptions-portal.md). Zkopírujte název oboru názvů, název sdíleného přístupového klíče a hodnotu primárního klíče z obrazovky **zásady sdíleného přístupu** , které se použijí později v tomto rychlém startu. 
- Python 3.4 x nebo vyšší s nainstalovaným balíčkem [sady Azure Python SDK][Azure Python package] . Další informace najdete v příručce pro [instalaci Pythonu](/azure/python/python-sdk-azure-install).

## <a name="create-a-servicebusservice-object"></a>Vytvoření objektu ServiceBusService

Objekt **ServiceBusService** vám umožní pracovat s tématy a odběry v tématech. K programovému přístupu Service Bus přidejte do horní části souboru Pythonu následující řádek:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Přidejte následující kód pro vytvoření objektu **ServiceBusService** . `<namespace>`, `<sharedaccesskeyname>`a `<sharedaccesskeyvalue>` nahraďte názvem oboru názvů Service Bus, názvem klíče sdíleného přístupového podpisu (SAS) a hodnotou primárního klíče. Tyto hodnoty najdete v části **zásady sdíleného přístupu** v oboru názvů Service Bus v [Azure Portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Vytvoření tématu

Následující kód používá metodu `create_topic` k vytvoření Service Busho tématu s názvem `mytopic`s výchozími nastaveními:

```python
bus_service.create_topic('mytopic')
```

Pomocí možností tématu můžete přepsat výchozí nastavení tématu, jako je například hodnota TTL (Time to Live) nebo maximální velikost tématu. Následující příklad vytvoří téma s názvem `mytopic` s maximální velikostí tématu 5 GB a výchozí hodnotou TTL zprávy 1 minute:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Vytvoření předplatných

K vytváření předplatných pro témata také použijete objekt **ServiceBusService** . Předplatné může mít filtr, který omezí sadu zpráv doručenou do její virtuální fronty. Pokud neurčíte filtr, nové odběry použijí výchozí filtr **MatchAll** , který umístí všechny zprávy publikované v tématu do virtuální fronty odběru. Následující příklad vytvoří odběr `mytopic` pojmenovaný `AllMessages`, který používá filtr **MatchAll** :

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Použití filtrů s předplatnými

Pomocí metody `create_rule` objektu **ServiceBusService** můžete filtrovat zprávy, které se zobrazují v rámci předplatného. Můžete zadat pravidla při vytváření předplatného nebo přidávat pravidla do stávajících předplatných.

Nejpružnější typ filtru je **SqlFilter**, který používá podmnožinu SQL-92. Filtry SQL fungují na základě vlastností zpráv publikovaných v tématu. Další informace o výrazech, které lze použít s filtrem SQL, naleznete v syntaxi [SqlFilter. syntaxi][SqlFilter.SqlExpression] .

Vzhledem k tomu, že výchozí filtr **MatchAll** se automaticky aplikuje na všechna nová předplatná, musíte ho odebrat z předplatných, která chcete filtrovat, nebo bude **MatchAll** přepsat všechny ostatní filtry, které zadáte. Výchozí pravidlo můžete odebrat pomocí metody `delete_rule` objektu **ServiceBusService** .

Následující příklad vytvoří předplatné pro `mytopic` s názvem `HighMessages`s pravidlem **SqlFilter** s názvem `HighMessageFilter`. Pravidlo `HighMessageFilter` vybere pouze zprávy s vlastností Custom `messageposition` větší než 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Následující příklad vytvoří předplatné pro `mytopic` s názvem `LowMessages`s pravidlem **SqlFilter** s názvem `LowMessageFilter`. Pravidlo `LowMessageFilter` vybere pouze zprávy s vlastností `messageposition` menší nebo rovnou 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

V důsledku `AllMessages`, `HighMessages`a `LowMessages` se zprávy odeslané do `mytopic` vždycky doručují příjemcům `AllMessages` předplatného. Zprávy jsou také selektivně doručovány do `HighMessages` nebo `LowMessages` předplatného v závislosti na hodnotě vlastnosti `messageposition` zprávy. 

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

Aplikace používají metodu `send_topic_message` objektu **ServiceBusService** k posílání zpráv do tématu Service Bus.

Následující příklad odešle pět zkušebních zpráv do tématu `mytopic`. Hodnota vlastnosti vlastního `messageposition` závisí na iteraci smyčky a určuje, které odběry obdrží zprávy. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Omezení velikosti zpráv a kvóty

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv, které může téma uchovávat, není nijak omezený, ale celková velikost zpráv, které toto téma obsahuje, je příliš velká. V části čas vytvoření můžete definovat velikost tématu s horním limitem 5 GB. 

Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného

Aplikace používají metodu `receive_subscription_message` v objektu **ServiceBusService** k přijímání zpráv z předplatného. Následující příklad přijme zprávy z předplatného `LowMessages` a odstraní je při jejich čtení:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Volitelný parametr `peek_lock` `receive_subscription_message` určuje, zda Service Bus odstraní zprávy z odběru při jejich čtení. Výchozím režimem pro přijímání zpráv je *PeekLock*nebo `peek_lock` nastaveno na **hodnotu true**, která čte (prohlédne) a uzamkne zprávy bez jejich odstranění z předplatného. Každá zpráva musí být pak explicitně dokončena, aby ji bylo možné odebrat z předplatného.

Pokud chcete odstranit zprávy z odběru při jejich čtení, můžete nastavit parametr `peek_lock` na **hodnotu false**, jako v předchozím příkladu. Odstraňování zpráv v rámci operace Receive je nejjednodušší model a funguje správně, pokud aplikace může tolerovat chybějící zprávy, pokud dojde k selhání. Pro pochopení tohoto chování Vezměte v úvahu scénář, ve kterém aplikace vystavuje žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Pokud se zpráva odstranila při obdržení, když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu přijatou před selháním.

Pokud vaše aplikace nemůže tolerovat zmeškané zprávy, obdrží se příjem operace se dvěma fázemi. PeekLock najde další zprávu, která se má spotřebovat, zamkne ji, aby zabránila ostatním příjemcům v přijetí a vrátila ji do aplikace. Po zpracování nebo uložení zprávy aplikace dokončí druhou fázi procesu Receive voláním metody `complete` u objektu **Message** .  Metoda `complete` označí zprávu jako spotřebou a odebere ji z předplatného.

Následující příklad ukazuje scénář pro náhled zámku:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Zpracování chyb aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nedokáže zpracovat zprávu, může volat metodu `unlock` pro objekt **zprávy** . Service Bus odemkne zprávu v rámci předplatného a zpřístupní ji tak, aby byla znovu přijata, a to buď pomocí stejné, nebo jiné náročné aplikace.

V rámci předplatného je také časový limit pro zprávy uzamčený. Pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku, například pokud dojde k selhání aplikace, Service Bus automaticky odemkne zprávu a zpřístupní ji pro příjem.

Pokud dojde k chybě aplikace po zpracování zprávy, ale před voláním metody `complete`, zpráva se znovu doručí do aplikace při restartu. Toto chování se často nazývá *zpracování nejméně jednou*. Každá zpráva je zpracována alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud váš scénář nemůže tolerovat duplicitní zpracování, můžete použít vlastnost **MessageID** zprávy, která zůstává při pokusůch o doručení konstantní, pro zpracování duplicitního doručování zpráv. 

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů

Chcete-li odstranit témata a odběry, použijte metodu [Azure Portal][Azure portal] nebo `delete_topic`. Následující kód odstraní téma s názvem `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Odstraněním tématu se odstraní všechna předplatná v tématu. Odběry můžete také odstranit nezávisle. Následující kód odstraní předplatné s názvem `HighMessages` z `mytopic`ho tématu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Ve výchozím nastavení jsou témata a odběry trvalé a existují, dokud je neodstraníte. K automatickému odstranění předplatných po určitém časovém období můžete nastavit parametr [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) v předplatném. 

> [!TIP]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer vám umožní připojit se k Service Busmu oboru názvů a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, a možnost testovat témata, fronty, odběry, služby přenosu, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy Service Bus témata, další informace najdete na následujících odkazech:

* [Fronty, témata a odběry][Queues, topics, and subscriptions]
* Odkaz na [SqlFilter. syntaxi][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
