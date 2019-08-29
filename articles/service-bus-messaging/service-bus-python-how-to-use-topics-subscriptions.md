---
title: Jak používat Azure Service Bus témata s Pythonem | Microsoft Docs
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: ef0237b38c8f640c0fc4b1b1788215c8804a5cd4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141898"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Jak používat Service Bus témata a předplatná v Pythonu

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento článek popisuje, jak používat témata a odběry služby Service Bus. Ukázky jsou napsané v Pythonu a používají [balíček Azure Python SDK][Azure Python package]. Mezi zahrnuté scénáře patří:

- Vytváření témat a předplatných 
- Vytváření filtrů předplatného 
- Odesílání zpráv do tématu 
- Příjem zpráv z předplatného
- Odstraňování témat a předplatných

## <a name="prerequisites"></a>Požadavky
1. Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Postupujte podle kroků v [rychlém startu: Použijte Azure Portal k vytvoření Service Bus tématu a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md) k vytvoření Service Bus **oboru názvů** a získání připojovacího **řetězce**.

    > [!NOTE]
    > V tomto rychlém startu vytvoříte **téma** a **předplatné** k tématu pomocí **Pythonu** . 
3. Nainstalujte [balíček Azure Python][Azure Python package]. Projděte si příručku k [instalaci Pythonu](/azure/python/python-sdk-azure-install).

## <a name="create-a-topic"></a>Vytvoření tématu

Objekt **ServiceBusService** vám umožní pracovat s tématy. Do horní části každého souboru Pythonu, do kterého chcete programově přistupovat, přidejte následující kód Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Následující kód vytvoří objekt **ServiceBusService** . `mynamespace`Nahraďte `sharedaccesskeyname`, a`sharedaccesskey` skutečným oborem názvů, názvem klíče a hodnotou klíče sdíleného přístupového podpisu (SAS).

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Hodnoty pro název a hodnotu klíče SAS můžete získat z [Azure Portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

`create_topic` Metoda také podporuje další možnosti, které umožňují přepsat výchozí nastavení tématu, jako je například doba zprávy na hodnotu Live nebo maximální velikost tématu. Následující příklad nastaví maximální velikost tématu na 5 GB a hodnotu TTL (Time to Live) na 1 minutu:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Vytvoření předplatných

Předplatná pro témata jsou také vytvořena s objektem **ServiceBusService** . Odběry jsou pojmenovány a mohou mít volitelný filtr, který omezuje sadu zpráv dodaných do virtuální fronty odběru.

> [!NOTE]
> Ve výchozím nastavení jsou předplatná trvalá a budou existovat až do chvíle, kdy se odstraní ani do tématu, ke kterému se přihlásí.
> 
> Odběry můžete nechat automaticky odstranit nastavením [vlastnosti auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Vytvoření odběru s výchozím filtrem (MatchAll).

Pokud při vytvoření nového předplatného není zadaný žádný filtr, použije se filtr **MatchAll** (výchozí). Když se použije filtr **MatchAll** , všechny zprávy publikované v tématu se umístí do virtuální fronty odběru. Následující příklad vytvoří odběr s názvem `AllMessages` a použije výchozí filtr **MatchAll** .

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Vytvoření odběru s filtry

Můžete také definovat filtry, které vám umožní určit, které zprávy odeslané do tématu se mají zobrazit v rámci konkrétního předplatného tématu.

Nejpružnější typ filtru podporovaný předplatnými je **SqlFilter**, které implementuje podmnožinu SQL92. Filtry SQL pracují s vlastnostmi zpráv publikované do tématu. Další informace, které se dají použít s filtrem SQL, najdete v syntaxi [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Do předplatného můžete přidat filtry pomocí metody **\_Create Rule** objektu **ServiceBusService** . Tato metoda umožňuje přidat nové filtry do stávajícího předplatného.

> [!NOTE]
> Vzhledem k tomu, že výchozí filtr je automaticky použit pro všechna nová předplatná, musíte nejprve odebrat výchozí filtr, jinak bude **MatchAll** přepsat všechny další filtry, které můžete zadat. Výchozí pravidlo můžete odebrat pomocí `delete_rule` metody objektu **ServiceBusService** .
> 
> 

Následující příklad vytvoří odběr s názvem `HighMessages` **SqlFilter** , který vybere pouze zprávy, které mají vlastní `messagenumber` vlastnost větší než 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Podobně následující příklad vytvoří odběr s názvem `LowMessages` **SqlFilter** , který vybere pouze `messagenumber` zprávy, které mají vlastnost menší nebo rovna 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Teď `mytopic` , když se do ní pošle zpráva, se vždycky doručí příjemcům, kteří se přihlásili k předplatnému tématu **AllMessages** , a selektivně doručovat na příjemce, kteří se přihlásili k předplatnému tématu **HighMessages** a **LowMessages** . odběry (v závislosti na obsahu zprávy).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

Chcete-li odeslat zprávu do Service Bus téma, musí vaše aplikace používat `send_topic_message` metodu objektu **ServiceBusService** .

Následující příklad ukazuje, jak odeslat pět testovacích zpráv do `mytopic`. Hodnota `messagenumber` vlastnosti každé zprávy se liší v iteraci smyčky (to určuje, které odběry obdrží):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Témata Service Bus podporují maximální velikost zprávy 256 KB [na úrovni Standard](service-bus-premium-messaging.md) a 1 MB [na úrovni Premium](service-bus-premium-messaging.md). Hlavička, která obsahuje standardní a vlastní vlastnosti aplikace, může mít velikost až 64 KB. Počet zpráv držených v tématu není omezený, ale celková velikost zpráv držených v tématu omezená je. Velikost tématu se definuje při vytvoření, maximální limit je 5 GB. Další informace o kvótách najdete v tématu [Service Bus kvóty][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného

Zprávy jsou přijímány z předplatného `receive_subscription_message` pomocí metody objektu **ServiceBusService** :

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Pokud je parametr `peek_lock` nastaven na **hodnotu false**, budou zprávy z odběru odstraněny, protože jsou čteny. Můžete číst (prohlížet) a uzamknout zprávu bez jejich odstranění z fronty nastavením parametru `peek_lock` na **hodnotu true**.

Chování při čtení a odstranění zprávy jako součást operace Receive je nejjednodušší model a funguje nejlépe ve scénářích, ve kterých aplikace může tolerovat nezpracovávající zprávu, pokud dojde k selhání. Pro pochopení tohoto chování Vezměte v úvahu situaci, ve které spotřebitel vydá žádost o přijetí, a poté dojde k chybě před jejím zpracováním. Vzhledem k tomu, že Service Bus označila zprávu jako spotřebovaná, pak když se aplikace znovu spustí a začne znovu přijímat zprávy, vynechala zprávu, která byla spotřebována před selháním.

Pokud je parametr nastaven na **hodnotu true**, obdrží se operace se dvěma fázemi, což umožňuje podporovat aplikace, které nemůžou tolerovat chybějící zprávy. `peek_lock` Když Service Bus přijme požadavek, najde zprávu, která je na řadě ke spotřebování, uzamkne ji proti spotřebování jinými spotřebiteli a vrátí ji do aplikace. Poté, co aplikace dokončí zpracování zprávy (nebo je uloží spolehlivě pro budoucí zpracování), dokončí druhou fázi procesu příjmu voláním `delete` metody objektu **Message** . `delete` Metoda označí zprávu jako spotřebou a odebere ji z předplatného.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Zpracování pádů aplikace a nečitelných zpráv

Service Bus poskytuje funkce, které vám pomůžou se elegantně zotavit z chyb v aplikaci nebo vyřešit potíže se zpracováním zprávy. Pokud aplikace příjemce z nějakého důvodu nemůže zprávu zpracovat, může volat `unlock` metodu objektu **zprávy** . Tato metoda způsobí, že Service Bus odemkne zprávu v rámci předplatného a zpřístupní ji pro opětovné přijetí, a to buď stejnou spotřebou aplikace, nebo jinou nenáročné aplikací.

Je také časový limit přidružený ke zprávě uzamčený v rámci předplatného a pokud aplikace nedokáže zpracovat zprávu před vypršením časového limitu zámku (například pokud aplikace selže), Service Bus automaticky odemkne zprávu. zpřístupňuje je, aby je bylo možné znovu přijmout.

V případě, že dojde k chybě aplikace po zpracování zprávy, ale před `delete` zavoláním metody, bude zpráva doručena do aplikace při restartu. Toto chování se často nazývá. Alespoň po zpracování\*; to znamená, že každá zpráva se zpracuje alespoň jednou, ale v některých situacích může být stejná zpráva doručena znovu. Pokud daný scénář nemůže tolerovat zpracování víc než jednou, vývojáři aplikace by měli přidat další logiku navíc pro zpracování víckrát doručené zprávy. K tomu můžete použít vlastnost **MessageID** zprávy, která zůstává konstantní při pokusů o doručení.

## <a name="delete-topics-and-subscriptions"></a>Odstranění témat a odběrů

Témata a odběry jsou trvalé, pokud není nastavena [vlastnost auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) . Je možné je odstranit pomocí [Azure Portal][Azure portal] nebo programově. Následující příklad ukazuje, jak odstranit téma s názvem `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Pokud se odstraní téma, odstraní se i všechny odběry registrované k tomuto tématu. Odběry se taky dají odstranit samostatně. Následující kód ukazuje, jak odstranit předplatné s názvem `HighMessages` `mytopic` z tématu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy Service Bus témata, získáte další informace na následujících odkazech.

* Viz [fronty, témata a odběry][Queues, topics, and subscriptions].
* Reference pro [SqlFilter. syntaxi][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
