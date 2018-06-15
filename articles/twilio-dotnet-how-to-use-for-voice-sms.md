---
title: Jak používat Twilio pro hlasový a serveru SMS (.NET) | Microsoft Docs
description: Naučte se telefonní hovor a odeslání zprávy SMS službou Twilio rozhraní API v Azure. Ukázky kódu jsou vytvořené v rozhraní .NET.
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
ms.openlocfilehash: 1442e3af26ae87e645cf207228ed1197b2afdd4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877024"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Jak používat Twilio pro hlasový a možnosti služby SMS z Azure
Tato příručka ukazuje, jak provádět běžné úkoly programování službou Twilio rozhraní API v Azure. Pokryté scénáře zahrnují uskutečnění telefonního hovoru a odesílání zprávy služby krátké zprávy (SMS). Další informace o Twilio a používání hlasového a SMS ve svých aplikacích najdete v tématu [další kroky](#NextSteps) části.

## <a id="WhatIs"></a>Co je Twilio?
Twilio je pohánějící budoucí obchodní komunikaci, umožňuje vývojářům pro vložení hlasové, VoIP a zasílání zpráv do aplikace. Jejich Virtualizovat všechny infrastrukturu potřebnou v prostředí cloudu, globální vystavení prostřednictvím platformy komunikace rozhraní API Twilio. Aplikace jsou jednoduché k sestavení a škálovatelné. Získejte flexibilitu s průběžnými platbami ceny a využívat cloudové spolehlivost.

**Hlasové Twilio** umožňuje aplikacím zkontrolujte a příjem telefonních hovorů. **Twilio SMS** umožňuje vaší aplikace odesílat a přijímat zprávy SMS. **Klient Twilio** umožňuje provádět volání VoIP z jakékoli telefon, tablet nebo prohlížeče a podporuje WebRTC.

## <a id="Pricing"></a>Ceny Twilio a speciálních nabídek
Přijímat Azure zákazníků [speciální nabídka](http://www.twilio.com/azure): bezplatnou 10 Twilio kreditu při upgradu vašeho účtu Twilio. Tato Twilio platební je použít pro všechny Twilio využití (10 platební ekvivalentní až 1 000 zpráv serveru SMS odesílání nebo přijímání až 1 000 příchozí hlasové minut, v závislosti na umístění telefonní číslo a zpráva nebo volání cíl). Uplatnit tento platební Twilio a začít na [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio je služba, průběžnými platbami. Neexistují žádné poplatky nastavení, a kdykoli můžete zavřít svůj účet. Můžete najít další podrobnosti o [Twilio ceny](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Koncepty
Rozhraní API Twilio je rozhraní RESTful API, která poskytuje hlas a funkce serveru SMS pro aplikace. Klientské knihovny jsou k dispozici v několika jazycích; seznam najdete v tématu [Twilio rozhraní API knihovny][twilio_libraries].

Klíčové aspekty rozhraní API Twilio jsou příkazy Twilio a Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Příkazy Twilio
Rozhraní API využívá Twilio příkazy; například  **&lt;indikované&gt;**  příkaz dá pokyn Twilio zpráva zvukově dodržujeme volání.

Následuje seznam operací Twilio.  Další informace o jiné příkazy a možnosti prostřednictvím [Twilio Markup Language dokumentaci](http://www.twilio.com/docs/api/twiml).

* **&lt;Vytočit&gt;**: připojí volající na jiný telefon.
* **&lt;Shromážděte&gt;**: shromažďuje číslice zadané na klávesnici telefonu.
* **&lt;Zavěšení&gt;**: ukončí volání.
* **&lt;Přehrání&gt;**: přehraje zvukový soubor.
* **&lt;Pozastavení&gt;**: bezobslužná čeká na zadaném počtu sekund.
* **&lt;Záznam&gt;**: zaznamenává hlasové volajícího a vrátí adresu URL souboru, který obsahuje záznamu.
* **&lt;Přesměrování&gt;**: předá řízení hovoru nebo SMS TwiML na jinou adresu URL.
* **&lt;Odmítnout&gt;**: odmítne příchozí volání na vaše číslo Twilio bez fakturace můžete
* **&lt;Řekněme&gt;**: Převod převede textu na řeč, že z volání.
* **&lt;SMS&gt;**: odešle zprávu SMS.

### <a id="TwiML"></a>TwiML
TwiML je sada založený na jazyce XML pokyny podle Twilio příkazy, které informují Twilio postup zpracování hovoru nebo SMS.

Jako příklad následující TwiML by převést text **Hello, World** k rozpoznávání řeči.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Pokud aplikace zavolá rozhraní API Twilio, jeden z parametrů rozhraní API je adresa URL, který vrátí odpověď TwiML. Pro účely vývoje URL můžete použít zadaný Twilio zajistit TwiML odpovědi použít v aplikacích. Může taky hostovat vlastní adresy URL k vytvoření odpovědi TwiML a další možností je použít **TwiMLResponse** objektu.

Další informace o Twilio příkazy, jejich atributů a TwiML najdete v tématu [TwiML][twiml]. Další informace o rozhraní API Twilio najdete v tématu [Twilio API][twilio_api].

## <a id="CreateAccount"></a>Vytvoření účtu Twilio
Když budete chtít získat účet Twilio, zaregistrujte si v [zkuste Twilio][try_twilio]. Můžete začít s bezplatný účet a upgradujte si účet později.

Když se zaregistrujete k účtu Twilio, obdržíte ID účtu a ověřovací token. Jak bude potřeba k volání rozhraní API Twilio. Aby se zabránilo neoprávněnému přístupu ke svému účtu, zabezpečit ověřovací token. ID účtu a ověřování tokenu je možné zobrazit na [stránku účtu Twilio][twilio_account], v polích s názvem bez přípony **SID účtu** a **ověřování TOKENU**, v uvedeném pořadí.

## <a id="create_app"></a>Vytvoření aplikace Azure
Aplikaci Azure, který je hostitelem aplikace Twilio povoleno se neliší od všech aplikací Azure. Přidání knihovny Twilio .NET a konfigurovat role, kterou chcete použít na knihovny Twilio .NET.
Informace o vytváření počáteční projektu Azure najdete v tématu [vytvoření projektu Azure pomocí sady Visual Studio][vs_project].

## <a id="configure_app"></a>Nakonfigurujte si aplikace pro používání knihovny Twilio
Twilio poskytuje sadu pomocné knihovny .NET, které balí různé aspekty Twilio zajistit způsoby jednoduchý a snadno pracovat s Twilio REST API a Twilio klienta vygenerovat TwiML odpovědi.

Twilio obsahuje pět knihoven pro vývojáře .NET:
Knihovna|Popis
---|---
Twilio.API|Základní knihovna Twilio, které zabaluje rozhraní REST API Twilio v popisný knihovny .NET. Tato knihovna je k dispozici pro rozhraní .NET, Silverlight a Windows Phone 7.
Twilio.TwiML|Poskytuje rozhraní .NET popisný způsob generování kódu TwiML.
Twilio.MVC|Pro vývojáře, kteří používají rozhraní ASP.NET MVC zahrnuje tato knihovna TwilioController, TwiML ActionResult a atribut ověření žádosti.
Twilio.WebMatrix|Tato knihovna pro vývojáře, kteří používají nástroj pro vývoj volné WebMatrix společnosti Microsoft, obsahuje Pomocníci syntaxe Razor pro různé akce Twilio.
Twilio.Client.Capability|Obsahuje tokenů generátor schopností pro použití s Twilio klienta JavaScript SDK.

Upozorňujeme, že všechny knihovny vyžaduje rozhraní .NET 3.5, Silverlight 4 nebo Windows Phone 7 nebo novější.

Ukázky uvedené v tomto průvodci v knihovně Twilio.API.

Může být v knihovnách [nainstalovat pomocí rozšíření Správce balíčků NuGet](http://www.twilio.com/docs/csharp/install) k dispozici pro Visual Studio 2010 až 2015.  Zdrojový kód je hostované na [Githubu][twilio_github_repo], což zahrnuje Wiki, který obsahuje kompletní dokumentaci pro používání knihovny.

Ve výchozím nastavení nainstaluje Microsoft Visual Studio 2010 verze 1.2 NuGet. Instalace knihovny Twilio vyžaduje verzi 1.6 NuGet nebo vyšší. Informace o instalaci nebo aktualizaci NuGet najdete v tématu [http://nuget.org/][nuget].

> [!NOTE]
> K instalaci nejnovější verze balíčku NuGet, musíte nejprve odinstalovat načíst verzi pomocí Správce rozšíření Visual Studio. To pokud chcete udělat, musíte Visual Studio spustit jako správce. Jinak je zakázána na tlačítko odinstalovat.
>
>

### <a id="use_nuget"></a>Přidání knihovny Twilio do projektu sady Visual Studio:
1. Otevřete řešení v sadě Visual Studio.
2. Klikněte pravým tlačítkem na **odkazy**.
3. Klikněte na tlačítko **spravovat balíčky NuGet...**
4. Klikněte na tlačítko **Online**.
5. Zadejte do vyhledávacího pole online *twilio*.
6. Klikněte na tlačítko **nainstalovat** na balíček Twilio.

## <a id="howto_make_call"></a>Postupy: volání odchozí
Následující ukazuje, jak zajistit odchozí volání pomocí **CallResource** třídy. Tento kód také používá zadaný Twilio lokality k vrácení odpovědi Twilio Markup Language (TwiML). Dosaďte svoje hodnoty **k** a **z** telefonních čísel a ujistěte se, abyste ověřili **z** telefonní číslo pro svůj účet Twilio před spuštěním kódu.

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

Další informace o parametry předané do **CallResource.Create** metodu, najdete v části [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Jak je uvedeno, tento kód používá k vrácení odpovědi TwiML zadaný Twilio lokality. Místo toho můžete použít vlastní funkční web zajistit TwiML odpovědi. Další informace najdete v tématu [postup: Zadejte TwiML odpovědí z vlastního webu](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Postupy: odeslání zprávy SMS
Následující snímek obrazovky ukazuje, jak odeslat zprávu SMS pomocí **MessageResource** třídy. **z** číslo poskytne Twilio pro zkušebními účty k odesílání zpráv serveru SMS. **k** musí ověřit číslo účtu Twilio můžete před spuštěním kódu.

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

## <a id="howto_provide_twiml_responses"></a>Postupy: poskytování TwiML odpovědí z vlastního webu
Pokud vaše aplikace zahájí volání rozhraní API Twilio – například prostřednictvím **CallResource.Create** metoda - Twilio odešle žádost na adresu URL, kterou je očekáván vrátí odpověď TwiML. V příkladu v [postupy: volání odchozí](#howto_make_call) používá adresu URL poskytnutou Twilio [http://twimlets.com/message] [ twimlet_message_url] vrátit odpověď.

> [!NOTE]
> Při TwiML je určen k použití pomocí webové služby, můžete zobrazit TwiML v prohlížeči. Klikněte například na [http://twimlets.com/message] [ twimlet_message_url] zobrazíte prázdnou &lt;odpovědi&gt; element; například klikněte na tlačítko [http://twimlets.com/message? Zpráva % 5B0 %5 D = Hello % 20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) zobrazíte &lt;odpovědi&gt; elementu, který obsahuje &lt;indikované&gt; element.
>
>

Aniž byste museli spoléhat na adresu URL pro zadaný Twilio, můžete vytvořit vlastního webu adresu URL, která vrací odpovědi HTTP. Můžete vytvořit web v libovolném jazyce, který vrací odpovědi HTTP. Toto téma předpokládá, že budete hostování adresu URL z obecné obslužné rutiny ASP.NET.

Obslužná rutina následující ASP.NET sestavuje TwiML odpovědi, která uvádí, že **Hello, World** při volání.

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
    
Jak je vidět na výše uvedeném příkladu, odpověď TwiML je jednoduše dokument XML. Knihovna Twilio.TwiML obsahuje třídy, které způsobí vygenerování TwiML za vás. Následující příklad vytvoří ekvivalentní odpovědi jako v příkladu nahoře, ale používá **VoiceResponse** třídy.

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

Další informace o TwiML najdete v tématu [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Jakmile jste nastavili způsob, jak poskytnout TwiML odpovědí, může předat tuto adresu URL k **CallResource.Create** metoda. Například pokud máte webovou aplikaci s názvem MyTwiML nasadit do cloudové služby Azure a názvem vaší obslužné rutiny ASP.NET je mytwiml.ashx, adresa URL se dá předat do **CallResource.Create** jak znázorňuje následující ukázka kódu:

    // This sample uses the sandbox number provided by Twilio to make the call.
    // Place the call.
    var call = CallResource.Create(
        to: new PhoneNumber("+NNNNNNNNNN"),
        from: new PhoneNumber("NNNNNNNNNN"),
        url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
        }

Další informace o používání Twilio v Azure s ASP.NET najdete v tématu [postup telefonní hovor ve webové roli v Azure pomocí Twilio][howto_phonecall_dotnet].

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
