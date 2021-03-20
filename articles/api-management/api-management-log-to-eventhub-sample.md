---
title: Monitorování rozhraní API s využitím Azure API Management, Event Hubs a Moesif
titleSuffix: Azure API Management
description: Ukázková aplikace, která demonstruje zásady pro přihlášení k síti pomocí připojení API Management Azure, Azure Event Hubs a Moesif pro protokolování a sledování HTTP
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
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88205510"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Monitorování rozhraní API pomocí API Management Azure, Event Hubs a Moesif
[Služba API Management](api-management-key-concepts.md) poskytuje mnoho funkcí, které zvyšují zpracování požadavků HTTP odeslaných na vaše rozhraní HTTP API. Existence požadavků a odpovědí je však přechodný. Požadavek se provede a přetéká prostřednictvím služby API Management do rozhraní back-end API. Vaše rozhraní API zpracovává požadavek a odezvu zpět do příjemce rozhraní API. Služba API Management udržuje několik důležitých statistik o rozhraních API pro zobrazení na řídicím panelu Azure Portal, ale kromě toho se tyto podrobnosti odešlou.

Pomocí zásad přihlášení k protokolu pro protokol eventhub ve službě API Management můžete odeslat jakékoli podrobnosti z žádosti a odpovědi do [centra událostí Azure](../event-hubs/event-hubs-about.md). Existuje řada důvodů, proč můžete chtít generovat události ze zpráv HTTP odesílaných do vašich rozhraní API. Mezi příklady patří auditující záznam aktualizací, analýza využití, upozorňování na výjimky a integrace třetích stran.

Tento článek ukazuje, jak zachytit celou žádost HTTP a zprávu s odpovědí, odeslat ji do centra událostí a pak tuto zprávu předat službě třetí strany, která poskytuje služby protokolování a monitorování HTTP.

## <a name="why-send-from-api-management-service"></a>Proč posílat z API Management služby?
Je možné napsat middleware HTTP, který se může připojit k rozhraní HTTP API a zachytit požadavky a odpovědi HTTP a podávat je do systémů protokolování a monitorování. Nevýhodou tohoto přístupu je, že middleware HTTP musí být integrovaná do rozhraní API back-endu a musí odpovídat platformě rozhraní API. Pokud existuje více rozhraní API, pak každý z nich musí nasadit middleware. K dispozici jsou často důvody, proč nelze aktualizovat rozhraní API back-endu.

Použití služby Azure API Management k integraci s infrastrukturou protokolování poskytuje centralizované řešení a nezávislé na platformě. Je také škálovatelná, protože se jedná o možnosti [geografické replikace](api-management-howto-deploy-multi-region.md) v rámci Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Proč posílat do centra událostí Azure?
Je vhodné požádat, proč vytvořit zásadu, která je specifická pro Azure Event Hubs? Je možné, že je k dispozici celá řada různých míst, kde mohu chtít protokolovat moje žádosti. Proč nestačí odeslat požadavky přímo do konečného cíle?  To je možnost. Při protokolování požadavků ze služby API Management je ale nutné zvážit, jak protokolování zpráv ovlivňuje výkon rozhraní API. Postupné zvýšení zatížení lze zpracovat zvýšením dostupných instancí systémových komponent nebo využitím geografické replikace. Krátké špičky v provozu ale můžou způsobit zpoždění požadavků v případě, že požadavky na protokolování infrastruktury začnou být při zatížení pomalé.

Azure Event Hubs je navržený tak, aby se na příchozí objemy dat nastavila, s kapacitou pro práci s mnohem větším počtem událostí, než je počet požadavků HTTP nejvíce rozhraní API. Centrum událostí slouží jako typ sofistikované vyrovnávací paměti mezi vaší službou API Management a infrastrukturou, která ukládá a zpracovává zprávy. Tím se zajistí, že výkon rozhraní API nebude kvůli infrastruktuře protokolování zhoršený.

Jakmile jsou data předána do centra událostí, je trvale zachována a budou čekat na jejich zpracování na příjemce centra událostí. Centrum událostí nezáleží na tom, jak se zpracovává, stačí jenom stojí o tom, že se zpráva úspěšně doručí.

Event Hubs má možnost streamovat události do více skupin příjemců. To umožňuje zpracovávat události v různých systémech. To umožňuje podporu mnoha scénářů integrace bez nutnosti přidávat prodlevy při zpracování žádosti rozhraní API v rámci služby API Management, protože je třeba vygenerovat jenom jednu událost.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Zásada pro posílání zpráv aplikace/http
Centrum událostí přijímá data události jako jednoduchý řetězec. Obsah tohoto řetězce je až na vás. Aby bylo možné zabalit požadavek HTTP a poslat ho do Event Hubs, musíme řetězec naformátovat pomocí informací o požadavku nebo odpovědi. V takových situacích, pokud existuje existující formát, který můžeme znovu použít, nemusí být nutné psát náš kód pro analýzu. Zpočátku jsem považoval za použití [Har](http://www.softwareishard.com/blog/har-12-spec/) pro posílání požadavků a odpovědí HTTP. Tento formát je však optimalizován pro ukládání sekvence požadavků HTTP ve formátu založeném na formátu JSON. Obsahovala řadu povinných prvků, které přidávají zbytečné složitosti pro scénář předávání zprávy HTTP přes kabel.

Alternativní možností bylo použít `application/http` typ média, jak je popsáno v tématu Specifikace http [RFC 7230](https://tools.ietf.org/html/rfc7230). Tento typ média používá přesný formát, který se používá ke skutečnému posílání zpráv HTTP prostřednictvím sítě, ale celá zpráva může být vložena do těla jiné žádosti HTTP. V našem případě budeme k odeslání do Event Hubs použít text jako naši zprávu. Pohodlně existuje analyzátor, který existuje v [Microsoft ASP.NET klientské knihovny webového rozhraní API 2,2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , které mohou analyzovat tento formát a převést jej na nativní `HttpRequestMessage` a `HttpResponseMessage` objekty.

Abychom mohli vytvořit tuto zprávu, musíme využít výhod [výrazů zásad](./api-management-policy-expressions.md) založených na jazyce C# v Azure API Management. Tady je zásada, která odešle zprávu požadavku HTTP do Azure Event Hubs.

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

### <a name="policy-declaration"></a>Deklarace zásad
Existuje několik konkrétních věcí, které se týkají tohoto výrazu zásad. Zásada přihlášení k protokolu pro protokol eventhub má atribut nazvaný protokolovací – ID, který odkazuje na název protokolovacího nástroje, který byl vytvořen v rámci služby API Management. Podrobnosti o tom, jak nastavit protokolovací nástroj centra událostí ve službě API Management, najdete v dokumentu [Jak protokolovat události do azure Event Hubs v azure API Management](api-management-howto-log-event-hubs.md). Druhý atribut je volitelný parametr, který instruuje Event Hubs, do kterého oddílu se uloží zpráva. Event Hubs používá k zajištění škálovatelnosti oddíly a vyžaduje minimálně dvě. Seřazené doručení zpráv je zaručeno pouze v rámci oddílu. Pokud neudělíme centru událostí, ve kterém oddíl umístit zprávu, použije k distribuci zatížení algoritmus kruhového dotazování. Nicméně to může způsobit, že některé z našich zpráv budou zpracovávány mimo pořadí.

### <a name="partitions"></a>Oddíly
Aby bylo zajištěno doručení našich zpráv spotřebitelům v daném pořadí a využití funkcí distribuce zatížení oddílů, jsem se rozhodli odeslat zprávy požadavku HTTP do jednoho oddílu a zprávy s odpovědí HTTP do druhého oddílu. Tím se zajistí rovnoměrné rozložení zatížení a můžeme zaručit, že všechny požadavky budou využity v daném pořadí a všechny odpovědi budou využity v daném pořadí. Je možné, aby odpověď byla spotřebována před odpovídajícím požadavkem, ale stejně jako to není problém, protože máme jiný mechanismus pro korelaci požadavků na odpovědi a víme, že požadavky jsou vždy před odpověďmi.

### <a name="http-payloads"></a>Datové části HTTP
Po vytvoření se `requestLine` zkontroluje, jestli by text požadavku měl být oříznutý. Text žádosti se zkrátí jenom na 1024. To se dá zvýšit, ale jednotlivé zprávy centra událostí jsou omezené na 256 KB, takže je možné, že některé tělo zprávy HTTP se nevejdou do jedné zprávy. Při protokolování a analýze významného množství informací se může odvozovat jenom z řádku požadavku HTTP a z hlavičky. Mnoho požadavků rozhraní API také vrací jenom malé body, takže ztráta hodnoty informací zkrácením velkých subjektů je poměrně minimální v porovnání s snížením nákladů na přenos, zpracování a ukládání, aby se zachoval obsah těla. Jednu poslední poznámku ke zpracování těla je, že musíme předat `true` `As<string>()` metodě, protože čteme obsah těla, ale měl by také jít o rozhraní back-end, aby bylo možné tělo přečíst. Předáním hodnoty true této metodě způsobíme, že tělo bude uloženo do vyrovnávací paměti tak, aby bylo možné ho přečíst podruhé. To je důležité vědět, pokud máte rozhraní API, které odesílá velké soubory nebo používá dlouhé cyklické dotazování. V těchto případech by bylo vhodné se vyhnout čtení těla.

### <a name="http-headers"></a>Hlavičky protokolu HTTP
Hlavičky HTTP se dají přenést do formátu zprávy ve formátu jednoduché dvojice klíč/hodnota. Rozhodli jsme se vykládat určitá pole citlivých na zabezpečení, aby nedocházelo k zbytečnému úniku informací o přihlašovacích údajích. Je pravděpodobné, že klíče rozhraní API a další přihlašovací údaje by se použily pro účely analýzy. Pokud chceme provést analýzu u uživatele a konkrétního produktu, který používají, můžeme to z `context` objektu získat a přidat ho do zprávy.

### <a name="message-metadata"></a>Metadata zprávy
Při sestavování kompletní zprávy pro odeslání do centra událostí není první řádek ve skutečnosti součástí `application/http` zprávy. První řádek je další metadata, která se skládají z toho, zda se jedná o zprávu žádosti nebo odpovědi a ID zprávy, které se používá ke sladění požadavků na odpovědi. ID zprávy se vytvoří pomocí jiné zásady, která vypadá takto:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

Mohli jsme vytvořit zprávu požadavku uloženou v proměnné, dokud odpověď nevrátíte, a pak žádost a odpověď odeslali jako jednu zprávu. Odesláním žádosti a odpovědi nezávisle na sobě a použitím ID zprávy si ale ve velikosti zprávy získáte trochu větší flexibilitu, což umožňuje využít více oddílů při zachování pořadí zpráv a v našem řídicím panelu protokolování se požadavek zobrazí dřív. Může se taky stát, že se do centra událostí nikdy nepošle platná odpověď, možná z důvodu závažné chyby požadavku ve službě API Management, ale pořád máme záznam o žádosti.

Zásada pro odeslání zprávy HTTP odpovědi vypadá podobně jako požadavek, takže kompletní konfigurace zásad vypadá takto:

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

`set-variable`Zásada vytvoří hodnotu, která je přístupná ze `log-to-eventhub` zásad v `<inbound>` části i v `<outbound>` části.

## <a name="receiving-events-from-event-hubs"></a>Příjem událostí z Event Hubs
Události z centra událostí Azure se přijímají pomocí [protokolu AMQP](https://www.amqp.org/). Tým Microsoft Service Bus provedl klientské knihovny, aby bylo možné snadněji spotřebovávat náročné události. Existují dva různé přístupy, jeden je *přímý spotřebitel* a druhý používá `EventProcessorHost` třídu. Příklady těchto dvou přístupů najdete v [Průvodci programováním v Event Hubs](../event-hubs/event-hubs-programming-guide.md). Krátká verze rozdílů je, `Direct Consumer` poskytuje úplnou kontrolu a `EventProcessorHost` provede některé pracovní postupy, které vám pomohou při zpracování těchto událostí.

### <a name="eventprocessorhost"></a>EventProcessorHost
V této ukázce používáme `EventProcessorHost` pro jednoduchost, ale to ale nemusí být pro tento konkrétní scénář nejlepší volbou. `EventProcessorHost` nemusíte mít jistotu, že se nemusíte starat o problémy s vlákny v rámci konkrétní třídy procesoru událostí. V našem scénáři ale jednoduše převádíme zprávu na jiný formát a projdeme ji do jiné služby pomocí asynchronní metody. Není potřeba aktualizovat sdílený stav, a proto nehrozí žádné riziko problémů s vlákny. Ve většině scénářů `EventProcessorHost` je pravděpodobně nejlepší volbou a ta je určitě jednodušší.

### <a name="ieventprocessor"></a>IEventProcessor
Centrální koncept při použití `EventProcessorHost` je vytvořit implementaci `IEventProcessor` rozhraní, které obsahuje metodu `ProcessEventAsync` . Podstata této metody je znázorněna zde:

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

Do metody se předává seznam objektů EventData a my se na tento seznam opakuje. Bajty každé metody jsou analyzovány do objektu HttpMessage a tento objekt je předán instanci IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
`HttpMessage`Instance obsahuje tři části dat:

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

`HttpMessage`Instance obsahuje `MessageId` identifikátor GUID, který nám umožňuje připojit požadavek HTTP k odpovídající odpovědi HTTP a logickou hodnotu, která identifikuje, zda objekt obsahuje instanci třídy zprávy HttpRequestMessage a HttpResponseMessage. Pomocí integrovaných tříd HTTP z `System.Net.Http` , jsem dokázal využít `application/http` analýzu kódu, který je součástí `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage`Instance se pak přepošle do implementace `IHttpMessageProcessor` , což je rozhraní, které jsem vytvořil, aby se odložilo přijímání a interpretace události z centra událostí Azure a jejich skutečné zpracování.

## <a name="forwarding-the-http-message"></a>Předávání zprávy HTTP
V této ukázce jsme se rozhodli, že byste do služby [MOESIF API Analytics](https://www.moesif.com)mohli odeslat požadavek HTTP. Moesif je cloudová služba, která se specializuje na službu HTTP Analytics a ladění. Mají bezplatnou úroveň, takže se snadno pokusíte a můžeme zobrazit požadavky HTTP v toku v reálném čase prostřednictvím naší služby API Management.

`IHttpMessageProcessor`Implementace vypadá takto.

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

Využívá `MoesifHttpMessageProcessor` [knihovnu rozhraní C# API pro Moesif](https://www.moesif.com/docs/api?csharp#events) , která usnadňuje vkládání dat událostí http do služby. Aby bylo možné odesílat data HTTP do rozhraní API Moesif collector, potřebujete účet a ID aplikace. ID aplikace Moesif získáte vytvořením účtu na [webu Moesif](https://www.moesif.com) a následným přechodem k nastavení aplikace v _pravém horním_ rohu  ->  .

## <a name="complete-sample"></a>Kompletní ukázka
[Zdrojový kód](https://github.com/dgilling/ApimEventProcessor) a testy pro ukázku jsou na GitHubu. Potřebujete [službu API Management](get-started-create-service-instance.md), [připojené centrum událostí](api-management-howto-log-event-hubs.md)a [účet úložiště](../storage/common/storage-account-create.md) , abyste mohli ukázku spustit sami.   

Ukázka je pouze jednoduchá Konzolová aplikace, která naslouchá událostem přicházejících z centra událostí, převádí je do Moesif `EventRequestModel` a `EventResponseModel` objektů a pak je předávají do rozhraní API kolekce Moesif.

Na následujícím animovaném obrázku vidíte na portálu pro vývojáře požadavek na rozhraní API, konzolová aplikace zobrazuje zprávu, která se přijímá, zpracovává a předává, a pak požadavek a odpověď zobrazený v datovém proudu událostí.

![Ukázka přesměrovaného požadavku na Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje ideální místo pro zachycení provozu HTTP na cestách a z vašich rozhraní API. Azure Event Hubs je vysoce škálovatelné řešení s nízkými náklady pro zachytávání provozu a jejich krmení do sekundárních zpracovatelských systémů pro protokolování, monitorování a další propracované analýzy. Připojení k systémům monitorování provozu třetích stran, jako je Moesif, je jednoduché jako několik desítek řádků kódu.

## <a name="next-steps"></a>Další kroky
* Další informace o Azure Event Hubs
  * [Začínáme s Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Přijímání zpráv pomocí třídy EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Průvodce programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Další informace o integraci API Management a Event Hubs
  * [Jak protokolovat události do Azure Event Hubs v Azure API Management](api-management-howto-log-event-hubs.md)
  * [Reference k entitě protokolovacího nástroje](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [odkaz na zásady přihlášení k protokolu eventhub](./api-management-advanced-policies.md#log-to-eventhub)
