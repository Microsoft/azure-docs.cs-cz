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
ms.openlocfilehash: 279a00a6146d756e6a518dbf86b88f471d170b3a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805661"
---
## <a name="what-is-a-replication-task"></a>Co je úloha replikace?

Úloha replikace přijímá události ze zdroje a přesměruje je do cíle.
Většina úloh replikace přesměruje události beze změny a provede mapování mezi strukturami metadat, pokud se liší zdrojový a cílový protokol. 

Úlohy replikace jsou obecně bezstavové, což znamená, že nesdílejí stav ani jiné vedlejší účinky v rámci sekvenčního nebo paralelního provádění úlohy. To platí také pro dávkování a zřetězení, které mohou být implementovány nad stávajícím stavem datového proudu. 

To umožňuje, aby se úlohy replikace lišily od úloh agregace, které jsou všeobecně stavové a jsou doménou analytických rozhraní a služeb, jako je [Azure Stream Analytics](/azure/stream-analytics/stream-analytics-introduction).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Aplikace a úlohy replikace v Azure Functions

V Azure Functions se úloha replikace implementuje pomocí [triggeru](/azure/azure-functions/functions-triggers-bindings) , který získá jednu nebo více vstupních zpráv z nakonfigurovaného zdroje a [výstupní vazbu](/azure/azure-functions/functions-triggers-bindings#binding-direction) , která přesměruje zprávy zkopírované ze zdroje do konfigurovaného cíle. 

| Trigger  | Výstup |
|----------|--------|
| [Aktivační událost služby Azure Event Hubs](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Výstupní vazba centra událostí Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Aktivační událost Azure Service Bus](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Výstupní vazba Azure Service Bus](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Aktivační událost služby Azure IoT Hub](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Výstupní vazba Azure IoT Hub](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Aktivační událost Azure Event Grid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Výstupní vazba Azure Event Grid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Trigger Azure Queue Storage](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Výstupní vazba Azure Queue Storage](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Aktivační událost Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Výstupní vazba Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Aktivační událost RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Výstupní vazba RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Výstupní vazba Azure Notification Hubs](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Výstupní vazba služby signalizace Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Výstupní vazba Twilio SendGrid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

Úlohy replikace se nasazují jako do replikační aplikace přes stejné metody nasazení jako jakékoli jiné aplikace Azure Functions. Do stejné aplikace můžete nakonfigurovat několik úloh. 

S Azure Functions Premium můžou více replikačních aplikací sdílet stejný podkladový fond zdrojů nazvaný App Service plán. To znamená, že můžete snadno společné umístění úlohy replikace napsané v rozhraní .NET s úlohami replikace, které jsou napsané v jazyce Java. To se projeví v případě, že chcete využít výhod konkrétních knihoven, jako je Apache ve stylu CamelCase, které jsou k dispozici pouze pro jazyk Java, a pokud se jedná o nejlepší možnost pro konkrétní integrační cestu, a to i v případě, že byste při ostatních úlohách replikace obvykle dávali jiný jazyk a modul runtime. 

Kdykoli je to možné, měli byste preferovat triggery orientované na dávku přes triggery, které dodávají jednotlivé události nebo zprávy, a měli byste vždycky získat úplnou strukturu události nebo zprávy, ale nemusíte spoléhat na [výrazy vazby parametrů](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)funkce Azure Functions.

Název funkce by měl odrážet dvojici zdroje a cíle, ke kterému se připojujete, a měli byste předponu odkazů na připojovací řetězce nebo jiné prvky konfigurace v konfiguračních souborech aplikace s tímto názvem. 

### <a name="data-and-metadata-mapping"></a>Mapování dat a metadat

Jakmile se rozhodnete pro pár vstupních triggerů a výstupní vazby, budete muset provést mapování mezi různými typy událostí nebo zpráv, pokud není typ triggeru a výstup stejný.

Pro úlohy jednoduché replikace, které kopírují zprávy mezi Event Hubs a Service Bus, nemusíte psát vlastní kód, ale můžete ho začínat pomocí [knihovny nástrojů, která je součástí](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) těchto ukázek replikace.

### <a name="retry-policy"></a>Zásady opakování

Aby se zabránilo ztrátě dat během události dostupnosti na kterékoli straně funkce replikace, je nutné nakonfigurovat zásady opakování, aby byly robustní. Pokud chcete nakonfigurovat zásady opakování, přečtěte si informace o [Azure Functions v dokumentaci k pokusům o](/azure/azure-functions/functions-bindings-error-pages) opakování. 

Nastavení zásad zvolené pro ukázkové projekty v [ukázkovém úložišti](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) konfigurují exponenciální strategii omezení rychlosti s intervaly opakování od 5 sekund do 15 minut a nekonečné opakování, aby se předešlo ztrátě dat. 

Pokud Service Bus, přečtěte si část ["použití možnosti opakování v horní části odolnosti triggeru"](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) , abyste pochopili interakci triggerů a maximální počet doručení definovaný pro frontu.

### <a name="setting-up-a-replication-application-host"></a>Nastavení hostitele replikační aplikace

Aplikace replikace je hostitel spouštění pro jednu nebo více úloh replikace. 

Jedná se o Azure Functions aplikaci, která je nakonfigurovaná tak, aby běžela buď v plánu spotřeby, nebo (doporučeno) na plánu Azure Functions Premium. Všechny replikační aplikace musí běžet v rámci [spravované identity přiřazené systémem nebo uživatelem](/azure/app-service/overview-managed-identity). 

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


### <a name="monitoring"></a>Monitorování

Informace o tom, jak můžete monitorovat aplikaci pro replikaci, najdete v [části monitorování](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) v dokumentaci k Azure Functions.

Zvlášť užitečným vizuálním nástrojem pro monitorování úloh replikace je Application Insights [Mapa aplikace](https://docs.microsoft.com/azure/azure-monitor/app/app-map), která se automaticky vygenerovala z informací o zaznamenaném monitorování a umožňuje prozkoumat spolehlivost a výkon zdrojů a cílových přenosů úlohy replikace.

Pro okamžité diagnostické poznatky můžete pracovat s nástrojem [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) Portal, který poskytuje vizualizaci podrobností protokolu s nízkou latencí.

## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Functions](/azure/azure-functions/functions-deployment-technologies)
* [Diagnostika Azure Functions](/azure/azure-functions/functions-diagnostics)
* [Možnosti Azure Functions sítě](/azure/azure-functions/functions-networking-options)
* [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)