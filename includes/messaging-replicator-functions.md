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
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900849"
---
## <a name="what-is-a-replication-task"></a>Co je úloha replikace?

Úloha replikace přijímá události ze zdroje a přesměruje je do cíle.
Většina úloh replikace přesměruje události beze změny a provede mapování mezi strukturami metadat, pokud se liší zdrojový a cílový protokol. 

Úlohy replikace jsou obecně bezstavové, což znamená, že nesdílejí stav ani jiné vedlejší účinky v rámci sekvenčního nebo paralelního provádění úlohy. To platí také pro dávkování a zřetězení, které mohou být implementovány nad stávajícím stavem datového proudu. 

To umožňuje, aby se úlohy replikace lišily od úloh agregace, které jsou všeobecně stavové a jsou doménou analytických rozhraní a služeb, jako je [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Aplikace a úlohy replikace v Azure Functions

V Azure Functions se úloha replikace implementuje pomocí [triggeru](../articles/azure-functions/functions-triggers-bindings.md) , který získá jednu nebo více vstupních zpráv z nakonfigurovaného zdroje a [výstupní vazbu](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) , která přesměruje zprávy zkopírované ze zdroje do konfigurovaného cíle. 

| Trigger  | Výstup |
|----------|--------|
| [Aktivační událost služby Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Výstupní vazba centra událostí Azure](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Aktivační událost Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Výstupní vazba Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Aktivační událost služby Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Výstupní vazba Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Aktivační událost Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Výstupní vazba Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Trigger Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Výstupní vazba Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Aktivační událost Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Výstupní vazba Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Aktivační událost RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Výstupní vazba RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Výstupní vazba Azure Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Výstupní vazba služby signalizace Azure](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Výstupní vazba Twilio SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

Úlohy replikace se nasazují jako do replikační aplikace přes stejné metody nasazení jako jakékoli jiné aplikace Azure Functions. Do stejné aplikace můžete nakonfigurovat několik úloh. 

S Azure Functions Premium můžou více replikačních aplikací sdílet stejný podkladový fond zdrojů nazvaný App Service plán. To znamená, že můžete snadno společné umístění úlohy replikace napsané v rozhraní .NET s úlohami replikace, které jsou napsané v jazyce Java. To se projeví v případě, že chcete využít výhod konkrétních knihoven, jako je Apache ve stylu CamelCase, které jsou k dispozici pouze pro jazyk Java, a pokud se jedná o nejlepší možnost pro konkrétní integrační cestu, a to i v případě, že byste při ostatních úlohách replikace obvykle dávali jiný jazyk a modul runtime. 

Kdykoli je to možné, měli byste preferovat triggery orientované na dávku přes triggery, které dodávají jednotlivé události nebo zprávy, a měli byste vždycky získat úplnou strukturu události nebo zprávy, ale nemusíte spoléhat na [výrazy vazby parametrů](../articles/azure-functions/functions-bindings-expressions-patterns.md)funkce Azure Functions.

Název funkce by měl odrážet dvojici zdroje a cíle, ke kterému se připojujete, a měli byste předponu odkazů na připojovací řetězce nebo jiné prvky konfigurace v konfiguračních souborech aplikace s tímto názvem. 

### <a name="data-and-metadata-mapping"></a>Mapování dat a metadat

Jakmile se rozhodnete pro pár vstupních triggerů a výstupní vazby, budete muset provést mapování mezi různými typy událostí nebo zpráv, pokud není typ triggeru a výstup stejný.

Pro úlohy jednoduché replikace, které kopírují zprávy mezi Event Hubs a Service Bus, nemusíte psát vlastní kód, ale můžete ho začínat pomocí [knihovny nástrojů, která je součástí](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) těchto ukázek replikace.

### <a name="retry-policy"></a>Zásady opakování

Aby se zabránilo ztrátě dat během události dostupnosti na kterékoli straně funkce replikace, je nutné nakonfigurovat zásady opakování, aby byly robustní. Pokud chcete nakonfigurovat zásady opakování, přečtěte si informace o [Azure Functions v dokumentaci k pokusům o](../articles/azure-functions/functions-bindings-error-pages.md) opakování. 

Nastavení zásad zvolené pro ukázkové projekty v [ukázkovém úložišti](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) konfigurují exponenciální strategii omezení rychlosti s intervaly opakování od 5 sekund do 15 minut a nekonečné opakování, aby se předešlo ztrátě dat. 

Pokud Service Bus, přečtěte si část ["použití možnosti opakování v horní části odolnosti triggeru"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) , abyste pochopili interakci triggerů a maximální počet doručení definovaný pro frontu.

### <a name="setting-up-a-replication-application-host"></a>Nastavení hostitele replikační aplikace

Aplikace replikace je hostitel spouštění pro jednu nebo více úloh replikace. 

Jedná se o Azure Functions aplikaci, která je nakonfigurovaná tak, aby běžela buď v plánu spotřeby, nebo (doporučeno) na plánu Azure Functions Premium. Všechny replikační aplikace musí běžet v rámci [spravované identity přiřazené systémem nebo uživatelem](../articles/app-service/overview-managed-identity.md). 

Šablony propojeného Azure Resource Manager (ARM) vytvoří a nakonfigurují replikační aplikaci pomocí:

* účet Azure Storage pro sledování průběhu replikace a pro protokoly,
* spravovaná identita přiřazená systémem a 
* Monitorování Azure a integrace Application Insights pro monitorování.

Replikační aplikace, které musí mít přístup Event Hubs vázaný na virtuální síť Azure (VNet), musí používat plán Azure Functions Premium a musí být nakonfigurované pro připojení ke stejné virtuální síti, což je také jedna z dostupných možností.


|       | Nasazení | Vizualizace  
|-------|------------------|--------------|---------------|
| **Plán spotřeby Azure Functions** | [![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Vizualizace](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Plán Azure Functions Premium** |[![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Vizualizace](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Plán Azure Functions Premium s virtuální sítí** | [![Nasadit do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Vizualizace](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Příklady

[Úložiště ukázek](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) obsahuje několik příkladů úloh replikace, které kopírují události mezi Event Hubs a/nebo mezi entitami Service Bus.

Pro kopírování událostí mezi Event Hubs použijte Trigger centra událostí s výstupní vazbou centra událostí:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Pro kopírování zpráv mezi Service Bus entitami se používá aktivační událost Service Bus a výstupní vazba:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Pomocné metody umožňují snadnou replikaci mezi Event Hubs a Service Bus:

| Zdroj      | Cíl      | Vstupní bod 
|-------------|-------------|------------------------------------------------------------------------
| Centrum událostí   | Centrum událostí   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centrum událostí   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centrum událostí   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Sledování

Informace o tom, jak můžete monitorovat aplikaci pro replikaci, najdete v [části monitorování](../articles/azure-functions/configure-monitoring.md) v dokumentaci k Azure Functions.

Zvlášť užitečným vizuálním nástrojem pro monitorování úloh replikace je Application Insights [Mapa aplikace](../articles/azure-monitor/app/app-map.md), která se automaticky vygenerovala z informací o zaznamenaném monitorování a umožňuje prozkoumat spolehlivost a výkon zdrojů a cílových přenosů úlohy replikace.

Pro okamžité diagnostické poznatky můžete pracovat s nástrojem [Live Metrics](../articles/azure-monitor/app/live-stream.md) Portal, který poskytuje vizualizaci podrobností protokolu s nízkou latencí.

## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Functions](../articles/azure-functions/functions-deployment-technologies.md)
* [Diagnostika Azure Functions](../articles/azure-functions/functions-diagnostics.md)
* [Možnosti Azure Functions sítě](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)