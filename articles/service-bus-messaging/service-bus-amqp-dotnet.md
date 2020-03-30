---
title: Azure Service Bus s rozhraními .NET a AMQP 1.0 | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat Azure Service Bus z aplikace .NET pomocí AMQP (Advanced Messaging Queuing Protocol).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297657"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Použití sběrnice Service Bus z rozhraní .NET s amqp 1.0

Podpora AMQP 1.0 je k dispozici v balíčku Service Bus verze 2.1 nebo novějším. Můžete zajistit, že máte nejnovější verzi stažením bitů service bus z [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurace aplikací .NET pro použití protokolu AMQP 1.0

Ve výchozím nastavení komunikuje klientská knihovna Service Bus .NET se službou Service Bus pomocí vyhrazeného protokolu založeného na protokolu SOAP. Použití protokolu AMQP 1.0 namísto výchozího protokolu vyžaduje explicitní konfiguraci v připojovacím řetězci služby Service Bus, jak je popsáno v další části. Kromě této změny zůstane kód aplikace při použití amqp 1.0 beze změny.

V aktuální verzi existuje několik funkcí rozhraní API, které nejsou podporovány při použití AMQP. Tyto nepodporované funkce jsou [uvedeny](#behavioral-differences)v části Behaviorální rozdíly . Některé z pokročilých nastavení konfigurace mají také jiný význam při použití AMQP.

### <a name="configuration-using-appconfig"></a>Konfigurace pomocí souboru App.config

Je vhodné, aby aplikace používaly konfigurační soubor App.config k ukládání nastavení. Pro aplikace service bus můžete použít App.config k uložení připojovacího řetězce Service Bus. Příklad souboru App.config je následující:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Hodnota `Microsoft.ServiceBus.ConnectionString` nastavení je připojovací řetězec Service Bus, který slouží ke konfiguraci připojení ke službě Service Bus. Formát je následující:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Kde `namespace` `SAS key` a jsou získány z [portálu Azure][Azure portal] při vytváření oboru názvů Service Bus. Další informace najdete [v tématu Vytvoření oboru názvů Service Bus pomocí portálu Azure][Create a Service Bus namespace using the Azure portal].

Při použití programu AMQP připojte připojovací řetězec s programem `;TransportType=Amqp`. Tento zápis dává klientům pokyn, aby se klientská knihovna spojila se službou Service Bus pomocí technologie AMQP 1.0.

## <a name="message-serialization"></a>Serializace zprávy

Při použití výchozího protokolu je výchozím chováním serializace klientské knihovny .NET použití typu [DataContractSerializer][DataContractSerializer] k serializaci instance [BrokeredMessage][BrokeredMessage] pro přenos mezi klientskou knihovnou a službou Service Bus. Při použití režimu přenosu AMQP používá klientská knihovna systém typu AMQP pro serializaci [zprostředkované zprávy][BrokeredMessage] do zprávy AMQP. Tato serializace umožňuje zprávu přijímat a interpretovat přijímající aplikace, která je potenciálně spuštěna na jiné platformě, například java aplikace, která používá rozhraní API JMS pro přístup k service bus.

Při vytváření [BrokeredMessage][BrokeredMessage] instance, můžete poskytnout objekt .NET jako parametr konstruktoru sloužit jako tělo zprávy. U objektů, které lze mapovat na primitivní typy AMQP, je tělo serializováno do datových typů AMQP. Pokud objekt nelze přímo mapovat na primitivní typ AMQP; to znamená, že vlastní typ definovaný aplikací, pak je objekt serializován pomocí [DataContractSerializer][DataContractSerializer]a serializované bajty jsou odesílány v datové zprávě AMQP.

Chcete-li usnadnit interoperabilitu s non-.NET klienty, používejte pouze typy .NET, které lze serializovat přímo do typů AMQP pro text zprávy. V následující tabulce jsou uvedeny tyto typy a odpovídající mapování na systém typu AMQP.

| Textový typ objektu rozhraní .NET | Mapovaný typ AMQP | Typ řezu Tělo AMQP |
| --- | --- | --- |
| bool |Boolean |Hodnota AMQP |
| byte |ubyte |Hodnota AMQP |
| ushort |ushort |Hodnota AMQP |
| uint |uint |Hodnota AMQP |
| ulong |ulong |Hodnota AMQP |
| sbyte |byte |Hodnota AMQP |
| short |short |Hodnota AMQP |
| int |int |Hodnota AMQP |
| long |long |Hodnota AMQP |
| float |float |Hodnota AMQP |
| double |double |Hodnota AMQP |
| decimal |desetinné číslo128 |Hodnota AMQP |
| char |char |Hodnota AMQP |
| DateTime |časové razítko |Hodnota AMQP |
| Identifikátor GUID |Uuid |Hodnota AMQP |
| bajt[] |binární |Hodnota AMQP |
| řetězec |řetězec |Hodnota AMQP |
| System.collections.ilist |list |AMQP Hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. |
| System.array |pole |AMQP Hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. |
| System.collections.idictionary |map |AMQP Hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. Poznámka: Jsou podporovány pouze klávesy String. |
| Uri |Popsaný řetězec (viz následující tabulka) |Hodnota AMQP |
| DateTimeOffset |Popsané long (viz následující tabulka) |Hodnota AMQP |
| TimeSpan |Popsáno dlouho (viz následující) |Hodnota AMQP |
| Datový proud |binární |AMQP data (může být vícenásobná). Části Data obsahují nezpracované bajty přečtené z objektu Stream. |
| Jiný objekt |binární |AMQP data (může být vícenásobná). Obsahuje serializovaný binární objekt, který používá DataContractSerializer nebo serializátor dodaný aplikací. |

| Typ .NET | Mapovaný typ popsaný amqp | Poznámky |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Rozdíly v chování

Existují některé malé rozdíly v chování rozhraní SERVICE BUS .NET API při použití AMQP, ve srovnání s výchozím protokolem:

* Vlastnost [OperationTimeout][OperationTimeout] je ignorována.
* `MessageReceiver.Receive(TimeSpan.Zero)`je implementována jako `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Dokončení zprávy tokeny zámku lze provést pouze příjemci zpráv, které původně obdržel zprávy.

## <a name="control-amqp-protocol-settings"></a>Řízení nastavení protokolu AMQP

Rozhraní [API ROZHRANÍ .NET zveřejňují](/dotnet/api/) několik nastavení pro řízení chování protokolu AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Řídí počáteční kredit použitý pro propojení. Výchozí hodnota je 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Řídí maximální velikost rámce AMQP nabízené během vyjednávání v době otevření připojení. Výchozí hodnota je 65 536 bajtů.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Pokud jsou přenosy dávkovatelné, tato hodnota určuje maximální zpoždění pro odesílání dispozic. Ve výchozím nastavení zděděno odesílateli/příjemci. Individuální odesílatel/příjemce může přepsat výchozí, což je 20 milisekund.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Určuje, zda jsou připojení AMQP vytvořena prostředpou připojení TLS. Výchozí hodnota je **true**.

## <a name="next-steps"></a>Další kroky

Jste připraveni na další informace? Navštivte následující odkazy:

* [Přehled služby AMQP sběrnice]
* [Průvodce protokolem AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Přehled služby AMQP sběrnice]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md

