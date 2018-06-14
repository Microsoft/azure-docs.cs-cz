---
title: Jak používat témata služby Azure Service Bus s Pythonem | Microsoft Docs
description: Naučte se používat Azure Service Bus témata a odběry z Pythonu.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/20/2018
ms.author: sethm
ms.openlocfilehash: 6f262a63de9409d6b355d9783ca958e4715b1ea5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797715"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Jak používat témata a odběry Service Bus s Pythonem

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata a odběry služby Service Bus. Ukázky jsou napsané v Pythonu a použití [balíček Azure Python SDK][Azure Python package]. Pokryté scénáře zahrnují **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijímání zpráv z odběru**, a **odstranění témat a odběrů**. Další informace o tématech a odběrech najdete v tématu [další kroky](#next-steps) části.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Pokud potřebujete k instalaci Pythonu nebo [balíček Azure Python][Azure Python package], najdete v článku [Průvodce instalací Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Vytvoření tématu

**ServiceBusService** objektu umožňuje pracovat s tématy. Přidejte následující kód v horní jakéhokoliv Python souboru, ve kterém chcete programový přístupu k Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Následující kód vytvoří **ServiceBusService** objektu. Nahraďte `mynamespace`, `sharedaccesskeyname`, a `sharedaccesskey` s vašeho oboru názvů skutečný název a klíč hodnotu klíče sdíleného přístupového podpisu (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Můžete získat hodnoty pro název klíče SAS a hodnotu z [portál Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

`create_topic` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení téma například zpráva hodnota time to live nebo téma maximální velikost. Následující příklad nastaví na 5 GB a čas live (TTL) hodnotu jedna minuta tématu maximální velikost:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Vytvořte odběr

Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, které omezují výběr zpráv doručených do virtuální fronty odběru.

> [!NOTE]
> Odběry jsou trvalé a bude dál existovat, dokud buď jejich, nebo v tématu, ke kterému jsou předplatné, se odstraní.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).

Filtr **MatchAll** je výchozí filtr, který se použije v případě, že při vytváření nového odběru nezadáte žádný filtr. Když **MatchAll** filtr se používá, všechny zprávy publikované do tématu jsou umístěny do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem `AllMessages` a používá výchozí **MatchAll** filtru.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry

Můžete také definovat filtry, které vám umožní určit, které by měl zprávy odeslané do tématu objeví v konkrétním odběru tématu.

Nejflexibilnější filtr, který odběry podporují je **SqlFilter**, který implementuje je podmnožinou SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o výrazech, které se dají použít s filtrem SQL, najdete v syntaxi [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Filtry k odběru můžete přidat pomocí **vytvořit\_pravidlo** metodu **ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

> [!NOTE]
> Protože výchozí filtr je automaticky použita pro všechny nové odběry, je nutno nejprve odstranit výchozí filtr nebo **MatchAll** přepíše ostatní filtry, můžete určit. Výchozí pravidla můžete odebrat pomocí `delete_rule` metodu **ServiceBusService** objektu.
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** který vybere jen zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Podobně platí, tento příklad vytvoří odběr s názvem `LowMessages` s **SqlFilter** který vybere jen zprávy, které mají `messagenumber` vlastnost menší než nebo rovna na 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Teď, když je odeslána zpráva `mytopic` vždy se dodá příjemci **AllMessages** odběr tématu a selektivně příjemcům přihlásit k odběru **HighMessages** a **LowMessages** odběry témat (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

K odeslání zprávy do tématu Service Bus, musí vaše aplikace používat `send_topic_message` metodu **ServiceBusService** objektu.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `mytopic`. `messagenumber` Hodnotu vlastnosti každé zprávy se liší na iteraci smyčky (to určuje, které odběry ji přijmou):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru

Přijímání zpráv z odběru pomocí `receive_subscription_message` metodu **ServiceBusService** objektu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Zprávy jsou odstraněny z předplatného, jako při jejich přečtení parametr `peek_lock` je nastaven na **False**. Můžete číst (funkce Náhled) a uzamčení zpráva bez odstranění z fronty nastavením parametru `peek_lock` k **True**.

Chování čtení a odstranění zprávy v rámci operace příjmu je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace tolerovat, pokud dojde k selhání se zpráva nezpracuje. Zjistit, toto chování, vezměte v úvahu scénář, ve kterém spotřebitel vyšle požadavek na přijetí a pak dojde k chybě před zpracováním ho. Vzhledem k tomu, že Service Bus se už ale zprávu označila jako spotřebovávanou, pak když se aplikace restartuje a začne znovu přijímat zprávy, ji budou neuskutečnily zprávu, která se spotřebovala před havárii.

Pokud `peek_lock` parametr je nastaven na **True**, receive stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), tím potvrdí dokončení druhé fáze přijetí volání `delete` metodu **zpráva** objektu. `delete` Metoda označí zprávu jako spotřebovávanou a odstraní ji z odběru.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může zavolat `unlock` metodu **zpráva** objektu. Tato metoda způsobí, že Service Bus zprávu odemkne v odběru a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

Je také vypršení časového limitu zpráva uzamčená v odběru, a pokud se nepodaří aplikace zprávu nezpracuje zámku vyprší časový limit (například pokud aplikace spadne), pak Service Bus zprávu automaticky odemkne a ji zpřístupní k přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete` metoda je volána, pak zpráva bude vysláním do aplikace odešle znovu. Toto chování se často říká *zpracování nejméně jednou*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát stejnou zprávu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Chcete-li to provést, můžete použít **MessageId** vlastnosti zprávy, která zůstává konstantní mezi pokusy o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů

Témata a odběry jsou trvalé a musí být explicitně odstranit buď prostřednictvím [portál Azure] [ Azure portal] nebo prostřednictvím kódu programu. Následující příklad ukazuje, jak odstranit téma s názvem `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit odběr s názvem `HighMessages` z `mytopic` tématu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili základy témat sběrnice Service Bus, postupujte podle následujících odkazech na další informace.

* V tématu [fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference pro [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
