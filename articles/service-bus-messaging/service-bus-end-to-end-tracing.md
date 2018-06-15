---
title: Azure Service Bus začátku do konce trasování a diagnostiku | Microsoft Docs
description: Přehled služby Service Bus klient diagnostiky a začátku do konce trasování
services: service-bus-messaging
documentationcenter: ''
author: lmolkova
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27741332"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuované trasování a korelace prostřednictvím zasílání zpráv Service Bus

Mezi běžné problémy při vývoji mikroslužeb přístup je schopnost trasování operaci od klienta přes všechny služby, které jsou součástí zpracování. Je vhodné pro ladění, analýza výkonu, A / B testování a další diagnostiky typické scénáře.
Jednou ze součástí sady tento problém je sledování logických jednotek zpracování. Její součástí jsou zpracování výsledků a latence a vnější závislosti volání zprávy. Další část je korelace tyto události diagnostiky mimo hranice procesu.

Pokud producent odešle zprávu prostřednictvím fronty, obvykle dochází v oboru jiné logické operace, iniciovaná některé klienta nebo služby. Jakmile obdrží zprávu spotřebitel pokračuje stejné operace. Výrobce a příjemců (i jiných služeb, které zpracovávají operaci), pravděpodobně z důvodu emitování telemetrické události pro trasování toku operace a výsledek. Chcete-li korelovat těchto událostí a trasování operaci klient server, musí každá služba, která hlásí telemetrie razítka každé události s kontextem trasování.

Zasílání zpráv Microsoft Azure Service Bus má definované vlastnosti datové části, které producenti a spotřebitelé měla použít pro takový kontext trasování.
Protokol je založena na [protokolu HTTP korelace](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Název vlastnosti        | Popis                                                 |
|----------------------|-------------------------------------------------------------|
|  Id diagnostiky       | Jedinečný identifikátor externí volání od výrobce do fronty. Odkazovat na [Id žádosti protokolu HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) odůvodnění, požadavky a formátu |
|  Korelace kontextu | Operace kontext, který se rozšíří ve všech službách účastnících se operace zpracování. Další informace najdete v tématu [korelace-kontextu protokolu HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Klient .NET Service Bus auto trasování

Počínaje verzí 3.0.0 [sběrnice klienta Microsoft Azure pro .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) poskytuje body instrumentace trasování, které může být jazyka trasování systémy nebo část kódu klienta.
Instrumentace umožňuje sledování všechna volání na sběrnici služby zasílání zpráv na úrovni služby z na straně klienta. Pokud se provádí zpracování zpráv s [vzorce obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), je také instrumentovány zpracování zpráv

### <a name="tracking-with-azure-application-insights"></a>Sledování pomocí služby Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) poskytuje bohaté možnosti, včetně automagical požadavku a sledování závislostí pro monitorování výkonu.

V závislosti na typu vašeho projektu nainstalujte Application Insights SDK:
- [ASP.NET](../application-insights/app-insights-asp-net.md) verze 2.5-beta2 nebo vyšší
- [ASP.NET Core](../application-insights/app-insights-asp-net-core.md) verze 2.2.0-beta2 nebo vyšší.
Tyto odkazy obsahují podrobné informace o instalaci sady SDK, vytváření prostředků a konfiguraci sady SDK (v případě potřeby). Aplikace bez ASP.NET, najdete v části [Azure Application Insights pro konzolové aplikace](../application-insights/application-insights-console.md) článku.

Pokud používáte [vzorce obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) ke zpracování zpráv, dokončení: všechna volání služby Service Bus provádí služby se automaticky sledovat a korelační s další položky telemetrie. V opačném případě najdete v následujícím příkladu pro sledování zpracování ruční zprávy.

#### <a name="trace-message-processing"></a>Zpracování zpráv trasování

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

V tomto příkladu `RequestTelemetry` hlášené pro každou zprávu zpracování časové razítko, doba trvání a výsledek (úspěch). Telemetrii také obsahuje sadu vlastností korelace.
Vnořené trasování a při zpracování zprávy nahlášené výjimky jsou také razítkem s vlastnostmi korelace představující je jako, děti, `RequestTelemetry`.

V případě, že provedete volání podporované externích součástí během zpracování zpráv, jsou také automagically sledován a korelační. Odkazovat na [sledovat vlastní operace s Application Insights .NET SDK](../application-insights/application-insights-custom-operations-tracking.md) pro ruční sledování a korelace.

### <a name="tracking-without-tracing-system"></a>Sledování bez trasování systému
V případě, že váš systém trasování nepodporuje volání služby Service Bus automagical sledování můžete hledat do přidání takové podpory do trasování systému nebo do vaší aplikace. Tato část popisuje diagnostiku událostí odeslaných klientem Service Bus .NET.  

Klient .NET Service Bus je instrumentovány pomocí rozhraní .NET trasování primitiv [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`slouží jako trasování kontextu při `DiagnosticSource` je vhodný mechanismus oznámení. 

Pokud není žádná naslouchání událostem DiagnosticSource, instrumentace je vypnutý, přitom udržet náklady nulové instrumentace. DiagnosticSource umožňuje všechny řízení naslouchací proces:
- naslouchací proces ovládací prvky, které zdroje a události pro naslouchání na
- počet událostí ovládací prvky naslouchací proces a vzorkování
- události se posílají s odebranou datovou částí, které poskytuje úplný kontextu, takže můžete používat a upravit objekt zpráv během události

Seznamte se s [DiagnosticSource uživatelská příručka](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) než budete pokračovat s implementací.

V aplikaci ASP.NET Core, která zapisuje protokoly s Microsoft.Extension.Logger vytvoříme naslouchací proces pro události služby Service Bus.
Používá [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) knihovna k odběru DiagnosticSource (je také snadno k odběru DiagnosticSource bez)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

V tomto příkladu naslouchací proces protokolu doba trvání, výsledek, jedinečný identifikátor a čas zahájení pro každou operaci služby Service Bus.

#### <a name="events"></a>Události

Pro každé operace jsou odesílány dvě události: 'Start' a 'Zastavit'. Největší pravděpodobností vás zajímá jenom události, zastavte'. Budou poskytovat výsledek operace, a také času spuštění a trvání jako vlastnosti aktivity.

Datová část události poskytuje naslouchací proces s kontextem operace, replikuje příchozí parametry rozhraní API a vrátit hodnotu. Datová část události, zastavte' má všechny vlastnosti 'Start' datová část události, takže můžete ignorovat 'Start' událostí úplně.

Všechny události také mít vlastnosti "Entitu" a "Koncový bod", že byly vynechány v níže uvedená tabulka
  * `string Entity`– Název entity (fronty, tématu, atd.)
  * `Uri Endpoint`-Adresa URL koncového bodu Service Bus

Všechny události, zastavte' má `Status` vlastnost s `TaskStatus` asynchronní operace byla dokončena s, který je také v následující tabulce pro jednoduchost vynechán.

Tady je úplný seznam instrumentovaného operace:

| Název operace | Sledované rozhraní API | Konkrétní datová část vlastnosti|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> – seznam zpráv odesílány zprávy |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Zpráva – zpráva zpracovává zprávy<br/>Datový typ DateTimeOffset ScheduleEnqueueTimeUtc - posun naplánované zpráv<br/>dlouhé SequenceNumber – pořadové číslo zprávy naplánované ('zastavit, datová část události) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | dlouhé SequenceNumber – pořadové číslo zprávy te ke zrušení | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount – maximální počet zpráv, které by mohly být přijaty.<br/>IList<Message> zprávy – seznam přijatých zpráv ('zastavit, datová část události) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber – počáteční bod, ze kterého chcete procházet dávku zpráv.<br/>int RequestedMessageCount - počet zpráv pro načtení.<br/>IList<Message> zprávy – seznam přijatých zpráv ('zastavit, datová část události) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | Rozhraní IEnumerable<long> SequenceNumbers – seznam obsahující pořadová čísla přijímat.<br/>IList<Message> zprávy – seznam přijatých zpráv ('zastavit, datová část události) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens – seznam obsahující zámek tokeny odpovídající zprávy a pokuste se dokončit.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | řetězec LockToken - token zámku odpovídající zprávy ukončil. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | řetězec LockToken - token zámku odpovídající zprávy odložení. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | řetězec LockToken - token zámku odpovídající zprávy nedoručených zpráv. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | řetězec LockToken - zámku token obnovení zámku na odpovídající zprávy.<br/>LockedUntilUtc datum a čas - datum vypršení platnosti tokenu nové uzamčení a čas ve formátu UTC. ('Zastavit, datová část události)|
| Microsoft.Azure.ServiceBus.Process | Funkce lambda obslužné rutiny zpráv součástí [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Zpráva zpráva – zpráva právě zpracovává. |
| Microsoft.Azure.ServiceBus.ProcessSession | Zpráva obslužné rutiny relace lambda funkce součástí [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Zpráva zpráva – zpráva právě zpracovává.<br/>Relace IMessageSession - relace zpracovává |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | Pravidlo RuleDescription – popis pravidla, který poskytuje pravidlo, které chcete přidat. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | řetězec RuleName – název pravidla, chcete-li odebrat. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | Rozhraní IEnumerable<RuleDescription> pravidla pravidla všechny přidružené k předplatnému. (Jenom datová 'Zastavit' část) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | řetězec SessionId - sessionId součástí zprávy. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | řetězec SessionId - sessionId součástí zprávy.<br/>Byte [] Stav – Stav relace ('zastavit, datová část události) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | řetězec SessionId - sessionId součástí zprávy.<br/>Byte [] Stav – Stav relace |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | řetězec SessionId - sessionId součástí zprávy. |
| Microsoft.Azure.ServiceBus.Exception | jakéhokoli instrumentovaného rozhraní API| Výjimka výjimka - instance výjimky |

V každé události, dostanete `Activity.Current` který obsahuje aktuální kontext operaci.

#### <a name="logging-additional-properties"></a>Další vlastnosti protokolování

`Activty.Current`poskytuje podrobné kontextu aktuální operace a jejích nadřazených tříd. Další informace najdete v tématu [aktivity dokumentaci](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) další podrobnosti.
Instrumentace Service Bus poskytuje další informace v `Activity.Current.Tags` -drží `MessageId` a `SessionId` vždy, když jsou k dispozici.

Aktivity, které sledovat 'Přijímat', 'Funkce Náhled' a 'ReceiveDeferred' událost také může mít `RelatedTo` značky. Drží odlišné seznam `Diagnostic-Id`(s) zpráv, které byly přijaty v důsledku.
Tato operace může vést k několika nesouvisejícími zprávy k přijetí. Navíc `Diagnostic-Id` nezná při spuštění operace, tak 'Receive' operace může časově 'Proces' operace pomocí jenom tato značka. Je užitečné při analýze problémy s výkonem ke kontrole, jak dlouho trvalo zpráva.

Efektivní způsob, jak protokolu značky je Iterujte přes, tak přidání značek v předcházejícím příkladu vypadá jako 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrování a vzorkování

V některých případech je žádoucí do protokolu pouze část události ke snížení spotřeby úložiště nebo Režijní náklady na výkon. Může protokolovat události, zastavte' pouze (jako v předchozím příkladu) nebo ukázkové procento událostí. 
`DiagnosticSource`Zadejte způsob, jak dosáhnout její `IsEnabled` predikátu. Další informace najdete v tématu [filtrování na základě kontextu v DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`může volat vícekrát pro jednu operaci k minimalizaci vlivu na výkon.

`IsEnabled`je volána v následujícím pořadí:

1. `IsEnabled(<OperationName>, string entity, null)`například `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Všimněte si, že je na konci žádné 'Start' nebo "Stop". Ji použijte k filtrování konkrétní operace nebo fronty. Pokud vrátí zpětného volání `false`, události pro operaci se neodesílají.

  * Pro operace 'Proces' a 'Entitami ProcessSession' můžete také přijímat `IsEnabled(<OperationName>, string entity, Activity activity)` zpětného volání. Použít pro filtrování událostí na základě `activity.Id` nebo značky vlastnosti.
  
2. `IsEnabled(<OperationName>.Start)`například `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontroluje, zda událost 'Start' by měl být aktivována. Výsledek se týká pouze 'Start' událostí, ale další instrumentace není závislá na něm.

Neexistuje žádné `IsEnabled` pro události, zastavte'.

Pokud některé výsledek operace výjimka, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` je volána. Jen může přihlásit k odběru událostí, výjimky a zabránit zbytek instrumentace. V takovém případě musíte ještě zpracovat takové výjimky. Vzhledem k tomu, že jiné instrumentace je zakázaná, není pravděpodobné, že trasování kontext, který má toku s zprávy na příjemce producent.

Můžete použít `IsEnabled` taky implementovat strategie vzorkování. Na základě vzorkování `Activity.Id` nebo `Activity.RootId` zajišťuje konzistentní vzorkování mezi všechny otestovat (Pokud je rozšířena trasování systému, nebo vlastní kód).

V přítomnost několika `DiagnosticSource` naslouchací procesy pro stejný zdroj, stačí pro jenom jeden naslouchací proces tak, aby přijímal událostí, takže `IsEnabled` není zaručeno, která se má volat

## <a name="next-steps"></a>Další postup

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights korelace](../application-insights/application-insights-correlation.md)
* [Application Insights monitorování závislosti](../application-insights/app-insights-asp-net-dependencies.md) chcete zobrazit, pokud REST, SQL nebo jiné externí prostředky zpomalují můžete.
* [Sledování vlastní operace s Application Insights .NET SDK](../application-insights/application-insights-custom-operations-tracking.md)
