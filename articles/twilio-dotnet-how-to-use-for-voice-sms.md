---
title: Jak používat Twilio pro hlasové a SMS (.NET) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak telefonovat a odesílat SMS zprávy pomocí služby Twilio API v Azure. Ukázky kódu napsané v rozhraní .NET.
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
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69876810"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak používat Twilio pro hlasové a SMS funkce z Azure
Tato příručka ukazuje, jak provádět běžné úlohy programování se službou Twilio API v Azure. Zahrnuté scénáře zahrnují telefonování a odeslání zprávy SMS (Short Message Service). Další informace o Twilio a používání hlasu a SMS ve vašich aplikacích naleznete v části [Další kroky.](#NextSteps)

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Co je Twilio?
Twilio napájí budoucnost obchodní komunikace, což vývojářům umožňuje vkládat hlas, VoIP a zasílání zpráv do aplikací. Virtualizují veškerou infrastrukturu potřebnou v globálním prostředí založeném na cloudu a vystavují ji prostřednictvím platformy Twilio communications API. Aplikace se snadno vytvářejí a lze škálovat. Využijte flexibilitu s průběžnými platbami a využijte spolehlivost cloudu.

**Twilio Voice** umožňuje vašim aplikacím uskutečňovat a přijímat telefonní hovory. **Twilio SMS** umožňuje aplikacím odesílat a přijímat SMS zprávy. **Twilio Client** umožňuje volat VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Ceny twilio a speciální nabídky
Zákazníci Azure obdrží [speciální nabídku](https://www.twilio.com/azure): bezplatný kredit Twilio kredit u twilio kreditu zdarma při upgradu účtu Twilio. Tento kredit Twilio lze použít na jakékoli použití Twilio (kredit $ 10 ekvivalentní odesílání až 1 000 SMS zpráv nebo přijímání až 1000 příchozích minut hlasu v závislosti na umístění vašeho telefonního čísla a zprávy nebo cíle hovoru). Využijte tento kredit Twilio a začít na [twilio.com/azure](https://twilio.com/azure).

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a svůj účet můžete kdykoli uzavřít. Více informací najdete na [Twilio Pricing](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Koncepty
Twilio API je RESTful API, které poskytuje hlasové a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici ve více jazycích. Seznam naleznete v tématu [Knihovny rozhraní API Twilio][twilio_libraries].

Klíčovými aspekty rozhraní Twilio API jsou slovesa Twilio a twilio markup language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Slovesa Twilio
Rozhraní API využívá slovesa Twilio; Například ** &lt;Say&gt; ** sloveso pokyn Twilio slyšitelně doručit zprávu na volání.

Následuje seznam sloves Twilio.  Další informace o dalších slovech a možnostech naleznete v [dokumentaci k twilio markup language](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Připojí volajícího k jinému telefonu.
* `<Gather>`: Shromažďuje číselné číslice zadané na klávesnici telefonu.
* `<Hangup>`: Ukončí hovor.
* `<Play>`: Přehraje zvukový soubor.
* `<Pause>`: Tiše čeká na zadaný počet sekund.
* `<Record>`: Zaznamená hlas volajícího a vrátí adresu URL souboru, který záznam obsahuje.
* `<Redirect>`: Přenese řízení hovoru nebo SMS na TwiML na jinou adresu URL.
* `<Reject>`: Odmítne příchozí hovor na vaše číslo Twilio bez fakturace
* `<Say>`: Převede text na řeč, která je provedena na volání.
* `<Sms>`: Odešle SMS zprávu.

### <a name="twiml"></a>Twiml
TwiML je sada pokynů založených na XML založených na slovesa Twilio, které informují Twilio o tom, jak zpracovat volání nebo SMS.

Jako příklad následující TwiML převede text **Hello World** na řeč.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Když vaše aplikace volá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, která vrací odpověď TwiML. Pro účely vývoje můžete pomocí adres URL poskytovaných twilio poskytnout odpovědi TwiML používané vašimi aplikacemi. Můžete také hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o slovesách Twilio, jejich atributech a TwiML naleznete v [tématu TwiML][twiml]. Další informace o rozhraní TWILIO API naleznete v tématu [Twilio API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni získat účet Twilio, zaregistrujte se na [Try Twilio][try_twilio]. Můžete začít s bezplatným účtem a později svůj účet upgradovat.

Když si zaregistrujete účet Twilio, obdržíte ID účtu a ověřovací token. Oba budou potřebné k volání rozhraní API Twilio. Chcete-li zabránit neoprávněnému přístupu k účtu, zabezpečte ověřovací token. ID vašeho účtu a ověřovací token lze zobrazit na [stránce účtu Twilio][twilio_account]v polích označených **ACCOUNT SID** a **AUTH TOKEN**.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Vytvoření aplikace Azure
Aplikace Azure, která hostuje aplikaci s podporou Twilio, se neliší od jiných aplikací Azure. Přidáte knihovnu Twilio .NET a nakonfigurujete roli tak, aby používala knihovny Twilio .NET.
Informace o vytvoření počátečního projektu Azure najdete v [tématu Vytvoření projektu Azure pomocí Visual Studia][vs_project].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurace aplikace pro použití knihoven Twilio
Twilio poskytuje sadu knihoven .NET pomocníků, které zabalit různé aspekty Twilio poskytnout jednoduché a snadné způsoby interakce s Twilio REST API a Twilio klienta pro generování TwiML odpovědi.

Twilio poskytuje pět knihoven pro vývojáře .NET:

| Knihovna | Popis |
| --- | --- |
| Twilio.API | Základní knihovna Twilio, která zabalí rozhraní Twilio REST API v přátelské knihovně .NET. Tato knihovna je k dispozici pro rozhraní .NET, Silverlight a Windows Phone 7. |
| Twilio.TwiML | Poskytuje popisný způsob generování značek TwiML .NET. |
| Twilio.MVC | Pro vývojáře, kteří používají ASP.NET MVC, tato knihovna obsahuje atribut TwilioController, TwiML ActionResult a request validation. |
| Twilio.WebMatrix | Pro vývojáře, kteří používají bezplatný vývojový nástroj WebMatrix společnosti Microsoft, obsahuje tato knihovna pomocníky syntaxe Razor pro různé akce Twilio. |
| Twilio.Client.Schopnost | Obsahuje generátor tokenů schopností pro použití s sadou JavaScript SDK klienta Twilio. |

> [!Important]
> Všechny knihovny vyžadují rozhraní .NET 3.5, Silverlight 4 nebo Windows Phone 7 nebo novější.

Ukázky uvedené v této příručce používají knihovnu Twilio.API.

Knihovny lze [nainstalovat pomocí rozšíření Správce balíčků NuGet,](https://www.twilio.com/docs/csharp/install) které je k dispozici pro Visual Studio 2010 až do roku 2015.  Zdrojový kód je hostován na [GitHubu][twilio_github_repo], který obsahuje Wiki, která obsahuje kompletní dokumentaci pro používání knihoven.

Ve výchozím nastavení aplikace Microsoft Visual Studio 2010 nainstaluje verzi 1.2 aplikace NuGet. Instalace knihoven Twilio vyžaduje verzi 1.6 NuGet nebo vyšší. Informace o instalaci nebo aktualizaci [https://nuget.org/][nuget]nugetu naleznete v tématu .

> [!NOTE]
> Chcete-li nainstalovat nejnovější verzi NuGet, musíte nejprve odinstalovat načtenou verzi pomocí Správce rozšíření sady Visual Studio. Chcete-li tak učinit, je nutné spustit Visual Studio jako správce. V opačném případě je tlačítko Odinstalovat zakázáno.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Přidání knihoven Twilio do projektu sady Visual Studio:
1. Otevřete řešení v sadě Visual Studio.
2. Klepněte pravým tlačítkem myši na **položku Odkazy**.
3. Klikněte na **Spravovat balíčky NuGet...**
4. Klepněte na **tlačítko Online**.
5. Do pole hledat online zadejte *příkaz twilio*.
6. Klikněte na **nainstalovat** na balíček Twilio.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Postup: Volání odchozího volání
Následující ukazuje, jak provést odchozí volání pomocí **CallResource** třídy. Tento kód také používá web poskytovaný twilio vrátit twilio markup jazyk (TwiML) odpověď. Nahraďte své hodnoty pro **do** a **z** telefonních čísel a ujistěte se, že jste před spuštěním kódu ověřili telefonní číslo **od** svého účtu Twilio.

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

Další informace o parametrech předaných metodě **CallResource.Create** naleznete v tématu [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak již bylo zmíněno, tento kód používá web poskytnutý Twilio k vrácení odpovědi TwiML. Místo toho můžete použít vlastní web k poskytnutí odpovědi TwiML. Další informace naleznete v [tématu How to: Provide TwiML responses from your own website](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Postup: Odeslání SMS zprávy
Následující snímek obrazovky ukazuje, jak odeslat zprávu SMS pomocí **MessageResource** třídy. Číslo **od** je poskytováno Twilio pro zkušební účty pro odesílání SMS zpráv. Před **to** spuštěním kódu je nutné ověřit číslo toto číslo pro váš účet Twilio.

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

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Postup: Poskytněte odpovědi TwiML z vašich vlastních webových stránek
Když vaše aplikace zahájí volání rozhraní API Twilio – například prostřednictvím **callresource.create** metody - Twilio odešle požadavek na adresu URL, která se očekává, že vrátí odpověď TwiML. Příklad v [návodu: Provedení odchozího volání](#howto_make_call) používá adresu URL [https://twimlets.com/message][twimlet_message_url] poskytovanou twilio k vrácení odpovědi.

> [!NOTE]
> Zatímco TwiML je určen pro webové služby, můžete zobrazit TwiML ve vašem prohlížeči. Klepnutím například [https://twimlets.com/message][twimlet_message_url] zobrazíte `<Response>` prázdný prvek. jako [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) další příklad, klikněte `<Response>` pro &lt;zobrazení&gt; prvku, který obsahuje Say prvek.
>

Místo spoléhání se na adresu URL poskytovanou twilio můžete vytvořit vlastní web URL, který vrací odpovědi HTTP. Web můžete vytvořit v libovolném jazyce, který vrací odpovědi HTTP. Toto téma předpokládá, že budete hostitelem adresy URL z obecné obslužné rutiny ASP.NET.

Následující ASP.NET Handler řemesla TwiML odpověď, která říká **Hello World** na volání.

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
    
Jak můžete vidět z výše uvedeného příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio.TwiML obsahuje třídy, které vám vygenerují TwiML. Následující příklad vytváří ekvivalentní odpověď, jak je uvedeno výše, ale používá **VoiceResponse** třídy.

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

Další informace o twiml [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)naleznete v tématu .

Po nastavení způsobu poskytování odpovědí TwiML můžete předat tuto adresu URL metodě **CallResource.Create.** Například pokud máte webovou aplikaci s názvem MyTwiML nasazenou do cloudové služby Azure a název vaší ASP.NET Handler je mytwiml.ashx, může být adresa URL předána **CallResource.Create,** jak je znázorněno v následující ukázce kódu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Další informace o používání Twilio v Azure s ASP.NET najdete [v tématu Jak provést telefonní hovor pomocí Twilio ve webové roli v Azure][howto_phonecall_dotnet].

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
