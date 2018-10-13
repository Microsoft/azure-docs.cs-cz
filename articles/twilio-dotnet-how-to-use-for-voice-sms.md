---
title: Postup použití Twilia pro hlasové hovory a SMS (.NET) | Dokumentace Microsoftu
description: Zjistěte, jak volat a odeslat zprávu SMS s služba Twilio API v Azure. Ukázky kódu napsané v rozhraní .NET.
services: ''
documentationcenter: .net
author: devinrader
manager: twilio
editor: ''
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: cde668fdeda7e484585a457a46fc5c25ce6ea6ae
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310534"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Postup použití Twilia pro hlasové hovory a SMS z Azure
Tato příručka ukazuje, jak k provádění běžných programovacích úloh pomocí služby Twilio API v Azure. Pokryté scénáře zahrnují vytváření telefonních hovorů a posílání zpráv služby krátké zprávy (SMS). Další informace o Twilio a používání hlasové hovory a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) oddílu.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je provozování budoucí komunikaci business, umožňuje vývojářům vkládat hlas, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat veškerá infrastruktura, je potřeba v prostředí založené na cloudu a globální, bude vystavená prostřednictvím platformy Twilio komunikace rozhraní API. Snadné sestavení a škálovatelné jsou aplikace. Využijte flexibilitu s cenami průběžných plateb a těžit z cloudu spolehlivost.

**Twilio pro hlasové hovory** umožňuje vašim aplikacím, ujistěte se, a přijímat telefonní hovory. **Twilio SMS** umožňuje aplikacím odesílat a přijímat zprávy SMS. **Twilio klienta** umožňuje provádět volání VoIP z libovolného telefonu, tabletu nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciální nabídky
Zákazníkům Azure poskytujeme [speciální nabídka](http://www.twilio.com/azure): praktického 10 USD kreditu Twilio při upgradu vašeho účtu Twilio. Tento kredit Twilio můžete použít pro jakékoli využití Twilio (10 USD kreditu ekvivalentní k odesílání až 1 000 zpráv SMS nebo přijímání až 1 000 příchozí hlasové minut v závislosti na umístění cílové vaše telefonní číslo a zpráva nebo volání). Uplatnění tento kredit Twilio a začněte s [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio je služba s průběžnými platbami. Neexistují žádné poplatky za nastavení a můžete kdykoli zavřít svůj účet. Můžete najít další podrobnosti najdete v [ceny Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilia je rozhraní RESTful API, která poskytuje hlasové hovory a SMS funkce pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [knihovny rozhraní API Twilia][twilio_libraries].

Klíčové aspekty Twilio API jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazů například **&lt;Say&gt;** příkaz nastaví Twilio zvukově doručení zprávy na volání.

Následuje seznam příkazů Twilio.  Další informace o ostatních příkazů a funkce prostřednictvím [dokumentaci Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* `<Dial>`: Volající se připojí k jiný telefon.
* `<Gather>`: Shromažďuje číslice zadané na klávesnici telefonu.
* `<Hangup>`: Ukončení volání.
* `<Play>`: Přehraje zvukový soubor.
* `<Pause>`: Bezobslužná čeká na zadaný počet sekund.
* `<Record>`: Zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznam.
* `<Redirect>`: Řízení přenosů volání nebo odeslání SMS TwiML na jinou adresu URL.
* `<Reject>`: Bez fakturace je odmítne příchozí volání na vaše číslo Twilio
* `<Say>`: Převede text na řeč, který je k volání.
* `<Sms>`: Odešle zprávu SMS.

### <a name="twiml"></a>TwiML
TwiML je sada instrukcí založený na formátu XML podle Twilio příkazy, které informují o Twilio, jak zpracovat hovor nebo SMS.

Například následující TwiML by převést text **Hello World** do mluvené řeči.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Pokud vaše aplikace volá rozhraní API pro Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje můžete použít adresy URL zadané Twilio zodpovězení TwiML používají vaše aplikace. Může taky hostovat vlastní adresy URL pro vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazů, jejich atributy a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilia najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Až budete připraveni začít účtu Twilio, zaregistrujte se na [zkuste Twilio][try_twilio]. Můžete začít s bezplatným účtem a později upgradovat svůj účet.

Při registraci účtu Twilio, obdržíte ID účtu a ověřovací token. Obojí je potřeba volat rozhraní API Twilia. Pokud chcete zabránit neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování jsou zobrazené v tokenu [stránka účtu Twilio][twilio_account], v polích s popiskem **SID účtu** a **Ověřovacím TOKENEM**v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace Azure
Aplikaci Azure, který je hostitelem aplikace Twilio povoleno se nijak neliší ze všech aplikací Azure. Přidejte knihovny Twilio .NET a nakonfigurujte roli na knihovny Twilio .NET.
Informace o vytvoření počáteční projekt Azure najdete v tématu [vytvoření projektu Azure pomocí sady Visual Studio][vs_project].

## <a id="configure_app"></a>Konfigurace vaše aplikace pro použití knihovny platformy Twilio
Twilio poskytuje sadu pomocné rutiny knihovny .NET, které obalují různé aspekty Twilio poskytují jednoduché a snadné způsoby, jak pracovat s rozhraním Twilio REST API a Twilio klienta vygenerovat TwiML odpovědi.

Twilio nabízí pět knihovny pro vývojáře na platformě .NET:

| Knihovna | Popis |
| --- | --- |
| Twilio.API | Základní knihovna Twilio, které zabaluje rozhraní REST API Twilia popisný knihovny .NET. Tato knihovna je dostupné pro .NET, Silverlight a Windows Phone 7. |
| Twilio.TwiML | Poskytuje způsob popisný .NET a vygenerovat TwiML značek. |
| Twilio.MVC | Pro vývojáře používající technologie ASP.NET MVC obsahuje tuto knihovnu TwilioController, TwiML ActionResult a atribut žádosti o ověření. |
| Twilio.WebMatrix | Tato knihovna pro vývojáře, kteří používají vývojovém nástroji Microsoftu zdarma WebMatrix, obsahuje pomocné rutiny syntaxe Razor pro různé platformy Twilio akce. |
| Twilio.Client.Capability | Obsahuje funkce generátor tokenů pro použití s Twilio klientské sady JavaScript SDK. |

> [!Important]
> Všechny knihovny vyžadují rozhraní .NET 3.5, Silverlight 4 nebo Windows Phone 7 nebo novější.

Ukázky popsané v této příručce použít knihovnu Twilio.API.

Může být knihoven [nainstalovat pomocí rozšíření Správce balíčků NuGet](http://www.twilio.com/docs/csharp/install) k dispozici pro sadu Visual Studio 2010 až 2015.  Zdrojový kód je hostován aplikací [Githubu][twilio_github_repo], což zahrnuje Wiki, která obsahuje kompletní dokumentace pro používání knihovny.

Ve výchozím nastavení nainstaluje Microsoft Visual Studio 2010 verze 1.2 NuGet. Instalace knihoven Twilio vyžaduje verzi 1.6 NuGet nebo vyšší. Informace o instalaci nebo aktualizaci NuGet naleznete v tématu [ http://nuget.org/ ] [ nuget].

> [!NOTE]
> K instalaci nejnovější verze Nugetu, musíte nejprve odinstalovat načtená verze pomocí Správce rozšíření sady Visual Studio. Uděláte to tak, musíte spustit aplikaci Visual Studio jako správce. V opačném případě je zakázáno na tlačítko odinstalovat.
>
>

### <a id="use_nuget"></a>Přidání Twilio knihovny do projektu sady Visual Studio:
1. Otevřete řešení v sadě Visual Studio.
2. Klikněte pravým tlačítkem na **odkazy**.
3. Klikněte na tlačítko **spravovat balíčky NuGet...**
4. Klikněte na tlačítko **Online**.
5. Zadejte do pole Hledat online *twilio*.
6. Klikněte na tlačítko **nainstalovat** na balíček platformy Twilio.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak provádět volání pomocí odchozí **CallResource** třídy. Tento kód také používá k vrácení odpovědi Twilio Markup Language (TwiML) poskytované Twilio lokality. Dosaďte svoje hodnoty **k** a **z** telefonní čísla a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "http://twimlets.com/message";
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

Další informace o parametrech předaná **CallResource.Create** metodu, najdete v článku [ http://www.twilio.com/docs/api/rest/making-calls ] [ twilio_rest_making_calls].

Jak už bylo zmíněno, tento kód používá poskytované Twilio webu vrátit TwiML odpovědi. Místo toho můžete použít svůj vlastní web k poskytování TwiML odpovědi. Další informace najdete v tématu [jak: Zadejte TwiML odpovědi z vlastní web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání SMS zprávy
Následující snímek obrazovky ukazuje, jak odeslat zprávu SMS pomocí **MessageResource** třídy. **z** poskytuje číslo Twilio pro účty posílat SMS zprávy. **k** číslo musí být ověřený pro váš účet Twilio, před spuštěním kódu.

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

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odezvy z vlastního webu
Pokud aplikace zahájí volání rozhraní API Twilia – například prostřednictvím **CallResource.Create** metoda - Twilio odešle požadavek na adresu URL, která se má vrátit odpověď TwiML. V příkladu v [postupy: volání odchozí](#howto_make_call) používá adresu URL poskytnutou Twilio [ http://twimlets.com/message ] [ twimlet_message_url] vrátit odpověď.

> [!NOTE]
> TwiML je určen k použití webových služeb, ale když zobrazíte TwiML v prohlížeči. Klikněte například na [ http://twimlets.com/message ] [ twimlet_message_url] zobrazíte prázdná `<Response>` element; další příklad, klikněte na tlačítko [ http://twimlets.com/message?Message%5B0%5D=Hello%20World ](http://twimlets.com/message?Message%5B0%5D=Hello%20World) zobrazíte `<Response>` element, který obsahuje &lt;Say&gt; elementu.
>

Aniž byste museli spoléhat na URL poskytnutou Twilio, můžete vytvořit vlastní adresu URL webu, který vrací odpovědi protokolu HTTP. Můžete vytvořit web v jakémkoli jazyce, který vrací odpovědi protokolu HTTP. Toto téma předpokládá, že budete hostovat adresu URL z ASP.NET obecné obslužné rutiny.

Následující obslužná rutina ASP.NET sestavuje TwiML odpovědi s upozorněním **Hello World** při volání.

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
    
Jak je vidět z výše uvedeného příkladu TwiML odpověď je jednoduše dokumentu XML. Knihovna Twilio.TwiML obsahuje třídy, které budou generovat TwiML za vás. Následující příklad vytvoří odpovídající odpověď, jak je uvedeno výše, ale používá **VoiceResponse** třídy.

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

Další informace o TwiML najdete v tématu [ https://www.twilio.com/docs/api/twiml ](https://www.twilio.com/docs/api/twiml).

Jakmile jste nastavili způsob, jak poskytnout TwiML odpovědi, můžete předat tuto adresu URL k **CallResource.Create** metody. Například pokud máte webovou aplikaci s názvem MyTwiML nasadit do cloudové služby Azure a vaší obslužné rutiny ASP.NET jmenuje mytwiml.ashx, adresa URL může být předán **CallResource.Create** jak je znázorněno v následujícím příkladu kódu:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Další informace o používání Twilio v Azure s využitím technologie ASP.NET najdete v tématu [jak telefonování pomocí Twilio ve webové roli v Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
