---
title: 'Úvodní příručka: Použití témat a předplatných Azure Service Bus s Pythonem'
description: Tento článek ukazuje, jak vytvořit téma Azure Service Bus, předplatné, odesílat zprávy na téma a přijímat zprávy z předplatného.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478069"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Úvodní příručka: Použití témat a předplatných služby Service Bus v Pythonu

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat Python s tématy a předplatnými Služby Azure Service Bus. Ukázky používají balíček [Azure Python SDK][Azure Python package] k: 

- Vytváření témat a odběrů témat
- Vytvoření filtrů a pravidel předplatného
- Odesílání zpráv na témata 
- Příjem zpráv z předplatných
- Odstranění témat a odběrů

## <a name="prerequisites"></a>Požadavky
- Předplatné Azure. Můžete aktivovat [výhody pro předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Obor názvů Service Bus vytvořený podle kroků na [úvodním panelu: Pomocí portálu Azure vytvořte téma služby Service Bus a předplatná](service-bus-quickstart-topics-subscriptions-portal.md). Zkopírujte název oboru názvů, název sdíleného přístupového klíče a hodnotu primárního klíče z obrazovky **Zásady sdíleného přístupu,** abyste jej později použili v tomto rychlém startu. 
- Python 3.4x nebo vyšší s nainstalovaným balíčkem [Azure Python SDK.][Azure Python package] Další informace naleznete v [Průvodci instalací pythonu](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Vytvoření objektu ServiceBusService

Objekt **ServiceBusService** umožňuje pracovat s tématy a odběry témat. Chcete-li programově přistupovat ke službě Service Bus, přidejte v horní části souboru Pythonu následující řádek:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Přidejte následující kód k vytvoření objektu **ServiceBusService.** Nahraďte `<namespace>`, `<sharedaccesskeyname>`a `<sharedaccesskeyvalue>` s názvem oboru názvů Service Bus, název klíče sdílený přístupový podpis (SAS) a hodnotu primárního klíče. Tyto hodnoty najdete v části **Zásady sdíleného přístupu** v oboru názvů Service Bus na [webu Azure Portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Vytvoření tématu

Následující kód používá `create_topic` metodu k vytvoření `mytopic`tématu service bus s názvem , s výchozím nastavením:

```python
bus_service.create_topic('mytopic')
```

Pomocí možností tématu můžete přepsat výchozí nastavení tématu, například čas zprávy k životu (TTL) nebo maximální velikost tématu. Následující příklad vytvoří téma `mytopic` s názvem s maximální velikostí tématu 5 GB a výchozí zprávou TTL jedné minuty:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Vytvoření předplatných

Můžete také použít **ServiceBusService** objekt k vytvoření odběry témata. Odběr může mít filtr omezit sadu zpráv doručované do jeho virtuální fronty. Pokud nezadáte filtr, nová předplatná použít výchozí **MatchAll** filtr, který umístí všechny zprávy publikované do tématu do virtuální fronty předplatného. Následující příklad vytvoří odběr `mytopic` `AllMessages` s názvem, který používá **filtr MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Použití filtrů s předplatnými

Pomocí `create_rule` metody objektu **ServiceBusService** můžete filtrovat zprávy, které se zobrazí v předplatném. Můžete určit pravidla při vytváření předplatného nebo přidat pravidla do existujících odběrů.

Nejflexibilnějším typem filtru je **sqlfilter**, který používá podmnožinu SQL-92. Filtry SQL pracují na základě vlastností zpráv publikovaných v tématu. Další informace o výrazech, které můžete použít s filtrem SQL, naleznete v syntaxi [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Vzhledem k tomu, že výchozí filtr **MatchAll** se automaticky použije na všechna nová předplatná, je nutné jej odebrat z odběrů, které chcete filtrovat, jinak **matchall** přepíše všechny ostatní filtry, které zadáte. Výchozí pravidlo můžete odebrat `delete_rule` pomocí metody objektu **ServiceBusService.**

Následující příklad vytvoří odběr `mytopic` `HighMessages`s názvem , s `HighMessageFilter`názvem pravidla **SqlFilter** . Pravidlo `HighMessageFilter` vybere pouze zprávy s `messageposition` vlastní vlastností větší než 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Následující příklad vytvoří odběr `mytopic` `LowMessages`s názvem , s `LowMessageFilter`názvem pravidla **SqlFilter** . Pravidlo `LowMessageFilter` vybere pouze zprávy `messageposition` s vlastností menší nebo rovnou 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

S `AllMessages` `HighMessages`, `LowMessages` , a všechny v `mytopic` platnosti, zprávy odeslané do jsou vždy doručeny příjemcům `AllMessages` předplatného. Zprávy jsou také selektivně `LowMessages` doručovány do `messageposition` `HighMessages` nebo předplatného, v závislosti na hodnotě vlastnosti zprávy. 

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

Aplikace používají `send_topic_message` metodu Objektu **ServiceBusService** k odesílání zpráv do tématu služby Service Bus.

Následující příklad odešle pět `mytopic` testovacích zpráv do tématu. Hodnota `messageposition` vlastní vlastnosti závisí na iteraci smyčky a určuje, které odběry přijímat zprávy. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Omezení velikosti a kvóty zpráv

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv, které může téma obsahovat, není nijak omezen, ale je zde omezení celkové velikosti zpráv, které téma obsahuje. Velikost tématu můžete definovat v době vytvoření s horním limitem 5 GB. 

Další informace o kvótách naleznete v [tématu Kvóty služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z předplatného

Aplikace používají `receive_subscription_message` metodu na **ServiceBusService** objektu přijímat zprávy z předplatného. Následující příklad přijímá zprávy `LowMessages` z předplatného a odstraňuje je při jejich čtení:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Volitelný `peek_lock` parametr `receive_subscription_message` určuje, zda Service Bus odstraní zprávy z předplatného, jak jsou čteny. Výchozí režim pro příjem zpráv je `peek_lock` *PeekLock*, nebo nastavit **True**, který čte (náhledy) a uzamkne zprávy bez jejich odstranění z předplatného. Každá zpráva pak musí být explicitně dokončena, aby byla odebrána z předplatného.

Chcete-li odstranit zprávy z předplatného při jejich `peek_lock` čtení, můžete nastavit parametr **false**, jako v předchozím příkladu. Odstranění zpráv jako součást operace příjmu je nejjednodušší model a funguje dobře, pokud aplikace může tolerovat chybějící zprávy, pokud dojde k selhání. Chcete-li pochopit toto chování, zvažte scénář, ve kterém aplikace vydá požadavek na přijetí a pak dojde k chybě před jeho zpracováním. Pokud byla zpráva odstraněna při přijetí, když se aplikace restartuje a začne znovu spotřebovávat zprávy, zmeškala zprávu, kterou obdržela před selháním.

Pokud vaše aplikace nemůže tolerovat zmeškané zprávy, příjem se stane dvoustupňovou operací. PeekLock najde další zprávu, která má být spotřebována, uzamkne ji zabránit ostatním spotřebitelům v přijímání a vrátí ji do aplikace. Po zpracování nebo uložení zprávy aplikace dokončí druhou fázi procesu příjmu `complete` voláním metody na **Message** objektu.  Metoda `complete` označí zprávu jako spotřebované a odebere ji z předplatného.

Následující příklad ukazuje scénář uzamčení náhledu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Zpracování selhání aplikací a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce nemůže znějakého důvodu zpracovat zprávu, může volat metodu `unlock` na **Message** objektu. Service Bus odemkne zprávu v rámci předplatného a zpřístupní ji k přijetí znovu, buď stejnou nebo jinou náročné aplikace.

K dispozici je také časový čas pro zprávy zamčené v rámci předplatného. Pokud aplikace nepodaří zpracovat zprávu před vypršením časového limitu uzamčení, například pokud dojde k chybě aplikace, service bus automaticky odemkne zprávu a zpřístupní ji k přijetí znovu.

Pokud dojde k chybě aplikace po zpracování `complete` zprávy, ale před voláním metody, zpráva bude znovu doručena do aplikace při restartování. Toto chování se často nazývá *At-least-once Processing*. Každá zpráva je zpracována alespoň jednou, ale v určitých situacích může být stejná zpráva znovu doručena. Pokud váš scénář nemůže tolerovat duplicitní zpracování, můžete použít **MessageId** vlastnost zprávy, která zůstává konstantní napříč pokusy o doručení, ke zpracování duplicitní doručení zprávy. 

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů

Chcete-li odstranit témata a předplatná, `delete_topic` použijte portál [Azure][Azure portal] nebo metodu. Následující kód odstraní téma `mytopic`s názvem :

```python
bus_service.delete_topic('mytopic')
```

Odstraněním tématu odstraníte všechna odběry tématu. Odběry můžete také odstranit nezávisle. Následující kód odstraní odběr `HighMessages` pojmenovaný `mytopic` z tématu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Ve výchozím nastavení jsou témata a odběry trvalé a existují, dokud je neodstraníte. Chcete-li automaticky odstranit odběry po uplynutí určitého časového období, můžete nastavit [parametr auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) v předplatném. 

> [!TIP]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Aplikace Service Bus Explorer umožňuje připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu a možnost testovat témata, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili základy témat služby Service Bus, se dozvíte více na těchto odkazech:

* [Fronty, témata a odběry][Queues, topics, and subscriptions]
* Odkaz [SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
