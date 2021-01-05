---
title: Azure Service Bus komplexní trasování a diagnostika | Microsoft Docs
description: Přehled Service Bus diagnostiky klientů a komplexní trasování (klient prostřednictvím všech služeb, které se podílejí na zpracování)
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc7dab21fc01b624e8ab122fe883be89ea8633f6
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832688"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuované trasování a korelace prostřednictvím Service Bus zasílání zpráv

Jedním z běžných problémů při vývoji mikroslužeb je schopnost sledovat operace od klienta přes všechny služby, které jsou součástí zpracování. Je užitečné pro ladění, analýzu výkonu, testování a/B a další typické scénáře diagnostiky.
Jednou z těchto potíží je sledování logických částí práce. Zahrnuje výsledky zpracování zpráv a latence a externí volání závislostí. Další část je korelace těchto událostí diagnostiky za hranice procesu.

Když producent pošle zprávu přes frontu, obvykle se děje v rozsahu některé jiné logické operace iniciované nějakým jiným klientem nebo službou. Stejná operace pokračuje příjemcem, jakmile obdrží zprávu. Producent i příjemce (a další služby, které zpracovávají operaci), předpokládané vysílat události telemetrie pro trasování toku a výsledku operace. Aby bylo možné tyto události a trasovací operace vzájemně sladit, musí každá služba, která hlásí telemetrii, vyplnit každou událost pomocí kontextu trasování.

Microsoft Azure Service Bus zasílání zpráv má definované vlastnosti datové části, které by producenti a spotřebitelé měli použít k předání takového kontextu trasování.
Protokol je založený na [protokolu korelace http](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Název vlastnosti        | Popis                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Jedinečný identifikátor externího volání od producenta do fronty. Odůvodnění, požadavky a formát najdete [v protokolu HTTP pro žádosti o ID](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) . |
|  Correlation-Context | Kontext operace, který je šířený napříč všemi službami zapojenými do zpracování operací. Další informace najdete v tématu [korelace – kontext v protokolu HTTP](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) . |

## <a name="service-bus-net-client-autotracing"></a>Service Bus autotracing klienta .NET

Počínaje verzí 3.0.0 [Microsoft Azure klient ServiceBus pro .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) poskytuje trasovací body instrumentace, které mohou být zapojeny do trasovacích systémů nebo do části kódu klienta.
Instrumentace umožňuje sledování všech volání služby Service Bus Messaging Service ze strany klienta. Pokud se zpracování zprávy provádí pomocí [vzoru obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), je také instrumentované zpracování zprávy.

### <a name="tracking-with-azure-application-insights"></a>Sledování s využitím Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) poskytuje bohatě výkonné možnosti monitorování, včetně automagic Request a sledování závislostí.

V závislosti na typu projektu nainstalujte Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) – instalace verze 2,5-beta2 nebo vyšší
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) – instalace verze 2.2.0-beta2 nebo vyšší.
Tyto odkazy poskytují podrobné informace o instalaci sady SDK, vytváření prostředků a konfiguraci sady SDK (v případě potřeby). Informace o aplikacích non-ASP.NET najdete v článku o [konzolových aplikacích Azure Application Insights](../azure-monitor/app/console.md) .

Pokud ke zpracování zpráv používáte [vzor obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) , jste hotovi: všechna Service Bus volání prováděná službou jsou automaticky sledována a koreluje s ostatními položkami telemetrie. Jinak v případě ručního sledování zpracování zpráv použijte následující příklad.

#### <a name="trace-message-processing"></a>Trasování zpracování zpráv

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

V tomto příkladu `RequestTelemetry` je hlášena pro každou zpracovávanou zprávu s časovým razítkem, dobou trvání a výsledkem (úspěch). Telemetrie má také sadu vlastností korelace.
Vnořená trasování a výjimky hlášené během zpracování zprávy jsou také označeny vlastnostmi korelace, které je představují jako podřízené položky `RequestTelemetry` .

V případě, že během zpracování zprávy provedete volání podporovaných externích komponent, automaticky se sledují a korelují. Informace o ručním sledování a korelaci najdete [v tématu sledování vlastních operací pomocí Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) .

Pokud kromě Application Insights SDK používáte i nějaký externí kód, při zobrazení protokolů Application Insights se očekává, že se zobrazí delší **Doba trvání** . 

![Delší doba Application Insights protokolu](./media/service-bus-end-to-end-tracing/longer-duration.png)

Neznamená to, že při přijímání zprávy došlo k prodlevě. V tomto scénáři již byla zpráva přijata, protože zpráva je předána jako parametr kódu sady SDK. A značka **Name** v protokolech App Insights (**Process**) označuje, že zpráva se teď zpracovává vaším kódem pro zpracování externích událostí. Tento problém se netýká Azure. Místo toho tyto metriky odkazují na efektivitu vašeho externího kódu, protože zpráva již byla přijata z Service Bus. Podívejte se [na tento soubor na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) a zjistěte, kde se po přijetí zprávy z Service Bus značka **procesu** vygenerovala a přiřadí. 

### <a name="tracking-without-tracing-system"></a>Sledování bez trasování systému
V případě, že váš systém trasování nepodporuje sledování volání automatických Service Bus, můžete se podívat, jak tato podpora přidat do trasovacího systému nebo do aplikace. Tato část popisuje diagnostické události odesílané Service Bus klienta .NET.  

Service Bus klient .NET instrumentuje pomocí primitivních primitiv rozhraní .NET [System. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) a [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` slouží jako kontext trasování, zatímco `DiagnosticSource` je mechanismus oznámení. 

Pokud není k dispozici naslouchací proces pro události DiagnosticSource, instrumentace se vypne a zachová nulové náklady instrumentace. DiagnosticSource poskytuje všem ovládacím prvkům naslouchací proces:
- naslouchací proces řídí, se kterými zdroji a událostmi naslouchá
- frekvence a vzorkování událostí ovládacích prvků naslouchacího procesu
- události se odesílají s datovou částí, která poskytuje úplný kontext, takže můžete přistupovat k objektu zprávy a upravovat ho během události.

Než budete pokračovat v implementaci, Seznamte se s [DiagnosticSource User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) .

Pojďme vytvořit naslouchací proces pro Service Bus události v aplikaci ASP.NET Core, která zapisuje protokoly do Microsoft. extension. protokolovacího nástroje.
Pomocí knihovny [System. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) se přihlásí k odběru DiagnosticSource (můžete se taky snadno přihlásit k odběru DiagnosticSource bez něj).

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

V tomto příkladu jsou protokoly naslouchacího procesu trvání, výsledek, jedinečný identifikátor a čas spuštění pro každou operaci Service Bus.

#### <a name="events"></a>Události

Pro každou operaci jsou odesílány dvě události: ' Start ' a ' stop '. Pravděpodobně vás zajímá pouze události stop. Poskytují výsledek operace a také čas zahájení a dobu trvání jako vlastnosti aktivity.

Datová část události poskytuje naslouchací proces s kontextem operace, replikuje příchozí parametry rozhraní API a návratovou hodnotu. Datová část události ' stop ' má všechny vlastnosti datové části ' Start ', takže můžete událost ' spustit ' zcela ignorovat.

Všechny události mají také vlastnosti entita a koncový bod, jsou v níže uvedené tabulce vynechány.
  * `string Entity` --Název entity (Queue, téma atd.)
  * `Uri Endpoint` – Service Bus adresa URL koncového bodu

Každé události ' stop ' má `Status` vlastnost s `TaskStatus` asynchronní operací byla dokončena s, která je také vynechána v následující tabulce pro zjednodušení.

Tady je úplný seznam instrumentované operace:

| Název operace | Sledované rozhraní API | Vlastnosti konkrétní datové části|
|----------------|-------------|---------|
| Microsoft. Azure. ServiceBus. Send | [MessageSender. SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` – Seznam odesílaných zpráv |
| Microsoft. Azure. ServiceBus. ScheduleMessage | [MessageSender. ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` – Zpracovávaná zpráva<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` – Posun naplánované zprávy<br/>`long SequenceNumber` – Pořadové číslo naplánované zprávy (část události ' stop ') |
| Microsoft. Azure. ServiceBus. Cancel | [MessageSender. CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` – Pořadové číslo zprávy, která se má zrušit | 
| Microsoft. Azure. ServiceBus. Receive | [MessageReceiver. metody ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` – Maximální počet zpráv, které by mohly být přijaty.<br/>`IList<Message> Messages` – Seznam přijatých zpráv (část události ' stop ') |
| Microsoft. Azure. ServiceBus. prohlížet | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` – Výchozí bod, ze kterého se má procházet dávka zpráv.<br/>`int RequestedMessageCount` – Počet zpráv, které se mají načíst.<br/>`IList<Message> Messages` – Seznam přijatých zpráv (část události ' stop ') |
| Microsoft. Azure. ServiceBus. ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` – Seznam obsahující pořadová čísla, která mají být přijata.<br/>`IList<Message> Messages` – Seznam přijatých zpráv (část události ' stop ') |
| Microsoft. Azure. ServiceBus. Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` – Seznam obsahující tokeny zámku odpovídajících zpráv, které mají být dokončeny.|
| Microsoft. Azure. ServiceBus. Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` – Token zámku odpovídající zprávy, která se má opustit. |
| Microsoft. Azure. ServiceBus. odklad | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` – Token zámku odpovídající zprávy, která se má odložit. | 
| Microsoft. Azure. ServiceBus. nedoručených zpráv | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` – Token zámku odpovídající zprávy na nedoručené písmeno. | 
| Microsoft. Azure. ServiceBus. RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` – Token zámku odpovídající zprávy pro obnovení zámku.<br/>`DateTime LockedUntilUtc` – Nové datum a čas vypršení platnosti tokenu zámku ve formátu UTC. (' Stop ' – datová část události)|
| Microsoft. Azure. ServiceBus. Process | Funkce lambda obslužné rutiny zpráv poskytnutá v [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` -Zpráva je zpracovávána. |
| Microsoft. Azure. ServiceBus. ProcessSession | Funkce lambda obslužné rutiny relace zprávy, která je k dispozici v [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` -Zpráva je zpracovávána.<br/>`IMessageSession Session` – Zpracovávaná relace |
| Microsoft. Azure. ServiceBus. AddRule | [SubscriptionClient. AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` – Popis pravidla, který poskytuje pravidlo, které se má přidat. |
| Microsoft. Azure. ServiceBus. RemoveRule | [SubscriptionClient. RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` – Název pravidla, které chcete odebrat. |
| Microsoft. Azure. ServiceBus. getrules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` -Všechna pravidla přidružená k předplatnému. (Pouze datové části ' stop ') |
| Microsoft. Azure. ServiceBus. AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` – Identifikátor sessionId obsažený ve zprávách. |
| Microsoft. Azure. ServiceBus. getsessionstate | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` – Identifikátor sessionId obsažený ve zprávách.<br/>`byte [] State` -Stav relace (část události ' stop ') |
| Microsoft. Azure. ServiceBus. SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` – Identifikátor sessionId obsažený ve zprávách.<br/>`byte [] State` -Stav relace |
| Microsoft. Azure. ServiceBus. RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` – Identifikátor sessionId obsažený ve zprávách. |
| Microsoft. Azure. ServiceBus. Exception | jakékoli instrumentované rozhraní API| `Exception Exception` -Instance výjimky |

V každé události máte přístup `Activity.Current` , který obsahuje kontext aktuální operace.

#### <a name="logging-additional-properties"></a>Protokolování dalších vlastností

`Activity.Current` poskytuje podrobný kontext aktuální operace a jejích nadřazených prvků. Další informace najdete v [dokumentaci aktivity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) , kde najdete další podrobnosti.
Service Bus Instrumentace poskytuje další informace, které jsou k `Activity.Current.Tags` `MessageId` `SessionId` dispozici, a pokaždé, když jsou k dispozici.

Aktivity, které sledují událost Receive, prohlížet a ReceiveDeferred, mohou mít také `RelatedTo` značku. Obsahuje jedinečný seznam `Diagnostic-Id` (y) zpráv, které byly přijaty v důsledku.
Tato operace může vést k přijetí několika nesouvisejících zpráv. Také není `Diagnostic-Id` známo, když operace začíná, takže operace Receive by mohly být sladěné s operacemi zpracování pouze pomocí této značky. Je užitečné při analýze problémů s výkonem ke kontrole, jak dlouho trvalo přijímání zprávy.

Účinný způsob, jak přihlašovat značky, je iterovat přes ně, takže Přidání značek k předchozímu příkladu vypadá takto. 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrování a vzorkování

V některých případech je žádoucí protokolovat pouze část událostí, aby se snížila režie výkonu nebo spotřeba úložiště. Mohli byste protokolovat pouze události stop (jako v předchozím příkladu) nebo ukázkové procento událostí. 
`DiagnosticSource` Poskytněte způsob, jak ho dosáhnout pomocí `IsEnabled` predikátu. Další informace najdete v tématu [filtrování založené na kontextu v DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` může být voláno vícekrát, aby jedna operace minimalizovala dopad na výkon.

`IsEnabled` se volá v následujícím pořadí:

1. `IsEnabled(<OperationName>, string entity, null)` například `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` . Všimněte si, že na konci není žádné "Start" nebo "Stop". Slouží k filtrování konkrétních operací nebo front. Pokud zpětné volání vrátí `false` události pro operaci, nebudou odeslány.

   * Pro operace "proces" a "ProcessSession" obdržíte také `IsEnabled(<OperationName>, string entity, Activity activity)` zpětné volání. Slouží k filtrování událostí na základě `activity.Id` vlastností značek nebo.
  
2. `IsEnabled(<OperationName>.Start)` například `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` . Kontroluje, zda by měla být aktivována událost Start. Výsledek má vliv pouze na událost Start, ale další instrumentace na ní není závislá.

Pro událost zastavení není k dispozici `IsEnabled` .

Pokud je výsledkem nějaké operace výjimka, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` je volána metoda. Přihlásili jste se k odběru událostí Exception a zabráníte zbytek instrumentace. V takovém případě je stále nutné tyto výjimky zpracovat. Vzhledem k tomu, že je jiná instrumentace zakázaná, neměli byste očekávat, že by kontext trasování byl tok se zprávami od spotřebitelů k producentovi.

Můžete použít `IsEnabled` také implementaci strategií vzorkování. Vzorkování založené na `Activity.Id` nebo `Activity.RootId` zaručuje konzistentní vzorkování přes všechny pneumatiky (Pokud je šířené systémem trasování nebo vlastním kódem).

V `DiagnosticSource` případě, že je pro stejný zdroj k dispozici více posluchačů, je pro přijetí události dostačující pouze jeden naslouchací proces, takže není `IsEnabled` zaručeno jejich volání.

## <a name="next-steps"></a>Další kroky

* [Korelace Application Insights](../azure-monitor/app/correlation.md)
* [Application Insights závislosti monitorů](../azure-monitor/app/asp-net-dependencies.md) , abyste viděli, jestli REST, SQL nebo jiné externí prostředky zpomalují vaši práci.
* [Sledování vlastních operací pomocí sady Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
