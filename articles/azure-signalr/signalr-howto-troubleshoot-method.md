---
title: Postup řešení potíží pro službu signalizace Azure
description: Přečtěte si, jak řešit potíže s připojením a doručováním zpráv.
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 4b0b85b08c3f813440d556c61ba5e290ac200049
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686761"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Řešení potíží s připojením a doručováním zpráv

Tento návod zavádí několik způsobů, jak pomocí samočinného diagnostiky najít hlavní příčinu přímo nebo zúžit problém. Výsledek samočinné diagnostiky je také užitečný při informování do nás pro další zkoumání.

Nejdřív je potřeba ověřit z Azure Portal, který je [ServiceMode](https://docs.microsoft.com/azure/azure-signalr/concept-service-mode) službou Azure Signal (označuje se také jako **ASRS**).

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* Informace o režimu najdete v `Default` tématu [řešení potíží s výchozím režimem](#default_mode_tsg) .

* Informace o režimu najdete v `Serverless` tématu [řešení potíží s režimem bez serveru](#serverless_mode_tsg) .

* Postup najdete v `Classic` tématu [řešení potíží v klasickém režimu](#classic_mode_tsg) .

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>Řešení potíží s výchozím režimem

Když je **ASRS** ve *výchozím* režimu, existují **tři** role: *klient*, *Server* a *Služba*:

* *Klient*: *klient* představuje klienty připojené k **ASRS**. Trvalá připojení připojující klienta a **ASRS** se nazývají *připojení klientů* v těchto pokynech.

* *Server*: *Server* představuje server, který slouží pro vyjednávání klienta a hostitele `Hub` logiky signálů. A trvalá připojení mezi *serverem* a **ASRS** se nazývají *připojení k serveru* v těchto pokynech.

* *Služba*: *Služba* je krátký název **ASRS** v těchto pokynech.

Podrobné informace o celé architektuře a pracovním postupu najdete v [interních informacích o službě Azure Signal](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Existuje několik způsobů, které vám pomůžou s zúžením tohoto problému. 

* Pokud potíže nastanou v cestě, jak je, nebo je reprodukci, je přímým způsobem možné zobrazit probíhající provoz. 

* Pokud je problém reprodukci, mohou vám trasování a protokoly pomáhat.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Postup zobrazení provozu a zúžení problému

Zachycení průběžného provozu je nejpřímější způsob, jak tento problém zúžit. [Trasování sítě](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces) můžete zachytit pomocí níže uvedených možností:

* [Shromáždění trasování sítě pomocí Fiddler](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces)

* [Shromáždění trasování sítě pomocí tcpdump](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Shromáždění trasování sítě v prohlížeči](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Žádosti klientů

V případě trvalého připojení signálního serveru nejprve `/negotiate` k hostovanému aplikačnímu serveru a následnému přesměrování na službu Azure Signal Service a následně vytvoří skutečné trvalé připojení ke službě Azure Signal. Podrobné pokyny najdete v [interních informacích o službě Azure Signal](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Pomocí trasování sítě na straně klienta zkontrolujte, který požadavek se nezdařil s kódem stavu a jaká odpověď je, a vyhledejte řešení v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide).

#### <a name="server-requests"></a>Žádosti serveru

*Server* signalizace udržuje *připojení serveru* mezi *serverem* a *službou*. Po spuštění aplikačního serveru se spustí připojení protokolu **WebSocket** ke službě Azure Signal. Všechny přenosy klientů jsou směrovány prostřednictvím služby Azure Signal Service na tyto *připojení k serveru* a poté odeslány na `Hub` . Když se *připojení k serveru* uvolní, budou to mít vliv na klienty směrované na toto připojení k tomuto *serveru* . Naše sada SDK služby Azure Signal má logiku "vždy opakovat", aby *připojení serveru* bylo možné znovu připojit s maximální prodlevou od 1 minuty, aby se minimalizoval dopad.

*Připojení k serveru* je možné vyřadit z důvodu nestability sítě nebo pravidelné údržby služby signalizace Azure nebo aktualizací/údržby hostovaného serveru aplikace. Pokud má Klientská strana mechanismus odpojení nebo opětovného připojení, je dopad minimální, protože kterákoli strana klienta způsobila odpojení a opětovné připojení.

Zobrazit trasování sítě na straně serveru pro zjištění stavového kódu a podrobností o chybě, proč *připojení serveru* poklesne nebo je *službou* zamítnuto, a vyhledejte hlavní příčinu v [Průvodci odstraňováním potíží](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide).


### <a name="how-to-add-logs"></a>Postup přidání protokolů

Protokoly mohou být užitečné k diagnostice problémů a sledování stavu spuštění.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Postup povolení protokolu na straně klienta

Prostředí protokolování na straně klienta je přesně stejné jako při použití signalizace s místním hostováním.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Povolit protokolování na straně klienta pro `ASP.NET Core SignalR`

* [Protokolování klienta JavaScript](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Protokolování klienta .NET](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Povolit protokolování na straně klienta pro `ASP.NET SignalR`

* [Klient .NET](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Povolení trasování v klientech Windows Phone 8](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Povolení trasování v klientovi JavaScriptu](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Postup povolení protokolu na straně serveru

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Povolit protokolování na straně serveru pro `ASP.NET Core SignalR`

Protokolování na straně serveru pro `ASP.NET Core SignalR` integraci s `ILogger` [protokolováním](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) na základě poskytované v `ASP.NET Core` rozhraní. Protokolování na straně serveru můžete povolit pomocí `ConfigureLogging` , ukázkového použití následujícím způsobem:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Kategorie protokolovacích nástrojů pro službu Azure Signaler vždy zahájí `Microsoft.Azure.SignalR` . Pokud chcete povolit podrobné protokoly z nástroje Azure Signal, nakonfigurujte předchozí předpony na `Information` úroveň v **appsettings.js** souboru, jak je uvedeno níže:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Zkontroluje, jestli nejsou zaznamenané žádné neobvyklé protokoly upozornění a chyb. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Povolit trasování na straně serveru pro `ASP.NET SignalR`

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

Zkontroluje, jestli nejsou zaznamenané žádné neobvyklé protokoly upozornění a chyb. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Jak povolit protokoly v rámci služby Azure Signal Service

Můžete také [Povolit diagnostické protokoly](https://docs.microsoft.com/azure/azure-signalr/signalr-tutorial-diagnostic-logs) pro službu Azure Signal Service. tyto protokoly poskytují podrobné informace o každém připojení připojeném ke službě Azure Signal.

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>Řešení potíží s režimem bez serveru

Když je **ASRS** v režimu bez *serveru* , podporuje ASP.NET Core pouze režim **signalizace** `Serverless` a **ASP.NET signál** nepodporuje tento režim **NOT** .

Pro diagnostiku problémů s připojením v `Serverless` režimu je nejpřímější způsob, jak [Zobrazit klientský provoz](#view_traffic_client). Povolení [protokolů na straně klienta](#add_logs_client) a [protokolů na straně služby](#add_logs_server) může být užitečné také.

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>Řešení potíží s klasickým režimem

`Classic` režim je zastaralý a nedoporučuje se ho používat. V tomto režimu používá služba Azure Signal připojení připojeného *serveru* k určení, jestli je aktuální služba v `default` režimu nebo režimu `serverless` . To může vést k určitým problémům s připojením klientů, protože když dojde k náhlému odpojení všech připojení k připojenému *serveru*, například kvůli nestabilitě sítě, služba Azure Signal se nyní přepnula na `serverless` režim a klienti připojení během této doby nebudou nikdy směrováni do hostovaného aplikačního serveru. Povolte [protokoly na straně služby](#add_logs_server) a ověřte, jestli nejsou nahrané žádné klienty, jako `ServerlessModeEntered` kdyby máte hostovaný aplikační server, ale někteří klienti nikdy nedosáhnou na straně aplikačního serveru. Pokud existuje, [přeruší tato klientská připojení](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) a umožní, aby se klienti mohli restartovat.

`classic`Problémy s připojením do režimu řešení potíží a doručování zpráv se podobají problémům s [výchozím režimem](#default_mode_tsg).

## <a name="service-health"></a>Stav služeb

Rozhraní API pro stav služby můžete kontrolovat.

* Požadavek: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Stavový kód odpovědi:
  * 200: v pořádku.
  * 503: vaše služba není v pořádku. Můžete:
    * Počkejte několik minut na automatické obnovení.
    * Ověřte, že je IP adresa shodná s IP adresou z portálu.
    * Nebo restartujte instanci.
    * Pokud všechny výše uvedené možnosti nefungují, kontaktujte nás přidáním nové žádosti o podporu v Azure Portal.

Další informace o [zotavení po havárii](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-disaster-recovery).

## <a name="next-steps"></a>Další kroky

V této příručce jste se dozvěděli, jak řešit potíže s připojením a doručováním zpráv. Můžete se také dozvědět, jak řešit běžné problémy. 

> [!div class="nextstepaction"]
> [Průvodce odstraňováním potíží](./signalr-howto-troubleshoot-guide.md)