---
title: Jak používat témata služby Azure Service Bus pomocí Pythonu | Dokumentace Microsoftu
description: Další informace o použití Azure Service Bus, témat a odběrů z Pythonu.
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
ms.topic: article
ms.date: 09/20/2018
ms.author: aschhab
ms.openlocfilehash: 280901c4b7af9cc65163ebc453a54fb7ec5d5111
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849392"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Jak používat témata a odběry Service Bus pomocí Pythonu

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata a odběry služby Service Bus. Ukázky jsou napsané v Pythonu a použití [balíčku sady Azure Python SDK][Azure Python package]. Mezi popsané scénáře patří **vytváření témat a odběrů**, **vytváření filtrů odběrů**, **odesílání zpráv do tématu**, **přijetí zprávy z odběru**, a **odstranění témat a odběrů**. Další informace o tématech a odběrech najdete v tématu [další kroky](#next-steps) oddílu.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Pokud je potřeba nainstalovat Python nebo [balíček Pythonu Azure][Azure Python package], najdete v článku [Průvodce instalací Pythonu](../python-how-to-install.md).

## <a name="create-a-topic"></a>Vytvoření tématu

**ServiceBusService** objektu umožňuje pracovat s tématy. Přidejte následující kód do horní jakéhokoli souboru Python, ve kterém chcete programovému přístupu ke službě Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Následující kód vytvoří **ServiceBusService** objektu. Nahraďte `mynamespace`, `sharedaccesskeyname`, a `sharedaccesskey` s skutečný obor názvů, název a klíč hodnotu klíče sdíleného přístupového podpisu (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Můžete získat hodnoty pro název klíče SAS a hodnoty ze [webu Azure portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

`create_topic` Metoda také podporuje další možnosti, které vám umožní přepsat výchozí nastavení téma například time to live zprávy nebo téma maximální velikost. Následující příklad nastaví maximální počet témat velikosti 5 GB a čas live (TTL) hodnotu jedné minuty:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Vytvořit odběry

Odběry témat taky jsou vytvořeny pomocí **ServiceBusService** objektu. Odběry mají názvy a můžou mít volitelné filtry, který omezuje sadu doručování zpráv do virtuální fronty odběru.

> [!NOTE]
> Předplatná jsou trvalé a bude pokračovat až do jejich nebo tématu, ke kterému jsou přihlášeni, se odstraní.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).

Pokud není zadán žádný filtr, když se vytvoří nový odběr, **MatchAll** použít filtr (výchozí). Když **MatchAll** se používá filtr, všechny zprávy publikované do tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem `AllMessages` a používá výchozí **MatchAll** filtru.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry

Můžete také Definujte filtry, které vám umožní určit, které zprávy odeslané do tématu se měla zobrazit v konkrétním odběru tématu.

Je nejflexibilnějším typem filtru odběry **SqlFilter**, která implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace o výrazech, které se dají použít s filtrem SQL, najdete v syntaxi [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Můžete přidat filtry na předplatné s použitím **vytvořit\_pravidlo** metodu **ServiceBusService** objektu. Tato metoda umožňuje přidat nové filtry k existujícímu předplatnému.

> [!NOTE]
> Protože výchozí filtr automaticky platí pro všechna nová předplatná, musíte nejdřív odebrat výchozí filtr nebo **MatchAll** přepíše ostatní filtry, můžete zadat. Výchozí pravidla můžete odebrat pomocí `delete_rule` metodu **ServiceBusService** objektu.
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` s **SqlFilter** , který vybere jen zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Obdobně následující příklad vytvoří odběr s názvem `LowMessages` s **SqlFilter** , který vybere jen zprávy, které mají `messagenumber` vlastnost menší než nebo rovna na 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Teď, když je odeslána zpráva `mytopic` vždy doručení příjemcům **AllMessages** odběru tématu a selektivně příjemcům **HighMessages**a **LowMessages** (podle obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

Odeslat zprávu do tématu služby Service Bus, vaše aplikace musí používat `send_topic_message` metodu **ServiceBusService** objektu.

Následující příklad ukazuje, jak odeslat pět zkušebních zpráv do `mytopic`. `messagenumber` Hodnota vlastnosti každé zprávy se liší v iteraci smyčky (to určuje, které odběry ji přijmou):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [kvótách služby Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Příjem zpráv z odběru

Přijme zprávy z odběru pomocí `receive_subscription_message` metodu **ServiceBusService** objektu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Zprávy jsou odstraněny z předplatného, jako jsou načteny při parametr `peek_lock` je nastavena na **False**. Může číst (Náhled) a uzamčení zprávy bez odstranění z fronty tak, že nastavíte parametr `peek_lock` k **True**.

Chování pro čtení a odstranění zprávy jako součást operace receive je nejjednodušší model a funguje nejlépe v situacích, ve kterých aplikace může tolerovat možnost, není zpracování zprávy, když dojde k selhání. Informace o tom toto chování, vezměte v úvahu scénář, ve kterém spotřebitel požadavek na přijetí a poté dojde k chybě před její zpracování. Vzhledem k tomu, že Service Bus označil zprávu jako spotřebovávanou, pak když aplikace znovu spustí a začne znovu přijímat zprávy, byl vynechán zprávu, která se spotřebovala před pádem vynechá.

Pokud `peek_lock` parametr je nastaven na **True**, receive stane dvoufázového operaci, která umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo spolehlivě uloží pro pozdější zpracování), dokončení druhé fáze přijetí voláním `delete` metodu **zpráva** objektu. `delete` Metoda označí zprávu jako spotřebovávanou a odstraní ji z odběru.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud přijímající aplikace nedokáže zpracovat zprávu z nějakého důvodu, pak může volat `unlock` metodu **zpráva** objektu. Tato metoda způsobí, že služba Service Bus zprávu odemkne v odběru a zpřístupní ji pro další přijetí, stejnou spotřebitelskou aplikací nebo jinou spotřebitelskou aplikací.

K dispozici je také vypršení časového limitu zpráva uzamčená v odběru a pokud se aplikaci nepodaří zprávu zpracovat před zámku vyprší časový limit (například pokud aplikace spadne), pak služby Service Bus zprávu automaticky odemkne a ji zpřístupní k přijetí.

V případě, že aplikace spadne po zpracování zprávy, ale předtím, než `delete` metoda je volána, pak bude doručit víckrát do aplikace při restartování. Toto chování se často říká. Alespoň jednou zpracování *; To znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může doručit víckrát. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. Uděláte to tak, můžete použít **MessageId** vlastnosti zprávy, která zůstává konstantní pokusu o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů

Témata a odběry jsou trvalé a musí být explicitně odstranit prostřednictvím [webu Azure portal] [ Azure portal] nebo prostřednictvím kódu programu. Následující příklad ukazuje, jak odstranit téma s názvem `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit odběr s názvem `HighMessages` z `mytopic` tématu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili základy témat sběrnice Service Bus, použijte tyto odkazy na další informace.

* Zobrazit [fronty, témata a odběry][Queues, topics, and subscriptions].
* Odkaz pro [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
