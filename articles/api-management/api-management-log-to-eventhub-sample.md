---
title: Monitorování rozhraní API pomocí Azure API Management, Event Hubs a Moesif
titleSuffix: Azure API Management
description: Ukázková aplikace demonstrující zásady log-to-eventhub propojením Azure API Management, Azure Event Hubs a Moesif pro protokolování a monitorování HTTP
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: 4a0717bf7a284668af4808acae3050cc7f42f836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442527"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorování rozhraní API pomocí Azure API Management, Event Hubs a Moesif
[Služba API Management](api-management-key-concepts.md) poskytuje mnoho funkcí pro zlepšení zpracování požadavků HTTP odeslaných do vašeho rozhraní HTTP API. Existence požadavků a odpovědí je však přechodné. Požadavek je a toky prostřednictvím služby API Management do back-endového rozhraní API. Vaše rozhraní API zpracuje požadavek a odpověď toky zpět do příjemce rozhraní API. Služba API Management uchovává některé důležité statistiky o rozhraních API pro zobrazení na řídicím panelu portálu Azure, ale kromě toho jsou podrobnosti pryč.

Pomocí zásad log-to-eventhub ve službě API Management můžete odeslat všechny podrobnosti z požadavku a odpovědi do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existuje celá řada důvodů, proč můžete chtít generovat události ze zpráv HTTP odesílaných do vašich api. Některé příklady zahrnují auditní záznam aktualizací, analýzy využití, upozornění na výjimky a integrace třetích stran.

Tento článek ukazuje, jak zachytit celý požadavek HTTP a odpověď zprávy, odeslat do centra událostí a potom předat tuto zprávu službě jiného výrobce, která poskytuje služby protokolování a monitorování HTTP.

## <a name="why-send-from-api-management-service"></a>Proč odesílat ze služby správy rozhraní API?
Je možné napsat http middleware, který lze připojit do http api rámců pro zachycení HTTP požadavků a odpovědí a jejich krmení do protokolovacích a monitorovacích systémů. Nevýhodou tohoto přístupu je middleware HTTP musí být integrovándo rozhraní API back-endu a musí odpovídat platformě rozhraní API. Pokud existuje více api, pak každý z nich musí nasadit middleware. Často existují důvody, proč nelze aktualizovat back-endová api.

Použití služby Azure API Management k integraci s infrastrukturou protokolování poskytuje centralizované a platformně nezávislé řešení. Je také škálovatelná, částečně kvůli možnostem [geografické replikace](api-management-howto-deploy-multi-region.md) azure api managementu.

## <a name="why-send-to-an-azure-event-hub"></a>Proč odesílat do Centra událostí Azure?
Je rozumné se ptát, proč vytvářet zásady, které jsou specifické pro Azure Event Hubs? Existuje mnoho různých míst, kde bych mohl chtít přihlásit své požadavky. Proč prostě neposlat žádosti přímo do konečného cíle?  To je možnost. Při vytváření požadavků na protokolování ze služby správy rozhraní API je však nutné zvážit, jak protokolování zpráv vliv na výkon rozhraní API. Postupné zvyšování zatížení lze zpracovat zvýšením dostupných instancí systémových součástí nebo využitím geografické replikace. Krátké špičky v provozu však může způsobit požadavky, které mají být zpožděny, pokud požadavky na protokolování infrastruktury začít zpomalit při zatížení.

Azure Event Hubs je navržen tak, aby příchozí obrovské objemy dat, s kapacitou pro řešení mnohem vyšší počet událostí, než je počet požadavků HTTP většina procesů API. Centrum událostí funguje jako druh sofistikované vyrovnávací paměti mezi službou správy rozhraní API a infrastrukturou, která ukládá a zpracovává zprávy. Tím zajistíte, že výkon rozhraní API nebude trpět z důvodu infrastruktury protokolování.

Jakmile jsou data předána do centra událostí, je trvalé a bude čekat na spotřebitele Event Hub zpracovat. Centrum událostí se nestará o to, jak je zpracována, jen se stará o to, aby zpráva byla úspěšně doručena.

Event Hubs má možnost streamovat události do více skupin spotřebitelů. To umožňuje události, které mají být zpracovány v různých systémech. To umožňuje podporovat mnoho scénářů integrace bez uvedení další zpoždění na zpracování požadavku rozhraní API v rámci služby api management jako pouze jednu událost musí být generovány.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zásada odesílání zpráv aplikace nebo http
Centrum událostí přijímá data událostí jako jednoduchý řetězec. Obsah toho řetězce je na vás. Abybylo možné zabalit požadavek HTTP a odeslat jej do centra událostí, musíme formátovat řetězec s informacemi o požadavku nebo odpovědi. V situacích, jako je tato, pokud existuje existující formát, který můžeme znovu použít, pak nemusíme psát vlastní kód analýzy. Zpočátku jsem zvažoval použití [HAR](http://www.softwareishard.com/blog/har-12-spec/) pro odesílání HTTP požadavků a odpovědí. Tento formát je však optimalizován pro ukládání posloupnosti požadavků HTTP ve formátu json. Obsahuje řadu povinných prvků, které přidaly zbytečnou složitost pro scénář předávání zprávy HTTP po drátě.

Alternativní možností bylo použít `application/http` typ média, jak je popsáno ve specifikaci [HTTP RFC 7230](https://tools.ietf.org/html/rfc7230). Tento typ média používá přesně stejný formát, který se používá k skutečně odesílání zpráv HTTP po drátě, ale celá zpráva může být umístěna v těle jiného požadavku HTTP. V našem případě použijeme tělo jako zprávu, kterou pošleme do event hubů. Pohodlně existuje analyzátor, který existuje v [knihovnách Microsoft ASP.NET Web API 2.2 Client,](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) který může `HttpRequestMessage` `HttpResponseMessage` analyzovat tento formát a převést jej na nativní a objekty.

Abychom mohli vytvořit tuto zprávu, musíme využít výhod [výrazů zásad](/azure/api-management/api-management-policy-expressions) založených na C# ve správě rozhraní Azure API. Tady je zásada, která odešle zprávu o požadavku HTTP do Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Prohlášení o zásadách
O tomto výrazu politiky stojí za zmínku několik konkrétních věcí. Zásada log-to-eventhub má atribut s názvem logger-id, který odkazuje na název úhozu, který byl vytvořen v rámci služby API Management. Podrobnosti o tom, jak nastavit protokolovač centra událostí ve službě API Management, najdete v dokumentu [Jak protokolovat události do Center událostí Azure ve správě rozhraní Azure API](api-management-howto-log-event-hubs.md). Druhý atribut je volitelný parametr, který instruuje centra událostí, který oddíl uložit zprávu. Centra událostí používá oddíly k povolení škálovatelnosti a vyžadují minimálně dva. Objednané doručení zpráv je zaručeno pouze v rámci oddílu. Pokud nebudeme instruovat Event Hub, ve kterém oddíl umístit zprávu, používá algoritmus kruhového dotazování k distribuci zatížení. To však může způsobit, že některé naše zprávy budou zpracovány mimo provoz.

### <a name="partitions"></a>Oddíly
Chcete-li zajistit, aby naše zprávy byly doručovány spotřebitelům v pořádku a využít možnosti distribuce zatížení oddílů, rozhodl jsem se odeslat zprávy o požadavcích HTTP na jeden oddíl a zprávy odpovědi HTTP do druhého oddílu. Tím je zajištěno rovnoměrné rozložení zatížení a můžeme zaručit, že všechny požadavky budou spotřebovány v pořadí a všechny odpovědi jsou spotřebovány v pořadí. Je možné, že odpověď bude spotřebována před odpovídající požadavek, ale protože to není problém, protože máme jiný mechanismus pro korelaci požadavků na odpovědi a víme, že požadavky vždy předcupují před odpovědi.

### <a name="http-payloads"></a>Datové části PROTOKOLU HTTP
Po sestavení `requestLine`, zkontrolujeme, zda tělo požadavku by měla být zkrácena. Tělo požadavku je zkráceno na pouze 1024. To může být zvýšena, ale jednotlivé zprávy Event Hub jsou omezeny na 256 KB, takže je pravděpodobné, že některá těla zpráv HTTP se nevejdou do jedné zprávy. Při provádění protokolování a analýzy lze odvodit značné množství informací pouze z řádku a záhlaví požadavků HTTP. Mnoho požadavků na úložiště api také vrací pouze malá těla, a proto je ztráta hodnoty informací zkrácením velkých těl poměrně minimální ve srovnání se snížením nákladů na přenos, zpracování a ukládání, aby byl veškerý obsah těla. Poslední poznámka o zpracování těla je, `true` že `As<string>()` musíme předat metodu, protože čteme obsah těla, ale byl také chtěl backend API, aby mohli číst tělo. Předáním true této metody způsobíme, že tělo bude uloženo do vyrovnávací paměti, aby bylo možné jej přečíst podruhé. To je důležité být vědomi, pokud máte rozhraní API, které provádí nahrávání velkých souborů nebo používá dlouhé dotazování. V těchto případech by bylo nejlepší, aby se zabránilo čtení těla vůbec.

### <a name="http-headers"></a>Hlavičky PROTOKOLU HTTP
Hlavičky HTTP lze přenést do formátu zprávy v jednoduchém formátu páru klíč/hodnota. Rozhodli jsme se odstranit určitá bezpečnostní pole, abychom se vyhnuli zbytečnému úniku informací o pověřeních. Je nepravděpodobné, že by se klíče rozhraní API a další pověření používaly pro účely analýzy. Pokud chceme provést analýzu uživatele a konkrétního produktu, který používají, `context` pak bychom to mohli získat z objektu a přidat to do zprávy.

### <a name="message-metadata"></a>Metadata zprávy
Při vytváření úplné zprávy k odeslání do centra událostí, první `application/http` řádek není ve skutečnosti součástí zprávy. První řádek je další metadata skládající se z toho, zda zpráva je požadavek nebo odpověď zprávy a ID zprávy, který se používá ke korelaci požadavků na odpovědi. ID zprávy se vytvoří pomocí jiné zásady, která vypadá takto:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Mohli jsme vytvořit zprávu požadavku, uloženou v proměnné, dokud nebyla vrácena odpověď a poté odeslala požadavek a odpověď jako jednu zprávu. Nicméně odesláním požadavku a odpovědi nezávisle a pomocí id zprávy ke korelaci dvou, dostaneme trochu větší flexibilitu ve velikosti zprávy, schopnost využít více oddílů při zachování pořadí zpráv a požadavek se zobrazí v našem řídicím panelu s protokolováním dříve. Mohou také existovat některé scénáře, kde je platná odpověď nikdy odeslána do centra událostí, pravděpodobně z důvodu závažné chyby požadavku ve službě api management, ale stále máme záznam o požadavku.

Zásada pro odeslání zprávy HTTP odpovědi vypadá podobně jako požadavek, a proto úplná konfigurace zásad vypadá takto:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Zásada `set-variable` vytvoří hodnotu, která `log-to-eventhub` je přístupná zásady v `<inbound>` části a `<outbound>` části.

## <a name="receiving-events-from-event-hubs"></a>Příjem událostí z centra událostí
Události z Centra událostí Azure jsou přijímány pomocí [protokolu AMQP](https://www.amqp.org/). Tým služby Microsoft Service Bus zpřístupnil klientské knihovny, aby usnadnil náročné události. Jsou podporovány dva různé přístupy, jeden je *přímý* `EventProcessorHost` spotřebitel a druhý používá třídu. Příklady těchto dvou přístupů naleznete v [Programovací příručce Centra událostí](../event-hubs/event-hubs-programming-guide.md). Krátká verze rozdílů je, `Direct Consumer` vám dává úplnou `EventProcessorHost` kontrolu a dělá některé instalatérské práce pro vás, ale dělá určité předpoklady o tom, jak zpracovat tyto události.

### <a name="eventprocessorhost"></a>EventProcessorHost
V této ukázce používáme `EventProcessorHost` pro jednoduchost, ale nemusí být nejlepší volbou pro tento konkrétní scénář. `EventProcessorHost`dělá těžkou práci, abyste se ujistili, že se nemusíte starat o problémy s vlákny v rámci určité třídy procesoru událostí. V našem scénáři však jednoduše převádíme zprávu do jiného formátu a předáváme ji do jiné služby pomocí asynchronní metody. Není nutné aktualizovat sdílený stav, a proto žádné riziko problémů s podprocesem. Pro většinu `EventProcessorHost` scénářů, je pravděpodobně nejlepší volbou, a to je jistě jednodušší možnost.

### <a name="ieventprocessor"></a>IEventProcesor
Centrální koncept při `EventProcessorHost` použití je vytvořit `IEventProcessor` implementaci rozhraní, která `ProcessEventAsync`obsahuje metodu . Podstata této metody je uvedena zde:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Seznam EventData objekty jsou předány do metody a my iterate přes tento seznam. Bajty každé metody jsou analyzovány do objektu HttpMessage a tento objekt je předán instanci IHttpMessageProcessor.

### <a name="httpmessage"></a>Zpráva httpmessage
Instance `HttpMessage` obsahuje tři části dat:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

Instance `HttpMessage` obsahuje `MessageId` identifikátor GUID, který nám umožňuje připojit požadavek HTTP k odpovídající odpovědi HTTP a logickou hodnotu, která identifikuje, zda objekt obsahuje instanci HttpRequestMessage a HttpResponseMessage. Pomocí předdefinované třídy HTTP `System.Net.Http`z aplikace jsem byl `application/http` schopen využít analyzační kód, který je součástí . `System.Net.Http.Formatting`  

### <a name="ihttpmessageprocessor"></a>IhttpMessageProcesor
Instance `HttpMessage` je pak předána `IHttpMessageProcessor`k implementaci , což je rozhraní, které jsem vytvořil pro oddělení příjmu a interpretace události z Centra událostí Azure a její skutečné zpracování.

## <a name="forwarding-the-http-message"></a>Předání zprávy HTTP
Pro tuto ukázku jsem se rozhodl, že by bylo zajímavé, aby se zasadila http žádost se [moesif API Analytics](https://www.moesif.com). Moesif je cloudová služba, která se specializuje na analýzu http a ladění. Mají bezplatnou úroveň, takže je snadné vyzkoušet a umožňuje nám vidět požadavky HTTP v reálném čase, které procházejí naší službou API Management.

Implementace `IHttpMessageProcessor` vypadá takto,

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

Využívá `MoesifHttpMessageProcessor` [c# knihovny ROZHRANÍ API pro Moesif,](https://www.moesif.com/docs/api?csharp#events) která usnadňuje nabízení dat událostí HTTP do jejich služby. Chcete-li odeslat http data do rozhraní Moesif Collector API, potřebujete účet a ID aplikace. Získáte Moesif App Id vytvořením účtu na [webových stránkách Moesif](https://www.moesif.com) a pak přejděte na _pravé horní menu_ -> _App Setup_.

## <a name="complete-sample"></a>Kompletní ukázka
[Zdrojový kód](https://github.com/dgilling/ApimEventProcessor) a testy pro ukázku jsou na GitHubu. Ke spuštění ukázky pro sebe potřebujete [službu api Management Service](get-started-create-service-instance.md), připojené centrum [událostí](api-management-howto-log-event-hubs.md)a [účet úložiště.](../storage/common/storage-create-storage-account.md)   

Ukázka je jen jednoduchá konzolová aplikace, která naslouchá událostem přicházejícím z `EventRequestModel` `EventResponseModel` Event Hubu, převádí je na Moesif a objekty a pak je předává do rozhraní Moesif Collector API.

V následujícím animovaném obrázku se zobrazí požadavek na rozhraní API na portálu pro vývojáře, aplikace konzoly zobrazující přijatou, zpracovávanou a předávanou zprávu a potom požadavek a odpověď, které se zobrazí v datovém proudu událostí.

![Prokázání žádosti předávané runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje ideální místo pro zachycení provozu HTTP, který putuje do a z vašich rozhraní API. Azure Event Hubs je vysoce škálovatelné, nízkonákladové řešení pro zachycení tohoto provozu a jeho navádění do sekundárních systémů zpracování pro protokolování, monitorování a další sofistikované analýzy. Připojení k systémům sledování provozu třetích stran, jako je Moesif, je stejně jednoduché jako několik desítek řádků kódu.

## <a name="next-steps"></a>Další kroky
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímání zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci služby API Management a Event Hubs
  * [Jak protokolovat události do Azure Event Hubs ve správě Azure API](api-management-howto-log-event-hubs.md)
  * [Odkaz na entitu loggeru](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [odkaz na zásady log-to-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
