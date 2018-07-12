---
title: Monitorování rozhraní API pomocí Azure API Management, Event Hubs a Runscope | Dokumentace Microsoftu
description: Ukázková aplikace představením toho zásady protokolu eventhub připojení Azure API Management, Azure Event Hubs a Runscope pro protokol HTTP, protokolování a monitorování
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
ms.openlocfilehash: 4c58be8f501e72027e1692ceb73552a3f252f92a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38603174"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Sledování vašich rozhraní API pomocí Azure API Management, Event Hubs a Runscope
[Služby API Management](api-management-key-concepts.md) poskytuje mnoho funkcí pro zvýšení zpracování požadavků HTTP odeslané do vašeho rozhraní API protokolu HTTP. Je však přechodné existenci požadavky a odpovědi. Požadavku a prochází přes službu API Management k rozhraní API back-endu. Vaše rozhraní API zpracuje požadavek a odpověď prochází zpět do rozhraní API příjemců. Služba API Management zajišťuje několik důležitých statistik o rozhraní API pro zobrazení v řídicím panelu portálu Azure, ale dalších fázích můžete využít, že podrobnosti jsou pryč.

Pomocí zásad protokolu do centra událostí ve službě API Management je možné posílat žádné podrobnosti z požadavku a odpovědi na [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). Existuje mnoho různých důvodů, proč můžete chtít generovat události z protokolu HTTP zprávy odesílané do rozhraní API. Mezi příklady patří záznam pro audit aktualizací, analýzy využití, výstrahy výjimek, a integraci třetích stran.   

Tento článek ukazuje, jak zachytit celou zprávu požadavku a odpovědi HTTP, odesílat do centra událostí a pak propojení této zprávy do služby třetí strany, která poskytuje protokolování a monitorování služeb HTTP.

## <a name="why-send-from-api-management-service"></a>Proč odeslat ze služby API Management?
Je možné psát HTTP middlewaru, který můžete připojit k rozhraní HTTP API zachytit požadavky a odpovědi HTTP a jejich vstupu do protokolování a monitorování systémů. Nevýhodou tento přístup je HTTP middleware je potřeba integrovat do back-endového rozhraní API a platformu rozhraní API se musí shodovat. Pokud existuje více rozhraní API, musíte nasadit každé z nich middleware. Často jsou důvody, proč se nedá aktualizovat back-endového rozhraní API.

Integrace s infrastrukturou protokolování pomocí služby Azure API Management poskytuje centralizovaný a nezávislý na platformě řešení. Také je škálovatelná, v části kvůli k [geografickou replikaci](api-management-howto-deploy-multi-region.md) možnosti služby Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Proč odesílat do centra událostí Azure?
Je možné logicky k požádat, proč vytvořit zásadu, která je specifická pro Azure Event Hubs? Kde můžu chcete protokolovat Moje žádosti o mnoha různých místech. Stačí poslat případně proč bezpečná není požadavků přímo do konečného místa určení?  To je možné. Při protokolování žádostí ze služby API management, je však nutné vzít v úvahu, jak protokolování zpráv ovlivnit výkon rozhraní API. Postupný nárůst zatížení může zpracovat, zvýšením dostupné instance součásti systému, nebo s využitím geografické replikace. Krátký špičkám provozu však může způsobit požadavky zpozdit. Pokud požadavky na infrastrukturu protokolování start ke zpomalení při zatížení.

Azure Event Hubs je navržena pro příchozí přenos dat obrovské objemy dat, s kapacitou pro pracující s mnohem vyšší počet událostí, než počet požadavků HTTP většina proces rozhraní API. Centrum událostí funguje jako sofistikované vyrovnávací paměti mezi vaše služba API management a infrastrukturu, která ukládá a zpracovávat zprávy. Tím se zajistí, že výkon rozhraní API nebude dochází z důvodu protokolování infrastruktury.  

Po uplynutí data do centra událostí je trvalý a bude čekat příjemců centra událostí ke zpracování. Centrum událostí nezáleží na tom, jak se zpracovávají, je právě dbá na ochranu a ujistěte se, že zpráva se úspěšně doručila.     

Služba Event Hubs má schopnost datový proud událostí do více skupin konzumentů. To umožňuje událostí pro zpracování různých systémech. Díky tomu podporuje mnoho scénářů integrace bez vložení Přidání zpoždění na zpracování požadavku rozhraní API ve službě API Management, jak je potřeba vytvořit jenom jednu událost.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zásady pro odesílání zpráv application/http
Centrum událostí přijme data událostí jako jednoduchým řetězcem. Obsah tohoto řetězce, záleží na vás. Aby bylo možné vytvořit požadavek HTTP balíček a odeslat ji do služby Event Hubs, musíme formátovací řetězec s informacemi o požadavku nebo odpovědi. V situacích, jako je to pokud je existující formát můžeme opakovaně, pak nemusí musíme napsat vlastní analýza kódu. Zpočátku jsem považovat za použití [HAR](http://www.softwareishard.com/blog/har-12-spec/) pro odesílání požadavků a odpovědí HTTP. Ale tento formát je optimalizovaná pro ukládání posloupnost požadavky HTTP ve formátu JSON. Obsahuje řadu povinné prvky, které přidá zbytečné složitosti pro scénář předání zprávy HTTP při přenosu.  

Alternativní možnost bylo použít `application/http` typ média, jak je popsáno ve specifikaci protokolu HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Tento typ média používá naprosto stejný formát, který se používá k přenosu ve skutečnosti odesílání zprávy protokolu HTTP, ale celá zpráva může být v těle další požadavek HTTP put. V našem případě stačí budeme používat text jako naše zprávy k odeslání do Event Hubs. Pohodlné, je analyzátor, který existuje v [Microsoft ASP.NET Web API 2.2 klienta](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) knihovny, které můžete tento formát analyzovat a převádět je do nativního `HttpRequestMessage` a `HttpResponseMessage` objekty.

Aby bylo možné vytvořit tuto zprávu, musíme využít výhod jazyka C# založeny [výrazy zásad](https://msdn.microsoft.com/library/azure/dn910913.aspx) ve službě Azure API Management. Tady je zásada, která odešle zprávu požadavku HTTP do služby Azure Event Hubs.

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
Existuje několik věcí konkrétní stojí za zmínku o tento výraz zásad. Zásady protokolu eventhub má atribut s názvem protokolovače id, které odkazuje na název protokolovací nástroj, který byl vytvořen v rámci služby API Management. Podrobnosti o tom, jak nastavit protokolovač centra událostí ve službě API Management najdete v dokumentu [jak protokolování událostí ve službě Azure Event Hubs ve službě Azure API Management](api-management-howto-log-event-hubs.md). Druhý atribut je volitelný parametr, který dává pokyn Event Hubs, která k ukládání zpráv v oddílu. Služba Event Hubs využívá k povolení škálovatelnost a vyžadují minimálně dva oddíly. Objednané dodání zprávy je zaručeno pouze v rámci oddílu. Pokud jsme vyzvat Centrum událostí v oddíl, který se umístí zprávu, používá algoritmus kruhové dotazování můžete distribuovat zatížení. Však, který může způsobit, že některé z našich zpráv pro zpracování nefungujících.  

### <a name="partitions"></a>Oddíly
Aby se zajistilo naše zprávy doručovaly do spotřebitelů v pořadí a využívat možnosti distribuce zatížení oddílů, volba odesílat zprávy požadavků HTTP na jeden oddíl a zpráv odpovědí HTTP na druhý oddíl. To zajistí, že distribuci zatížení a garantujeme, že všechny požadavky budou spotřebovány v pořadí a všechny odpovědi jsou využité v pořadí. Je možné pro odpověď využít před požadavku, ale to není problém jak jsme jiný mechanismus pro korelaci žádostí o odpovědi a víme, že žádosti vždy předcházet odpovědi.

### <a name="http-payloads"></a>HTTP datových částí
Po sestavení `requestLine`, zkontrolujeme Pokud textu požadavku by byla zkrácena. Text požadavku se zkrátí na pouze 1024. To může zvýšit, ale jednotlivé zprávy centra událostí jsou omezena na 256 KB, takže je pravděpodobné, že některé zprávy HTTP subjektech se nevejdou do jedné zprávy. Při protokolování a analýza významné množství informací může být odvozena z právě řádek požadavku HTTP a hlaviček. Také mnoho rozhraní API vracejí pouze malé těla požadavku a proto je poměrně minimální porovnání snížení přenosu, zpracování a náklady na úložiště zachovat veškerý obsah textu ztráty informací hodnoty zkrácením velké těla. Jeden poslední poznámku o zpracování textu je, že potřebujeme k předání `true` k As<string>– metoda () vzhledem k tomu, že jsme čtou text obsahu, ale byl zároveň chtěl back-endového rozhraní API, abyste mohli ke čtení textu. Předáním hodnotu PRAVDA, tato metoda způsobit jsme abych mohl ukládány do vyrovnávací paměti, takže je může přečíst podruhé. To je důležité znát Pokud máte rozhraní API, které provádí nahrávání velkých souborů nebo používá dlouhým dotazováním. V těchto případech by se nedoporučuje čtení textu vůbec.   

### <a name="http-headers"></a>Hlavičky protokolu HTTP
Hlavičky protokolu HTTP lze přenášet prostřednictvím do formátu zprávy ve formátu pár klíč hodnota. Jsme se rozhodli odstranit určitá citlivé pole zabezpečení, aby předešla úniku zbytečně přihlašovací údaje. Není pravděpodobné, že klíče rozhraní API a další přihlašovací údaje se použije pro účely analýzy. Pokud bychom chtěli provádět analýzu na uživatele a konkrétní produkt, které využívají a potom jsme mohli získat z `context` objektu a přidejte ho do zprávy.     

### <a name="message-metadata"></a>Zpráva metadat
Při sestavování zprávu o dokončení odesílat do centra událostí, první řádek není ve skutečnosti součástí `application/http` zprávy. První řádek je další metadata, který se skládá z Určuje, zda je zpráva žádost nebo odpověď a napište zprávu, ID, které slouží ke sladění požadavku odpovědi. ID zprávy je vytvořen pomocí jiné zásady, který vypadá takto:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Jsme může mít vytvořené zprávy s požadavkem, dokud odpověď byla vrácena a potom je odešlete žádost a odpověď jako jedna zpráva, která uloží do proměnné. Ale nezávisle na sobě odesílání požadavků a odpovědí a porovnat dva pomocí id zprávy, získáme o něco větší flexibilitu v velikost zprávy, možnosti využít více oddílů při zachování pořadí zpráv a požadavek se zobrazí v našem protokolování řídicí panel dříve. Také může existovat několik scénářů, kde platné odpovědi se nikdy neodesílá do centra událostí, pravděpodobně z důvodu chyby závažná žádost o služby API Management, ale máme záznam požadavku.

Vypadá podobně jako na žádost o zásadu odeslat zprávu odpovědi HTTP a tak kompletní zásady Konfigurace vypadá takto:

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

`set-variable` Zásady vytvoří hodnotu, která je přístupné pro oba `log-to-eventhub` zásad v `<inbound>` oddílu a `<outbound>` oddílu.  

## <a name="receiving-events-from-event-hubs"></a>Příjem událostí ze služby Event Hubs
Přijetí události ze služby Azure Event Hub pomocí [protokolu AMQP](http://www.amqp.org/). Tým Microsoft Service Bus provedli klientských knihoven, které jsou k dispozici pro usnadnění používání událostí. Existují dva různé přístupy, které jsou podporovány, je právě jeden *přímé příjemce* a druhý je použití `EventProcessorHost` třídy. Mezi příklady těchto dvou přístupů najdete v [Průvodce programováním pro Event Hubs](../event-hubs/event-hubs-programming-guide.md). Je zkrácený rozdíly `Direct Consumer` poskytuje úplnou kontrolu a `EventProcessorHost` provádí určitou část práce vložení pro ale znamená určité předpoklady o tom, jak zpracovat tyto události.  

### <a name="eventprocessorhost"></a>EventProcessorHost
V této ukázce používáme `EventProcessorHost` pro zjednodušení, ale nemusí být není nejlepší volbou pro tento konkrétní scénář. `EventProcessorHost` provede práci a ujistěte se, že se že nemusíte starat o dělení na vlákna problémy v rámci třídy procesoru určité události. V tomto scénáři, jsme se však jednoduše převod zprávu do jiného formátu a předáním podél do jiné služby pomocí asynchronní metody. Není nutné pro aktualizaci sdílený stav a proto riziko potíže s vlákny. Pro většinu scénářů `EventProcessorHost` je pravděpodobně nejlepší volbou a určitě je jednodušší možnosti.     

### <a name="ieventprocessor"></a>IEventProcessor
Při použití centrální koncept `EventProcessorHost` je vytvoření implementace `IEventProcessor` rozhraní, které obsahuje metodu `ProcessEventAsync`. Podstatě této metody je znázorněna zde:

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

Seznam objektů EventData jsou předány do metody a jsme iteraci tohoto seznamu. Počet bajtů jednotlivé metody jsou analyzovány do HttpMessage objektu a tento objekt je předán do instance IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage` Instance obsahuje tři druhy dat:

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

`HttpMessage` Instance obsahuje `MessageId` identifikátor GUID, který umožňuje připojení k odpovídající odpověď HTTP a logickou hodnotu, která určuje, zda objekt obsahuje instanci HttpRequestMessage a používá HttpResponseMessage požadavku HTTP. S použitím integrované HTTP třídy z `System.Net.Http`, mi bylo možné využívat `application/http` analýzu kódu, který je součástí `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage` Provádění potom předány instanci `IHttpMessageProcessor`, což je rozhraní jsem vytvořil oddělit přijímají a interpretaci událostí z Azure Event Hubs a skutečné zpracování.

## <a name="forwarding-the-http-message"></a>Předávání zpráv protokolu HTTP
V tomto příkladu jsem se rozhodla by být zajímavé vložit požadavku HTTP přes [Runscope](http://www.runscope.com). Runscope je Cloudová služba, která se specializuje na HTTP, ladění, protokolování a monitorování. Úroveň free mají tak snadno vyzkoušet a umožňuje nám to naleznete v tématu požadavky HTTP v reálném čase prostřednictvím naší službě API Management.

`IHttpMessageProcessor` Implementace vypadá takto,

```csharp
public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
{
   private HttpClient _HttpClient;
   private ILogger _Logger;
   private string _BucketKey;
   public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
   {
       _HttpClient = httpClient;
       var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
       _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
       _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
       _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
       _Logger = logger;
   }

   public async Task ProcessHttpMessage(HttpMessage message)
   {
       var runscopeMessage = new RunscopeMessage()
       {
           UniqueIdentifier = message.MessageId
       };

       if (message.IsRequest)
       {
           _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
           runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
       }
       else
       {
           _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
           runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
       }

       var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
       messagesLink.BucketKey = _BucketKey;
       messagesLink.RunscopeMessage = runscopeMessage;
       var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
       _Logger.LogDebug("Request sent to Runscope");
   }
}
```

Povedlo se využít [existující klientské knihovny pro Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) umožňující snadno o nasdílení změn `HttpRequestMessage` a `HttpResponseMessage` instancí až do své služby. Pokud chcete získat přístup k rozhraní API Runscope, musíte účtu a klíč rozhraní API. Pokyny k získání klíče rozhraní API najdete v [vytváření aplikací na používání rozhraní API Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) záznam dění na monitoru.

## <a name="complete-sample"></a>Úplnou ukázku
[Zdrojový kód](https://github.com/darrelmiller/ApimEventProcessor) a jsou testy pro ukázku na Githubu. Budete potřebovat [služby API Management](get-started-create-service-instance.md), [připojeného centra událostí](api-management-howto-log-event-hubs.md)a [účtu úložiště](../storage/common/storage-create-storage-account.md) ke spuštění ukázky sami.   

Ukázka je stejně jednoduché konzolové aplikace, která přijímá událostí přicházejících z centra událostí, převede je do `HttpRequestMessage` a `HttpResponseMessage` objektů a předává je do rozhraní API Runscope.

Následující animovaný obrázek zobrazí se žádost odeslaná do rozhraní API v portálu pro vývojáře, konzolová aplikace zobrazuje zpráva se přijal, zpracování a předané a pak požadavek a odpověď zobrazují v okně Inspektor Runscope provoz.

![Ukázka požadavku předávaná Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje ideálním místem pro zachycení provozu HTTP na cestách do a z rozhraní API. Azure Event Hubs je vysoce škálovatelné a úsporné řešení pro zachytávání provozu a jí předáte do systémů sekundární zpracování pro protokolování, sledování a další sofistikované analýzy. Probíhá připojování k monitorování systémů, jako je snadné – stačí pár desítek řádků kódu Runscope provoz třetích stran.

## <a name="next-steps"></a>Další postup
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Příjem zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a služby Event Hubs
  * [Jak protokolování událostí ve službě Azure Event Hubs ve službě Azure API Management](api-management-howto-log-event-hubs.md)
  * [Odkaz na entitu protokolovací nástroj](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Referenční příručce o zásadách protokolu do centra událostí](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
