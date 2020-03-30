---
title: Trasování a diagnostika azure service busu od konce | Dokumenty společnosti Microsoft
description: Přehled diagnostiky klienta service bus a trasování mezi koncovými kroky (klient prostřednictvím všech služeb, které se podílejí na zpracování.)
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 7c2efc9c736097873201505f280af5d47bed4847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294176"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuované trasování a korelace prostřednictvím zasílání zpráv service bus

Jedním z běžných problémů ve vývoji mikroslužeb je schopnost trasování operace z klienta prostřednictvím všech služeb, které se podílejí na zpracování. Je to užitečné pro ladění, analýzu výkonu, testování A/B a další typické diagnostické scénáře.
Jedna část tohoto problému je sledování logické části práce. Zahrnuje výsledek zpracování zpráv a latence a volání externí závislosti. Další částí je korelace těchto diagnostických událostí za hraniceprocesu.

Když výrobce odešle zprávu prostřednictvím fronty, obvykle se stane v rozsahu některé jiné logické operace, iniciované jiným klientem nebo službou. Stejná operace pokračuje spotřebitel i po přijetí zprávy. Výrobce i příjemce (a další služby, které zpracovávají operaci), pravděpodobně vyzařují telemetrické události ke sledování toku operace a výsledku. Aby bylo možné korelovat tyto události a trasování operace end-to-end, každá služba, která hlásí telemetrie má razítko každou událost s kontextu trasování.

Zasílání zpráv služby Microsoft Azure Service Bus má definované vlastnosti datové části, které by výrobci a spotřebitelé měli použít k předání takového kontextu trasování.
Protokol je založen na [protokolu HTTP Correlation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Název vlastnosti        | Popis                                                 |
|----------------------|-------------------------------------------------------------|
|  Id diagnostiky       | Jedinečný identifikátor externího volání od výrobce do fronty Informace o důvodech, aspektech a formátu naleznete [v protokolu HTTP na dotaz ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) požadavku v protokolu HTTP. |
|  Korelační kontext | Kontext operace, který je šířen napříč všemi službami zapojenými do zpracování operací. Další informace naleznete v tématu [Korelační kontext v protokolu HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Automatické trasování klienta služby Service Bus .NET

Počínaje verzí 3.0.0 [Microsoft Azure ServiceBus Client pro rozhraní .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) poskytuje body instrumentace trasování, které mohou být připojeny pomocí trasovacích systémů nebo části klientského kódu.
Instrumentace umožňuje sledování všech volání služby zasílání zpráv Service Bus ze strany klienta. Pokud se zpracování zprávy provádí se [vzorem obslužné rutiny zprávy](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), je zpracování zpráv také instrumentováno

### <a name="tracking-with-azure-application-insights"></a>Sledování pomocí přehledů aplikací Azure

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) poskytuje bohaté možnosti sledování výkonu, včetně automatického zpracování požadavků a sledování závislostí.

V závislosti na typu projektu nainstalujte sdk Application Insights:
- [ASP.NET](../azure-monitor/app/asp-net.md) - instalace verze 2.5-beta2 nebo vyšší
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - nainstalujte verzi 2.2.0-beta2 nebo vyšší.
Tyto odkazy obsahují podrobnosti o instalaci sady SDK, vytváření prostředků a konfiguraci sady SDK (v případě potřeby). Informace o non-ASP.NET aplikacích najdete v článku [Přehledy aplikací Azure pro konzolové aplikace.](../azure-monitor/app/console.md)

Pokud ke zpracování zpráv použijete [vzor obslužné rutiny zpráv,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) je hotovo: všechna volání služby Service Bus provedená vaší službou jsou automaticky sledována a korelována s jinými položkami telemetrie. V opačném případě naleznete v následujícím příkladu pro ruční sledování zpracování zpráv.

#### <a name="trace-message-processing"></a>Zpracování trasovacích zpráv

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

V tomto `RequestTelemetry` příkladu je hlášena pro každou zpracovanou zprávu s časovým razítkem, dobou trvání a výsledkem (úspěch). Telemetrie má také sadu vlastností korelace.
Vnořené trasování a výjimky hlášené během zpracování zpráv jsou také označeny vlastnostmi korelace představujícími je jako "podřízené" `RequestTelemetry`rozhraní .

V případě, že během zpracování zpráv voláte na podporované externí součásti, jsou také automaticky sledovány a korelovány. Ruční sledování a korelace naleznete v příručce [Sledování vlastních operací pomocí sady Application Insights .NET SDK.](../azure-monitor/app/custom-operations-tracking.md)

Pokud kromě sady Application Insights SDK používáte libovolný externí kód, očekávejte delší **dobu trvání** při zobrazení protokolů Application Insights. 

![Delší trvání v protokolu Application Insights](./media/service-bus-end-to-end-tracing/longer-duration.png)

To neznamená, že došlo ke zpoždění při přijímání zprávy. V tomto scénáři již byla přijata zpráva od zprávy je předán jako parametr kódu sady SDK. A **jmenovka** v protokolech Přehledy aplikací **(Proces)** označuje, že zpráva je nyní zpracovávána vaším externím kódem pro zpracování událostí. Tento problém není související s Azure. Místo toho tyto metriky odkazují na efektivitu externího kódu vzhledem k tomu, že zpráva již byla přijata ze služby Service Bus. Podívejte se [na tento soubor na GitHubu,](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) abyste zjistili, kde je generována značka **Process** a přiřazena po přijetí zprávy z service busu. 

### <a name="tracking-without-tracing-system"></a>Sledování bez sledovacího systému
V případě, že váš systém trasování nepodporuje automatické sledování volání service bus, můžete se podívat na přidání takové podpory do sledovacího systému nebo do vaší aplikace. Tato část popisuje události diagnostiky odeslané klientem service bus .NET.  

Service Bus .NET Client je instrumentován pomocí primitivních nástrojů .NET trasování [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`slouží jako kontext `DiagnosticSource` trasování, zatímco je mechanismus oznamování. 

Pokud neexistuje žádný naslouchací proces pro události DiagnosticSource, instrumentace je vypnuto, udržování nulové náklady na instrumentaci. DiagnosticSource poskytuje veškerou kontrolu naslouchací proces:
- posluchač řídí, které zdroje a události poslouchat
- posluchač řídí rychlost událostí a vzorkování
- události jsou odesílány s datovou částí, která poskytuje úplný kontext, takže můžete během události přistupovat k objektu Message a upravovat jej

Než budete pokračovat v implementaci, seznamte se s [uživatelskou příručkou DiagnosticSource.](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

Pojďme vytvořit naslouchací proces pro události Service Bus v aplikaci ASP.NET Core, která zapisuje protokoly s Microsoft.Extension.Logger.
Používá [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) knihovny přihlásit k odběru DiagnosticSource (je to také snadné se přihlásit k odběru DiagnosticSource bez něj)

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

V tomto příkladu posluchač eloguje dobu trvání, výsledek, jedinečný identifikátor a čas spuštění pro každou operaci sběrnice.

#### <a name="events"></a>Události

Pro každou operaci jsou odeslány dvě události: 'Start' a 'Stop'. S největší pravděpodobností se zajímáte pouze o akce "Stop". Poskytují výsledek operace, stejně jako čas zahájení a trvání jako Activity vlastnosti.

Datová část události poskytuje naslouchací proces s kontextem operace, replikuje příchozí parametry rozhraní API a vrácenou hodnotu. Datová část události Stop má všechny vlastnosti datové části události Start, takže můžete událost Start zcela ignorovat.

Všechny události mají také vlastnosti Entita a Koncový bod, jsou vynechány v následující tabulce
  * `string Entity`- - Název entity (fronta, téma atd.)
  * `Uri Endpoint`- Adresa URL koncového bodu sběrnice

Každá událost Stop `Status` má `TaskStatus` vlastnost s asynchronní operací byla dokončena s, která je také vynechána v následující tabulce pro jednoduchost.

Zde je úplný seznam instrumentovaných operací:

| Název operace | Sledované rozhraní API | Specifické vlastnosti datové části|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`- Seznam odesílaných zpráv |
| Zpráva Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`- Zpráva je zpracovávána<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`- Posun naplánované zprávy<br/>`long SequenceNumber`- Pořadové číslo naplánované zprávy ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`- Pořadové číslo zprávy te, která má být zrušena | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`- Maximální počet zpráv, které by mohly být přijaty.<br/>`IList<Message> Messages`- Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`- Výchozí bod, ze kterého chcete procházet dávku zpráv.<br/>`int RequestedMessageCount`- Počet zpráv, které chcete načíst.<br/>`IList<Message> Messages`- Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.ReceiveOdložené | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`- Seznam obsahující pořadová čísla, která mají být přijímána.<br/>`IList<Message> Messages`- Seznam přijatých zpráv ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`- Seznam obsahující tokeny zámku odpovídající zprávy k dokončení.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`- Token zámku odpovídající zprávy opustit. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`- Token zámku odpovídající zprávy odložit. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`- Token zámku odpovídající zprávy nedoručené zprávě. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`- Token zámku odpovídající zprávy obnovit zámek.<br/>`DateTime LockedUntilUtc`- Nový zámek token datum a čas ve formátu UTC. ('Stop' datová část události)|
| Microsoft.Azure.ServiceBus.Process | Funkce lambda obslužné rutiny zprávy poskytnutá v [souboru IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`- Zpráva se zpracovává. |
| Relace Microsoft.Azure.ServiceBus.ProcessSession | Funkce lambda obslužné rutiny relace zprávy poskytovaná v [souboru IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`- Zpráva se zpracovává.<br/>`IMessageSession Session`- Zpracování relace |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`- Popis pravidla, který poskytuje pravidlo přidat. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- Název pravidla odstranit. |
| Pravidla Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- Všechna pravidla spojená s předplatným. (pouze datová část stop) |
| Relace zpráv Microsoft.Azure.ServiceBus.AcceptSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`- SessionId přítomné ve zprávách. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`- SessionId přítomné ve zprávách.<br/>`byte [] State`- Stav relace ('Stop' datová část události) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`- SessionId přítomné ve zprávách.<br/>`byte [] State`- Stav relace |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`- SessionId přítomné ve zprávách. |
| Výjimka Microsoft.Azure.ServiceBus.Exception | jakékoli instrumentované API| `Exception Exception`- Instance výjimky |

V každém případě můžete `Activity.Current` přistupovat, který obsahuje aktuální kontext operace.

#### <a name="logging-additional-properties"></a>Protokolování dalších vlastností

`Activity.Current`poskytuje podrobný kontext současného provozu a jeho rodiče. Další informace naleznete v [dokumentaci k aktivitám,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) kde najdete další podrobnosti.
Service Bus instrumentace poskytuje `Activity.Current.Tags` další `MessageId` informace `SessionId` v - drží a kdykoli jsou k dispozici.

Aktivity, které sledují 'Receive', 'Peek' a 'ReceiveDeferred' událost také může mít `RelatedTo` značku. Obsahuje odlišný seznam `Diagnostic-Id`(y) zpráv, které byly přijaty jako výsledek.
Tato operace může mít za následek několik nesouvisejících zpráv, které mají být přijaty. Také `Diagnostic-Id` není známo, kdy operace spustí, takže operace "Příjem" může být korelován s operacemi procesu pomocí pouze tuto značku. Je užitečné při analýze problémů s výkonem a zkontrolujte, jak dlouho trvalo přijetí zprávy.

Efektivní způsob, jak protokolovat tagy je iterate nad nimi, takže přidání tagy do předchozího příkladu vypadá 

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

#### <a name="filtering-and-sampling"></a>Filtrování a odběr vzorků

V některých případech je žádoucí protokolovat pouze část událostí ke snížení režie výkonu nebo spotřeby úložiště. Můžete protokolovat pouze události Stop (jako v předchozím příkladu) nebo procento vzorku událostí. 
`DiagnosticSource`způsob, jak toho `IsEnabled` dosáhnout predikátem. Další informace naleznete [v tématu Filtrování na základě kontextu v diagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`může být volána vícekrát pro jednu operaci minimalizovat dopad na výkon.

`IsEnabled`je volána v následujícím pořadí:

1. `IsEnabled(<OperationName>, string entity, null)`například `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Všimněte si, že na konci není žádný "Start" nebo "Stop". Slouží k odfiltrování konkrétních operací nebo front. Pokud zpětné `false`volání vrátí , události pro operaci nejsou odeslány

   * Pro operace "Proces" a "ProcessSession" `IsEnabled(<OperationName>, string entity, Activity activity)` obdržíte také zpětné volání. Slouží k filtrování událostí `activity.Id` na základě vlastností nebo Tagy.
  
2. `IsEnabled(<OperationName>.Start)`například `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Zkontroluje, zda má být aktivována událost Start. Výsledek ovlivňuje pouze událost Start, ale další instrumentace na ní nezávisí.

Neexistuje žádná `IsEnabled` událost 'Stop'.

Pokud některé operace výsledek `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` je výjimka, je volána. Můžete se přihlásit pouze k odběru událostí výjimky a zabránit zbytku instrumentace. V takovém případě stále musíte zpracovat takové výjimky. Vzhledem k tomu, že jiné instrumentace je zakázána, neměli byste očekávat, že kontext trasování toku se zprávami od příjemce k výrobci.

Můžete také `IsEnabled` implementovat strategie vzorkování. Odběr vzorků `Activity.Id` založený `Activity.RootId` na nebo zajišťuje konzistentní odběr vzorků napříč všemi pneumatikami (pokud je šířen sledovacím systémem nebo vaším vlastním kódem).

V přítomnosti `DiagnosticSource` více posluchačů pro stejný zdroj, stačí pouze jeden posluchač přijmout `IsEnabled` událost, takže není zaručeno, že bude volána,

## <a name="next-steps"></a>Další kroky

* [Korelace přehledů aplikací](../azure-monitor/app/correlation.md)
* [Závislostí monitoru přehledů aplikací, abyste zjistili,](../azure-monitor/app/asp-net-dependencies.md) jestli vás rest, SQL nebo jiné externí prostředky zpomalují.
* [Sledování vlastních operací pomocí sady Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
