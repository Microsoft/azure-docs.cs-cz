---
title: Jak používat Twilio pro hlas a SMS (.NET) | Microsoft Docs
description: Naučte se, jak uskutečnit telefonní hovor a poslat zprávu SMS pomocí služby Twilio API v Azure. Ukázky kódu napsané v .NET
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003751"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak používat Twilio pro hlasové funkce a SMS z Azure
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí služby Twilio API v Azure. Mezi zahrnuté scénáře patří telefonní hovor a odeslání zprávy o krátké službě zprávy (SMS). Další informace o Twilio a použití hlasu a SMS v aplikacích najdete v části [Další kroky](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio vypíná budoucí obchodní komunikaci a umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v cloudovém globálním prostředí a zpřístupňuje je prostřednictvím platformy rozhraní API pro komunikaci Twilio. Aplikace jsou jednoduché pro sestavování a škálovatelnost. Využijte flexibilitu při průběžných platbách a využijte výhod spolehlivosti cloudu.

**Twilio Voice** umožňuje vašim aplikacím vytvářet a přijímat telefonní hovory. **TWILIO SMS** umožňuje aplikacím odesílat a přijímat zprávy SMS. **Klient Twilio** umožňuje volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníci Azure obdrží [zvláštní nabídku](https://www.twilio.com/azure): bezplatný $10 kredit Twilio při upgradu účtu Twilio. Tento Twilio kredit se dá použít pro jakékoli použití Twilio ($10 kreditový ekvivalent pro odeslání až 1 000 zpráv SMS nebo přijetí až 1000 zpráv o telefonním čísle v závislosti na umístění telefonního čísla a zprávy nebo cíli volání). Uplatněte tento Twilio kredit a začněte na [Twilio.com/Azure](https://twilio.com/azure).

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a účet můžete kdykoli zavřít. Další podrobnosti najdete v [Twilio cenách](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Rozhraní Twilio API je rozhraní RESTful API, které poskytuje funkce hlasu a SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích. seznam najdete v tématu [knihovny rozhraní API pro Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou Twilio příkazy a Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Operace Twilio
Rozhraní API využívá operace Twilio; Například příkaz **&lt; vyslovit &gt;** instruuje Twilio, aby audibly doručení zprávy na volání.

Následuje seznam operací Twilio.  Přečtěte si o dalších příkazech a funkcích prostřednictvím [dokumentace jazyka Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Připojí volajícího k jinému telefonu.
* `<Gather>`: Shromažďuje číselné číslice zadané na klávesnici na telefonu.
* `<Hangup>`: Ukončí volání.
* `<Play>`: Přehraje zvukový soubor.
* `<Pause>`: Netiché čekání po zadaný počet sekund.
* `<Record>`: Zaznamenává hlas volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* `<Redirect>`: Přenáší řízení volání nebo SMS na TwiML na jinou adresu URL.
* `<Reject>`: Odmítne příchozí volání na číslo Twilio bez fakturace.
* `<Say>`: Převede text na řeč, který je proveden na volání.
* `<Sms>`: Odešle zprávu SMS.

### <a name="twiml"></a>TwiML
TwiML je sada instrukcí založených na XML, která je založená na příkazech Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Například následující TwiML převede text **Hello World** na řeč.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Když vaše aplikace volá rozhraní Twilio API, je jedním z parametrů rozhraní API adresa URL, která vrací TwiML odpověď. Pro účely vývoje můžete použít adresy URL poskytované Twilio a poskytnout tak odpovědi na TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL pro vytváření odpovědí TwiML a další možností je použít objekt **TwiMLResponse** .

Další informace o příkazech Twilio, jejich atributech a TwiML naleznete v tématu [TwiML][twiml]. Další informace o rozhraní Twilio API najdete v tématu [rozhraní Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se do [Twilio try][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio obdržíte ID účtu a ověřovací token. Pro volání rozhraní API Twilio budou potřeba obojí. Abyste zabránili neoprávněnému přístupu k účtu, udržujte svůj ověřovací token zabezpečený. ID účtu a ověřovací token se mohou zobrazit na [stránce účet Twilio][twilio_account]v polích s popiskem **SID účtu** a **ověřovacím tokenem** v uvedeném pořadí.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Vytvoření aplikace Azure
Aplikace Azure, která je hostitelem aplikace s podporou Twilio, se neliší od žádné jiné aplikace Azure. Přidáte knihovnu rozhraní .NET Twilio a nakonfigurujete roli pro použití knihoven .NET Twilio.
Informace o vytvoření počátečního projektu Azure najdete v tématu [Vytvoření projektu Azure pomocí sady Visual Studio][vs_project].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Twilio poskytuje sadu pomocných knihoven .NET, které zabalí různé aspekty Twilio, aby poskytovaly jednoduché a jednoduché způsoby interakce s Twilio REST API a klientem Twilio pro generování odpovědí na TwiML.

Twilio poskytuje pro vývojáře v rozhraní .NET pět knihoven:

| Knihovna | Description |
| --- | --- |
| Twilio. API | Základní knihovna Twilio, která zabalí Twilio REST API v uživatelsky přívětivé knihovně .NET. Tato knihovna je k dispozici pro .NET, Silverlight a Windows Phone 7. |
| Twilio. TwiML | Poskytuje uživatelsky přívětivý způsob, jak generovat kód TwiML. |
| Twilio. MVC | Pro vývojáře, kteří používají ASP.NET MVC, tato knihovna obsahuje atribut TwilioController, TwiML ActionResult a žádost o ověření. |
| Twilio. WebMatrix | Pro vývojáře, kteří používají bezplatný nástroj pro vývoj WebMatrix od Microsoftu, tato knihovna obsahuje syntaxe Razor pomocníků pro různé akce Twilio. |
| Twilio. Client. Capability | Obsahuje generátor tokenu schopností pro použití s Twilio Client JavaScript SDK. |

> [!Important]
> Všechny knihovny vyžadují rozhraní .NET 3,5, Silverlight 4 nebo Windows Phone 7 nebo novější.

Ukázky uvedené v této příručce používají knihovnu Twilio. API.

Knihovny lze [nainstalovat pomocí rozšíření Správce balíčků NuGet](https://www.twilio.com/docs/csharp/install) dostupného pro sadu Visual Studio 2010 až 2015.  Zdrojový kód je hostovaný na [GitHubu][twilio_github_repo], který obsahuje wiki, který obsahuje ucelenou dokumentaci pro používání knihoven.

Ve výchozím nastavení Microsoft Visual Studio 2010 nainstaluje verzi 1,2 sady NuGet. Instalace knihoven Twilio vyžaduje verzi 1,6 nástroje NuGet nebo vyšší. Informace o instalaci nebo aktualizaci NuGet najdete v tématu [https://nuget.org/][nuget] .

> [!NOTE]
> Chcete-li nainstalovat nejnovější verzi nástroje NuGet, je nutné nejprve odinstalovat načtenou verzi pomocí Správce rozšíření sady Visual Studio. K tomu je nutné spustit aplikaci Visual Studio jako správce. V opačném případě je tlačítko odinstalovat zakázané.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Přidání knihoven Twilio do projektu aplikace Visual Studio:
1. Otevřete své řešení v prostředí Visual Studio.
2. Klikněte pravým tlačítkem na **odkazy**.
3. Klikněte na **Spravovat balíčky NuGet...**
4. Klikněte na možnost **online**.
5. Do pole Hledat online zadejte *Twilio*.
6. V balíčku Twilio klikněte na **nainstalovat** .

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postupy: provedení odchozího volání
Následující příklad ukazuje, jak provést odchozí volání pomocí třídy **CallResource** . Tento kód také používá Twilio web k vrácení odpovědi TwiML (Twilio Markup Language). Nahraďte své hodnoty telefonními čísly a a **z** nich a ověřte, že před spuštěním kódu ověříte **z** telefonního **čísla pro účet** Twilio.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Další informace o parametrech předaných metodě **CallResource. Create** naleznete v tématu [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Jak bylo zmíněno, tento kód používá Twilio web k vrácení TwiML odpovědi. Místo toho můžete k poskytnutí odpovědi TwiML použít svůj vlastní web. Další informace najdete v tématu [Postup: poskytování TwiML odpovědí z vašeho vlastního webu](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující snímek obrazovky ukazuje, jak odeslat zprávu SMS pomocí třídy **MessageResource**  . Číslo **od** je poskytované Twilio pro zkušební účty k odeslání zpráv SMS. Před spuštěním kódu musí **být pro svůj** účet Twilio ověřené číslo.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postupy: poskytování odpovědí TwiML z vašeho vlastního webu
Když vaše aplikace zahájí volání rozhraní Twilio API – například prostřednictvím metody **CallResource. Create** -Twilio odešle vaši žádost na adresu URL, která by měla vrátit odpověď TwiML. Příklad v tématu [Postup: provedení odchozího volání](#howto_make_call) používá adresu URL poskytnutou Twilio [https://twimlets.com/message][twimlet_message_url] k vrácení odpovědi.

> [!NOTE]
> I když je TwiML navržený pro použití webovými službami, můžete si Zobrazit TwiML v prohlížeči. Například kliknutím [https://twimlets.com/message][twimlet_message_url] zobrazíte prázdný `<Response>` prvek. jako jiný příklad [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) můžete kliknutím zobrazit `<Response>` prvek, který obsahuje &lt; &gt; element řekněme.
>

Místo toho, abyste museli spoléhat na adresu URL zadanou v Twilio, můžete vytvořit vlastní adresu URL, která bude vracet odpovědi HTTP. Web můžete vytvořit v jakémkoli jazyce, který vrací odpovědi HTTP. Toto téma předpokládá, že budete hostovat adresu URL z obecné obslužné rutiny ASP.NET.

Následující obslužná rutina ASP.NET plavidlo TwiML odpověď, která říká **Hello World** volání.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Jak vidíte z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio. TwiML obsahuje třídy, které budou generovat TwiML pro vás. Následující příklad vytvoří ekvivalentní odpověď, jak je uvedeno výše, ale používá třídu **VoiceResponse** .

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) .

Jakmile nastavíte způsob, jak poskytnout odpovědi TwiML, můžete tuto adresu URL předat metodě **CallResource. Create** . Například pokud máte webovou aplikaci s názvem MyTwiML nasazenou do cloudové služby Azure a název vaší obslužné rutiny ASP.NET je MyTwiML. ashx, adresa URL může být předána **CallResource. Create** , jak je znázorněno v následující ukázce kódu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Další informace o používání Twilio v Azure s ASP.NET najdete v tématu [jak uskutečnit telefonní hovor pomocí Twilio ve webové roli v Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
