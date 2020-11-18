---
title: Průvodce odstraňováním potíží pro službu Azure SignalR Service
description: Zjistěte, jak řešit běžné problémy.
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 11ea348a80bc226b6a96bea1e7c023ee9c06b13a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684113"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Průvodce odstraňováním potíží pro běžné problémy služby signalizace Azure

V těchto pokynech je vhodné použít Průvodce odstraňováním potíží na základě běžných problémů, které zákazníci splní a vyřešili v posledních letech.

## <a name="access-token-too-long"></a>Přístupový token je moc dlouhý.

### <a name="possible-errors"></a>Možné chyby:

* Na straně klienta `ERR_CONNECTION_`
* identifikátor URI 414 je příliš dlouhý.
* datová část 413 je moc velká.
* Přístupový token nesmí být delší než 4K. Entita požadavku 413 je moc velká.

### <a name="root-cause"></a>Hlavní příčina:

Pro HTTP/2 je maximální délka pro jednu hlavičku **4 KB**, takže pokud používáte prohlížeč pro přístup ke službě Azure, dojde `ERR_CONNECTION_` k chybě tohoto omezení.

Pro klienty HTTP/1.1 nebo C# je maximální délka identifikátoru URI **12 k**, maximální délka hlavičky je **16 k**.

Pomocí sady SDK verze **1.0.6** nebo vyšší `/negotiate` Vyvolá příkaz, `413 Payload Too Large` když je vygenerovaný přístupový token větší než **4 KB**.

### <a name="solution"></a>Řešení:

Ve výchozím nastavení jsou deklarace identity `context.User.Claims` zahrnuté při generování přístupového tokenu JWT **A** do **ASRS**(zure **s** Ignal **R** **S** lužby), aby deklarace identity byly zachované a dají se předávat z **ASRS** do rozhraní, `Hub` když se klient připojí k `Hub` .

V některých případech `context.User.Claims` se využívá k ukládání velkého množství informací o aplikačním serveru. většina z nich se nepoužívá v `Hub` s, ale jinými komponentami.

Vygenerovaný přístupový token se předává přes síť a pro připojení pomocí protokolu WebSocket/SSE se přístupové tokeny předávají prostřednictvím řetězců dotazů. Proto doporučujeme, abyste před tím, než se v centru vycházejí, od klienta až po **ASRS** do vašeho aplikačního serveru předali jenom **nezbytné** deklarace identity.

Je k dispozici, `ClaimsProvider` abyste mohli přizpůsobit deklarace identity předávání do **ASRS** v rámci přístupového tokenu.

Pro ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Pro ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>Je vyžadován protokol TLS 1,2

### <a name="possible-errors"></a>Možné chyby:

* ASP.NET [#279](https://github.com/Azure/azure-signalr/issues/279) není k dispozici žádný server.
* ASP.NET "připojení není aktivní, data nelze odeslat službě." Chyba [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Při vytváření požadavku HTTP na https://došlo k chybě <API endpoint> . Tato chyba může být způsobena tím, že certifikát serveru není správně nakonfigurován s HTTP.SYS v případě protokolu HTTPS. Tato chyba může být také způsobena neshodou vazby zabezpečení mezi klientem a serverem. "

### <a name="root-cause"></a>Hlavní příčina:

Služba Azure podporuje pouze TLS 1.2 z bezpečnostních důvodů. V případě rozhraní .NET Framework je možné, že TLS 1.2 není výchozím protokolem. V důsledku toho nelze úspěšně vytvořit připojení serveru k ASRS.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

1. Pokud je možné tuto chybu reprodukovat místně, zrušte kontrolu *pouze můj kód* a vyvolejte všechny výjimky CLR a místní ladění serveru aplikace, abyste viděli, jaká výjimka vyvolá výjimku.
    * Zrušit kontrolu *pouze můj kód*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Zrušit kontrolu Pouze můj kód":::

    * Vyvolat výjimky CLR

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Vyvolat výjimky CLR":::

    * Podívejte se na výjimky throw při ladění kódu aplikace na straně serveru:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Výjimka vyvolá výjimku":::

2. Pro ASP.NET můžete také přidat následující kód pro `Startup.cs` povolení podrobného trasování a zobrazit chyby z protokolu.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Řešení:

Přidejte do svého spuštění následující kód:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 Chybný požadavek pro žádosti klienta

### <a name="root-cause"></a>Původní příčina

Ověřte, zda má váš požadavek klienta více `hub` řetězců dotazu. `hub` je zachovaný parametr dotazu a 400 vyvolá, pokud služba zjistí více než jednu `hub` v dotazu.

## <a name="401-unauthorized-returned-for-client-requests"></a>Požadavky klientů vrací chybu 401 Neautorizováno

### <a name="root-cause"></a>Původní příčina

Výchozí hodnota životnosti tokenu JWT je v současné době 1 hodina.

Pokud ASP.NET Core Signal používá typ přenosu protokolu WebSocket, je v pořádku.

U ASP.NET Core pro jiný typ přenosu, SSE a dlouhodobé cyklické dotazování, to znamená, že ve výchozím nastavení může připojení trvat maximálně jednu hodinu.

V případě ASP.NET signalizace pošle `/ping` službě požadavek na udržení naživu do služby včas, a když `/ping` dojde k chybě, klient **přeruší** připojení a nikdy se nepřipojí. To znamená, že pro signál ASP.NET výchozí doba platnosti tokenu vytvoří připojení **po** dobu 1 hodiny pro všechny typy přenosů.

### <a name="solution"></a>Řešení

Z důvodu zabezpečení nedoporučujeme rozšířenou hodnotu TTL. Doporučujeme přidat logiku opětovného připojení z klienta a restartovat připojení, když dojde k těmto 401. Když klient připojení znovu spustí, bude vyjednávat se serverem App Server a získat token JWT znovu a získat obnovený token.

[Zde](#restart_connection) najdete informace o tom, jak restartovat připojení klientů.

## <a name="404-returned-for-client-requests"></a>Požadavky klientů vrací chybu 404 Neautorizováno

V případě trvalého připojení k signalizaci je nejprve `/negotiate` Služba Azure Signal Service a potom vytvoří skutečné připojení ke službě Azure Signal.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

* Následující [postup ukazuje, jak zobrazit odchozí žádosti](#view_request) o získání žádosti od klienta ke službě.
* Ověřte adresu URL žádosti, když dojde k 404. Pokud se adresa URL zaměřuje na vaši webovou aplikaci a podobá se `{your_web_app}/hubs/{hubName}` , ověřte, jestli `SkipNegotiation` je klient `true` . Když používáte službu Azure Signaler, klient obdrží adresu URL pro přesměrování při prvním vyjednání s aplikačním serverem. Klient **by neměl** při použití nástroje Azure Signal vynechávat vyjednávání.
* Další 404 může nastat, pokud je žádost o připojení zpracována více než **5** sekund po `/negotiate` volání. Podívejte se na časové razítko žádosti klienta a otevřete nám problém, pokud má požadavek na službu pomalou odezvu.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 vráceno pro žádost o obnovení připojení ASP.NET Signal

V případě ASP.NET signalizace, když se [připojení klienta](#client_connection_drop)napustí, se znovu připojí přes stejný `connectionId` čas třikrát před zastavením připojení. `/reconnect` může pomáhat, pokud je připojení vyřazené kvůli přerušovaným problémům v síti, které `/reconnect` můžou úspěšně obnovit trvalé připojení. V některých případech je připojení klienta například vyřazeno z důvodu přerušení připojení směrovaného serveru nebo služba signalizace obsahuje některé interní chyby, jako je restartování/převzetí služeb při selhání nebo nasazení, připojení již neexistuje, takže se `/reconnect` vrátí `404` . Jedná se o očekávané chování pro `/reconnect` a po trojnásobných pokusech o připojení se zastaví. Při zastavení připojení doporučujeme mít logiku [restartu připojení](#restart_connection) .

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (příliš mnoho požadavků) vrácených pro žádosti klientů

429 vrátí hodnotu, pokud počet **souběžných** připojení překračuje limit.

U **bezplatných** instancí je limit počtu **souběžných** připojení 20 u instancí **Standard** , limit počtu **souběžných** připojení **na jednotku** je 1 K, což znamená, že Unit100 umožňuje souběžná připojení 100-K.

Připojení zahrnují připojení klienta i serveru. [tady](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-messages-and-connections#how-connections-are-counted) najdete informace o tom, jak se započítávají připojení.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 Chyba při vyjednávání: Služba Azure Signaler ještě není připojená, zkuste to prosím znovu později.

### <a name="root-cause"></a>Původní příčina

Tato chyba se zobrazí, když není připojeno žádné připojení k serveru ke službě Azure Signal Service.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Pokud se server pokusí připojit ke službě signalizace Azure, povolte trasování na straně serveru a zjistěte podrobnosti o chybě.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Povolit protokolování ASP.NET Coreového signálu na straně serveru

Protokolování na straně serveru pro signalizaci ASP.NET Core se integruje s `ILogger` [protokolováním](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) založeným na ASP.NET Core Framework. Protokolování na straně serveru můžete povolit pomocí `ConfigureLogging` , ukázkového použití následujícím způsobem:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Kategorie protokolovacích nástrojů pro službu Azure Signaler vždy zahájí `Microsoft.Azure.SignalR` . Pokud chcete povolit podrobné protokoly z nástroje Azure Signal, nakonfigurujte předchozí předpony na `Debug` úroveň v **appsettings.js** souboru, jak je uvedeno níže:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Povolit trasování na straně serveru pro ASP.NET signál

Při použití sady SDK verze >= `1.0.0` můžete povolit trasování přidáním následujícího do `web.config` : ([Podrobnosti](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Pokles připojení klienta

Když je klient připojen ke službě Azure Signal, trvalé připojení mezi klientem a službou Azure Signal se někdy může vyřadit z různých důvodů. Tato část popisuje několik možností, které způsobují zrušení takového připojení a poskytuje některé pokyny k identifikaci hlavní příčiny.

### <a name="possible-errors-seen-from-the-client-side"></a>Možné chyby zjištěné na straně klienta

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Hlavní příčina:

Připojení klientů můžete vyřadit za různé okolnosti:
* `Hub`Vyvolá výjimku s příchozím požadavkem.
* Po připojení serveru, na které klient směruje, se v části níže uvádí podrobnosti o tom, jak [připojení serveru poklesne](#server_connection_drop).
* Dojde-li k potížím se síťovým připojením mezi klientem a službou Signal.
* Když služba signalizace obsahuje některé interní chyby, jako je třeba restartování instance, převzetí služeb při selhání, nasazení atd.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

1. Otevřete protokol na straně serveru aplikace a zjistěte, jestli došlo k nějakému abnormálnímu výskytu.
2. Zkontrolujte protokol událostí na straně aplikačního serveru a zjistěte, jestli se App Server restartoval.
3. Vytvořte problém, abychom vám poskytli časový rámec, a pošlete nám e-mail s názvem prostředku.


## <a name="client-connection-increases-constantly"></a>Nepřetržité zvyšování připojení klienta

Příčinou může být nesprávné použití připojení klienta. Pokud někdo zazapomene k zastavení nebo odstranění klienta signalizace, připojení zůstane otevřené.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Možné chyby zjištěné na základě metriky signalizace, která je v části monitorování v nabídce Azure Portal prostředků

V metrikách služby Azure Signal je nepřetržitý nárůst připojení klientů na dlouhou dobu.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Nepřetržité zvyšování připojení klienta":::

### <a name="root-cause"></a>Hlavní příčina:

Připojení klienta k signalizaci `DisposeAsync` není nikdy voláno, připojení zůstane otevřené.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

1. Zkontroluje, jestli se klient nástroje pro signalizaci **nikdy** nezavřel.

### <a name="solution"></a>Řešení

Ověřte, zda je ukončeno připojení. `HubConnection.DisposeAsync()`Po použití volání zastavte ručně.

Například:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Běžné použití nesprávného připojení klienta

#### <a name="azure-function-example"></a>Příklad funkce Azure 

K tomuto problému často dochází, když někdo naváže připojení klienta k signalizaci v metodě Azure Function namísto toho, aby se stal statickým členem vaší třídy funkcí. Je možné, že jste navázali jenom jedno připojení klienta, ale v části nabídka prostředků, která je v oblasti monitorování Azure Portal, se všechna tato připojení vynechává až po restartování služby Azure Function nebo Azure Signal. Důvodem je to, že pro **každý** požadavek služba Azure Function vytvoří **jedno** připojení klienta, Pokud nezastavíte připojení klienta v metodě Function, klient zachová připojení ke službě Azure Signal.

#### <a name="solution"></a>Řešení

* Pokud používáte klienty signalizace ve službě Azure Functions nebo klienta služby Signal jako typ singleton, nezapomeňte připojení klienta zavřít.
* Místo používání klientů služby Signal v Azure Functions můžete vytvořit klienty signalizace kdekoli jinde a použít [Azure Functions vazby pro službu Azure signaler](https://github.com/Azure/azure-functions-signalrservice-extension) k [vyjednání](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) klienta s nástrojem Azure Signal. A můžete také využít vazbu k [posílání zpráv](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Ukázky pro vyjednávání klienta a odeslání zpráv najdete [tady](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Další informace najdete [tady](https://github.com/Azure/azure-functions-signalrservice-extension).
* Pokud používáte klienty signalizace ve službě Azure Functions, může to být lepší architektura pro váš scénář. Ověřte, jestli navrhujete správnou architekturu bez serveru. [Pomocí vazeb služby signalizace v Azure Functions můžete odkazovat na aplikace bez serveru v reálném čase](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Připojení k serveru je odložené.

Po spuštění aplikačního serveru začne sada Azure SDK iniciovat připojení serveru ke vzdálenému signálu Azure. Jak je popsáno v [části interní služby pro službu Azure signaler, služba](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)Azure Signal směruje příchozí přenosy klientů na tato připojení serveru. Jakmile bude připojení k serveru vyřazeno, všechna připojení klientů, která obsluhuje, budou také zavřena.

Vzhledem k tomu, že připojení mezi serverem App Server a službou signalizace jsou trvalá připojení, může docházet k problémům se síťovým připojením. V sadě SDK serveru jsme **vždycky znovu připojili** strategii k připojením k serveru. Jako osvědčený postup doporučujeme také uživatelům, aby do klientů s náhodným zpožděním přidali logiku průběžného opětovného připojení, abyste se vyhnuli obrovskému počtu souběžných požadavků na server.

V pravidelných intervalech jsou k dispozici nové verze pro službu signalizace Azure a někdy se jedná o opravy operačního systému v rámci Azure nebo upgrady nebo občasná přerušení z našich závislých služeb. To může vést k krátkodobému přerušení služby, ale pokud má Klientská strana mechanismus odpojení nebo opětovného připojení, je dopad minimální, protože kterákoli strana na straně klienta způsobila odpojení a opětovné připojení.

Tato část popisuje několik možností, které vedou k odkládání připojení k serveru, a poskytuje některé pokyny k identifikaci hlavní příčiny.

### <a name="possible-errors-seen-from-server-side"></a>Možné chyby zjištěné na straně serveru:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Hlavní příčina:

Připojení služby serveru je uzavřeno pomocí **ASRS**(**Zure** **s** Ignal **R** **S** lužby).

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

1. Otevřete protokol na straně serveru aplikace a zjistěte, jestli došlo k nějakému abnormálnímu výskytu.
2. Zkontrolujte protokol událostí na straně aplikačního serveru a zjistěte, jestli se App Server restartoval.
3. Vytvořte problém, abychom vám poskytli časový rámec, a pošlete nám e-mail s názvem prostředku.

## <a name="tips"></a>Tipy

<a name="view_request"></a>

* Jak zobrazit odchozí požadavek z klienta?
Vezměte například ASP.NET Core jeden příklad (ASP.NET One je podobný):
    * Z prohlížeče:

        Využijte Chrome jako příklad. pomocí **klávesy F12** můžete otevřít okno konzoly a přepnout na kartu **síť** . Je možné, že budete muset aktualizovat stránku pomocí klávesy **F5** a zachytit síť od začátku.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Síť zobrazení Chrome":::

    * Z klienta jazyka C#:

        Místní webové přenosy můžete zobrazit pomocí [Fiddler](https://www.telerik.com/fiddler). Přenosy protokolu WebSocket se podporují od Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler zobrazení sítě" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Jak restartovat připojení klienta?
    
    Tady jsou [Ukázkové kódy](https://github.com/Azure/azure-signalr/tree/dev/samples) , které obsahují pravidla restartování logiky připojení s strategií *vždy opakovat* :

    * [Klient ASP.NET Core C#](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [Klient ASP.NET Core JavaScript](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [Klient ASP.NET C#](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [Klient ASP.NET JavaScript](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli o tom, jak řešit běžné problémy. Můžete si také přečíst obecnější metody řešení potíží. 

> [!div class="nextstepaction"]
> [Řešení potíží s připojením a doručováním zpráv](./signalr-howto-troubleshoot-method.md)
