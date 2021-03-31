---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900848"
---
Azure Functions umožňuje vytvářet úlohy replikace pouze pro konfiguraci, které jsou zabudovány na předem připraveném vstupním bodu. [Ukázky replikace založené na konfiguraci pro Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) ilustrují, jak využít [předem připravené](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) obslužné rutiny ve vlastním kódu nebo vyhnout se neplnění kódu a jenom používat konfiguraci.

## <a name="create-a-replication-task"></a>Vytvořit úlohu replikace
Chcete-li vytvořit takovou úlohu replikace, nejprve vytvořte novou složku pod složkou projektu. Název nové složky je název funkce, například `europeToAsia` nebo `telemetry` . Název nemá žádnou přímou korelaci s použitými entitami pro zasílání zpráv a slouží pouze k jejich identifikaci. Ve stejném projektu můžete vytvořit desítky funkcí.

Dále `function.json` ve složce vytvořte soubor. Soubor nakonfiguruje funkci. Začněte s následujícím obsahem:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

V tomto souboru je nutné provést tři kroky konfigurace, které závisí na tom, které entity chcete připojit:

1. [Konfigurace směrování vstupu](#configure-the-input-direction)
2. [Nakonfigurovat směr výstupu](#configure-the-output-direction)
3. [Konfigurace vstupního bodu](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Konfigurace směrování vstupu

#### <a name="event-hub-input"></a>Vstup centra událostí

Pokud chcete přijímat události z centra událostí, přidejte informace o konfiguraci do horní části v části "vazby", které nastaví

* **Type** – typ "eventHubTrigger".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec centra událostí. 
* **eventHubName** – název centra událostí v rámci oboru názvů identifikovaného připojovacím řetězcem.
* skupina **uživatelů** – název skupiny příjemců. Je třeba mít na paměti, že název je uzavřen v symbolech procenta, a proto odkazuje také na hodnotu nastavení aplikace.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Vstup z fronty Service Bus

Pokud chcete přijímat události z fronty Service Bus, přidejte informace o konfiguraci do horní části v části "vazby", které nastaví

* **Type** – typ "serviceBusTrigger".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec Service Bus.
* **Queue** – název fronty Service Bus v rámci oboru názvů identifikovaného připojovacím řetězcem.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Vstup Service Bus tématu

Pokud chcete přijímat události z Service Bus tématu, přidejte informace o konfiguraci do horní části v části "vazby", které nastaví

* **Type** – typ "serviceBusTrigger".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec Service Bus. Tato hodnota musí být, `{FunctionName}-source-connection` Pokud chcete použít poskytnuté skripty.
* název **tématu** – název Service Busho tématu v rámci oboru názvů identifikovaného připojovacím řetězcem.
* název **předplatného** – název předplatného Service Bus na daném tématu v rámci oboru názvů identifikovaného připojovacím řetězcem.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Nakonfigurovat směr výstupu

#### <a name="event-hub-output"></a>Výstup centra událostí

Pokud chcete předávané události do centra událostí, přidejte informace o konfiguraci do dolní části v části "vazby", které nastaví

* **Type** – typ "eventHub".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec centra událostí. 
* **eventHubName** – název centra událostí v rámci oboru názvů identifikovaného připojovacím řetězcem.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Výstup fronty Service Bus

Pokud chcete předávané události do fronty Service Bus, přidejte informace o konfiguraci do dolní části v části "vazby", které nastaví

* **Type** – typ "serviceBus".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec Service Bus. 
* **Queue** – název fronty Service Bus v rámci oboru názvů identifikovaného připojovacím řetězcem.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Výstup tématu Service Bus

Pokud chcete události přeposílány do Service Bus tématu, přidejte informace o konfiguraci do dolní části v části "vazby", které nastaví

* **Type** – typ "serviceBus".
* **připojení** – název hodnoty nastavení aplikace pro připojovací řetězec Service Bus. 
* název **tématu** – název Service Busho tématu v rámci oboru názvů identifikovaného připojovacím řetězcem.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Konfigurace vstupního bodu

Konfigurace vstupního bodu vybere jeden ze standardních úloh replikace. Pokud upravujete `Azure.Messaging.Replication` projekt, můžete také přidat úkoly a odkazovat na ně zde. Například:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

Následující tabulka poskytuje správné hodnoty pro kombinace zdrojů a cílů:

| Zdroj      | Cíl      | Vstupní bod 
|-------------|-------------|------------------------------------------------------------------------
| Centrum událostí   | Centrum událostí   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centrum událostí   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centrum událostí   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Zásady opakování

Pokud chcete nakonfigurovat zásady opakování, přečtěte si informace o [Azure Functions v dokumentaci k pokusům o](../articles/azure-functions/functions-bindings-error-pages.md) opakování. Nastavení zásad zvolené v rámci projektů v tomto úložišti konfigurují exponenciální strategii omezení rychlosti s intervaly opakování 5 sekund až 5 minut a nekonečné opakování, aby nedošlo ke ztrátě dat.

Pokud Service Bus, přečtěte si část ["použití možnosti opakování v horní části odolnosti triggeru"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) , abyste pochopili interakci triggerů a maximální počet doručení definovaný pro frontu.

### <a name="build-deploy-and-configure"></a>Sestavování, nasazování a konfigurace

I když se zaměříte na konfiguraci, úlohy stále vyžadují sestavení nasaditelné aplikace a konfiguraci Azure Functions hostitelů tak, aby měly všechny požadované informace pro připojení k daným koncovým bodům. 

To je znázorněno společně s opakovaně použitelnými skripty v [ukázkách replikace na základě konfigurace pro Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).