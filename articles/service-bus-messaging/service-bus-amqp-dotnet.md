---
title: Azure Service Bus s .NET a AMQP 1.0 | Dokumentace Microsoftu
description: Pomocí služby Azure Service Bus z .NET pomocí AMQP
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: f5713fe3333f291d8d28a6ef3df48572507661be
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853200"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Použití služby Service Bus z .NET pomocí protokolu AMQP 1.0

Podpora AMQP 1.0 je k dispozici v balíčku verze služby Service Bus 2.1 nebo vyšší. Zajistíte si nejnovější verzi stahování bits služby Service Bus z [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurace aplikace .NET pomocí protokolu AMQP 1.0

Ve výchozím nastavení klientské knihovny .NET služby Service Bus komunikuje se službou Service Bus pomocí vyhrazené protokol založený na protokolu SOAP. Použití AMQP 1.0 místo výchozího protokol vyžaduje explicitní konfiguraci připojovací řetězec sběrnice služeb, jak je popsáno v další části. Než tuto změnu zůstane beze změny kódu aplikace, při použití protokolu AMQP 1.0.

V aktuální verzi existuje několik funkcí rozhraní API, které nejsou podporována při použití protokolu AMQP. Tyto nepodporované funkce jsou uvedené v části [rozdílného](#behavioral-differences). Některá nastavení pokročilou konfiguraci také mají různý význam při použití protokolu AMQP.

### <a name="configuration-using-appconfig"></a>Konfigurace pomocí souboru App.config

Je vhodné pro aplikace pro použití k ukládání nastavení konfiguračního souboru App.config. Pro aplikace Service Bus můžete v souboru App.config uložit připojovací řetězec služby Service Bus. Příklad souboru App.config vypadá takto:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Hodnota `Microsoft.ServiceBus.ConnectionString` nastavení je připojovací řetězec služby Service Bus, který se používá ke konfiguraci připojení k Service Bus. Formát je následujícím způsobem:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Kde `namespace` a `SAS key` jsou získávány z [webu Azure portal] [ Azure portal] při vytváření oboru názvů služby Service Bus. Další informace najdete v tématu [vytvoření oboru názvů služby Service Bus pomocí webu Azure portal][Create a Service Bus namespace using the Azure portal].

Při použití AMQP, připojte řetězec připojení s `;TransportType=Amqp`. Tento typ notation instruuje klientské knihovny, aby jeho připojení k Service Bus pomocí protokolu AMQP 1.0.

## <a name="message-serialization"></a>Serializace zprávy

Při použití výchozího protokolu, je použít výchozí chování serializace Klientská knihovna pro .NET [DataContractSerializer] [ DataContractSerializer] typ k serializaci [BrokeredMessage] [ BrokeredMessage] instance pro přenos mezi knihovnou klienta a služby Service Bus. Když používáte režim přenosu protokolu AMQP, knihovna klienta používá systém typů AMQP za účelem serializace [zprostředkované zprávy] [ BrokeredMessage] do zprávy protokolu AMQP. Serializace umožňuje zprávy přijímají a interpretovat přijímající aplikace, na kterém běží potenciálně na různé platformy, například aplikace v Javě, která používá rozhraní API pro JMS pro přístup k Service Bus.

Při sestavování [BrokeredMessage] [ BrokeredMessage] instance, můžete zadat objekt .NET jako parametr do konstruktoru, který bude sloužit jako text zprávy. Pro objekty, které je možné mapovat na primitivní typy AMQP je serializován text do AMQP datové typy. Pokud objekt nelze mapovat přímo do AMQP primitivní typ; To znamená, vlastní typ definován aplikací a pak je objekt serializován pomocí [DataContractSerializer][DataContractSerializer], a odesílání bajtů serializovaná data zprávy protokolu AMQP.

Pro usnadnění vzájemná funkční spolupráce s klienty než .NET, používají jenom typy .NET, které lze serializovat přímo do AMQP typů pro text zprávy. Následující tabulka obsahuje podrobnosti o těchto typů a odpovídající mapování do systému typů AMQP.

| Typ objektu text .NET | Typ mapované AMQP | Typ oddílu textu AMQP |
| --- | --- | --- |
| BOOL |Boolean |Hodnota AMQP |
| byte |ubyte |Hodnota AMQP |
| ushort |ushort |Hodnota AMQP |
| uint |uint |Hodnota AMQP |
| ulong |ulong |Hodnota AMQP |
| SByte |byte |Hodnota AMQP |
| krátké |krátké |Hodnota AMQP |
| int |int |Hodnota AMQP |
| Long |Long |Hodnota AMQP |
| float |float |Hodnota AMQP |
| double |double |Hodnota AMQP |
| decimal |decimal128 |Hodnota AMQP |
| Char |Char |Hodnota AMQP |
| DateTime |časové razítko |Hodnota AMQP |
| Guid |Uuid |Hodnota AMQP |
| Byte |Binární |Hodnota AMQP |
| řetězec |řetězec |Hodnota AMQP |
| System.Collections.IList |list |Hodnota AMQP: položky obsažené v kolekci lze pouze ty, které jsou definovány v této tabulce. |
| System.Array |pole |Hodnota AMQP: položky obsažené v kolekci lze pouze ty, které jsou definovány v této tabulce. |
| System.Collections.IDictionary |map |Hodnota AMQP: položky obsažené v kolekci lze pouze ty, které jsou definovány v této tabulce. Poznámka: jsou podporovány pouze řetězec klíče. |
| URI |Popisuje řetězec (viz následující tabulka) |Hodnota AMQP |
| DateTimeOffset |Popisuje dlouho (viz následující tabulka) |Hodnota AMQP |
| Časový interval |Popisuje dlouho (viz následující) |Hodnota AMQP |
| Datový proud |Binární |Data protokolu AMQP (může být více). Části Data obsahují nezpracované bajty přečtené z objektu Stream. |
| Druhý objekt |Binární |Data protokolu AMQP (může být více). Obsahuje serializovaná binární soubor objektu, který používá objektu DataContractSerializer nebo serializátor poskytnuté aplikací. |

| Typ formátu .NET | Mapovaná AMQP popsané typu | Poznámky |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| Časový interval |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Behaviorální rozdíly

Při použití AMQP v porovnání s výchozím protokolem existují určité malé rozdíly v chování rozhraní .NET API služby Service Bus:

* [OperationTimeout] [ OperationTimeout] vlastnost se ignoruje.
* `MessageReceiver.Receive(TimeSpan.Zero)` je implementován jako `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Dokončení zprávy pomocí tokenů uzamčení lze provést pouze spuštěné přijímače zpracovaly zprávy, které původně přijaté zprávy.

## <a name="control-amqp-protocol-settings"></a>Nastavení protokolu AMQP ovládacího prvku

[Rozhraní .NET API](/dotnet/api/) vystavit několik nastavení, které řídí chování protokolu AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Určuje počáteční kredit použitý pro odkaz. Výchozí hodnota je 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Ovládací prvky maximální velikost rámce AMQP nabízelo pro vyjednávání při připojení otevřete čas. Výchozí hodnota je 65 536 bajty.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Pokud přenosy batchable, tato hodnota určuje maximální zpoždění pro odesílání potížemi. Ve výchozím nastavení dědí odesílatele a příjemce. Jednotlivé odesílatele a příjemce může přepsat výchozí, což je 20 MS.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Určuje, zda připojení AMQP jsou navázat připojení přes protokol SSL. Výchozí hodnota je **true**.

## <a name="next-steps"></a>Další postup

Jste připraveni na další informace? Naleznete pod těmito odkazy:

* [Přehled protokolu AMQP Service Bus]
* [Průvodce protokolem AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Přehled protokolu AMQP Service Bus]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md

