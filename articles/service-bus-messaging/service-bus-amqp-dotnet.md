---
title: Azure Service Bus s .NET a AMQP 1,0 | Microsoft Docs
description: Tento článek popisuje, jak použít Azure Service Bus z aplikace .NET pomocí AMQP (rozšířený protokol řízení front zpráv).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632846"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Použití Service Bus z rozhraní .NET s AMQP 1,0

Podpora AMQP 1,0 je k dispozici v balíčku Service Bus verze 2,1 nebo novější. Nejnovější verzi si můžete ověřit stažením Service Bus bitů z [nugetu][NuGet].

> [!NOTE]
> V knihovně .NET pro Service Bus můžete použít buď rozšířený protokol řízení front zpráv (AMQP) (AMQP), nebo protokol SBMP (Service Bus Messaging Protocol). AMQP je výchozí protokol používaný knihovnou .NET. Doporučujeme, abyste použili Protokol AMQP (což je výchozí nastavení) a nepřepíšete ho. 

## <a name="configure-net-applications-to-use-amqp-10"></a>Konfigurace aplikací .NET pro použití AMQP 1,0

Ve výchozím nastavení komunikuje Klientská knihovna Service Bus .NET se službou Service Bus pomocí protokolu AMQP. Jako typ přenosu můžete také explicitně zadat AMQP, jak je znázorněno v následující části. 

V aktuální verzi je k dispozici několik funkcí rozhraní API, které se při použití AMQP nepodporují. Tyto nepodporované funkce jsou uvedené v části [rozdíly v chování](#behavioral-differences). Některá z pokročilých nastavení konfigurace mají také při použití AMQP jiný význam.

### <a name="configuration-using-appconfig"></a>Konfigurace pomocí App.config

Je vhodné, aby aplikace používaly konfigurační soubor App.config k uložení nastavení. U Service Busch aplikací můžete použít App.config k uložení připojovacího řetězce Service Bus. Příkladem App.config souboru je následující:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Hodnota `Microsoft.ServiceBus.ConnectionString` nastavení je Service Bus připojovací řetězec, který se používá ke konfiguraci připojení k Service Bus. Formát je následující:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Kde `namespace` a `SAS key` jsou získány z [Azure Portal][Azure portal] při vytváření oboru názvů Service Bus. Další informace najdete v tématu [Vytvoření oboru názvů Service Bus pomocí Azure Portal][Create a Service Bus namespace using the Azure portal].

Při použití AMQP připojit připojovací řetězec k `;TransportType=Amqp` . Tento zápis dá pokyn klientské knihovně k tomu, aby připojení k Service Bus pomocí AMQP 1,0.

### <a name="amqp-over-websockets"></a>AMQP přes WebSockets
Pokud chcete používat AMQP přes objekty WebSockets, nastavte `TransportType` v připojovacím řetězci na `AmqpWebSockets` . Příklad: `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

Pokud používáte knihovnu .NET Microsoft. Azure. ServiceBus, nastavte [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) na AmqpWebSockets z [TransportType enum](/dotnet/api/microsoft.azure.servicebus.transporttype).

Pokud používáte knihovnu .NET Azure. Messaging. ServiceBus, nastavte [ServiceBusClient. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) na AmqpWebSockets z [ServiceBusTransportType enum](/dotnet/api/azure.messaging.servicebus.servicebustransporttype).


## <a name="message-serialization"></a>Serializace zprávy

Při použití výchozího protokolu je výchozím chováním serializace klientské knihovny .NET použití typu [DataContractSerializer][DataContractSerializer] k serializaci instance [BrokeredMessage][BrokeredMessage] pro přenos mezi klientskou knihovnou a službou Service Bus. Při použití režimu přenosu AMQP používá Klientská knihovna systém typů AMQP pro serializaci zprostředkované [zprávy][BrokeredMessage] do zprávy AMQP. Tato serializace umožňuje příjem a interpretaci zprávy přijímající aplikací, která je potenciálně spuštěna na jiné platformě, například aplikace Java, která používá rozhraní API JMS pro přístup k Service Bus.

Při vytváření instance [BrokeredMessage][BrokeredMessage] můžete poskytnout objekt .NET jako parametr konstruktoru, který bude sloužit jako tělo zprávy. Pro objekty, které lze namapovat na primitivní typy AMQP, je text serializován do datových typů AMQP. Pokud objekt nelze přímo namapovat na primitivní typ AMQP; To znamená, že vlastní typ definovaný aplikací, je objekt serializován pomocí třídy [DataContractSerializer][DataContractSerializer]a serializované bajty jsou odesílány do AMQP dat.

Pro usnadnění interoperability s klienty non-.NET používejte pouze typy .NET, které lze serializovat přímo do typů AMQP pro tělo zprávy. Následující tabulka popisuje tyto typy a odpovídající mapování na systém typů AMQP.

| Typ objektu textu .NET | Mapovaný typ AMQP | Typ oddílu textu AMQP |
| --- | --- | --- |
| bool |boolean |Hodnota AMQP |
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
| decimal |decimal128 |Hodnota AMQP |
| char |char |Hodnota AMQP |
| DateTime |časové razítko |Hodnota AMQP |
| Identifikátor GUID |uuid |Hodnota AMQP |
| Byte [] |binární |Hodnota AMQP |
| řetězec |řetězec |Hodnota AMQP |
| System. Collections. IList |list |AMQP hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. |
| System. Array |array |AMQP hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. |
| System. Collections. IDictionary |mapa |AMQP hodnota: položky obsažené v kolekci mohou být pouze ty, které jsou definovány v této tabulce. Poznámka: podporovány jsou pouze řetězcové klíče. |
| Identifikátor URI |Popis řetězce (viz následující tabulka) |Hodnota AMQP |
| DateTimeOffset |Popsaná dlouho (viz následující tabulka) |Hodnota AMQP |
| TimeSpan |Popsaná doba (viz následující) |Hodnota AMQP |
| Stream |binární |Data AMQP (můžou být vícenásobná). Oddíly dat obsahují nezpracované bajty přečtené z objektu Stream. |
| Jiný objekt |binární |Data AMQP (můžou být vícenásobná). Obsahuje serializovaný binární soubor objektu, který používá DataContractSerializer nebo serializátor poskytnutý aplikací. |

| Typ .NET | Typ AMQP mapovaného typu | Poznámky |
| --- | --- | --- |
| Identifikátor URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Identifikátor URI. AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset. UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan. osové značky |

## <a name="behavioral-differences"></a>Rozdíly v chování

V porovnání s výchozím protokolem je v chování rozhraní Service Bus .NET API k dispozici několik malých rozdílů:

* Vlastnost [OperationTimeout][OperationTimeout] je ignorována.
* `MessageReceiver.Receive(TimeSpan.Zero)` je implementován jako `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` .
* Vytváření zpráv pomocí zámků tokenů může být provedeno pouze příjemci zprávy, kteří zprávy původně obdrželi.

## <a name="control-amqp-protocol-settings"></a>Control – nastavení protokolu AMQP

Rozhraní [API .NET](/dotnet/api/) zveřejňují několik nastavení pro řízení chování protokolu AMQP:

* **[MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: řídí počáteční kredit aplikovaný na odkaz. Výchozí hodnota je 0.
* **[MessagingFactorySettings. AmqpTransportSettings. MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Určuje maximální velikost rámce AMQP nabízená během vyjednávání v době otevření připojení. Výchozí hodnota je 65 536 bajtů.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Pokud jsou přenosy k dispozici, určuje tato hodnota maximální zpoždění odeslání. Zděděni odesílateli a přijímači ve výchozím nastavení. Každý odesílatel/příjemce může přepsat výchozí hodnotu, což je 20 milisekund.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Určuje, zda jsou připojení AMQP navázána přes připojení TLS. Výchozí hodnota je **true**.

## <a name="next-steps"></a>Další kroky

Jste připraveni na další informace? Navštivte následující odkazy:

* [Přehled Service Bus AMQP]
* [Průvodce protokolem AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Přehled Service Bus AMQP]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md