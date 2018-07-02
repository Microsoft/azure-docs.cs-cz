---
title: Jak používat služby sendgrid vám umožňuje e-mailů (.NET) | Microsoft Docs
description: Zjistěte, jak odeslat e-mailu pomocí e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky kódu jsou vytvořené v C# a používají .NET API.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: a5f07d02bfe4032d77a17e5972b88f6530125f28
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "27534851"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Postup odesílání e-mailu pomocí sendgrid vám umožňuje pomocí Azure
## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné úkoly programování s e-mailovou službu sendgrid vám umožňuje v Azure. Ukázky jsou napsané v jazyce C\# a podporuje standardní 1.3 .NET. Pokryté scénáře zahrnují vytváření e-mailu, odesílání e-mailu, přidání příloh a povolení různé e-mailu a sledování nastavení. Další informace o sendgrid vám umožňuje a odesílání e-mailu, najdete v článku [další kroky] [ Next steps] části.

## <a name="what-is-the-sendgrid-email-service"></a>Co je služba sendgrid vám umožňuje e-mailu?
Je sendgrid vám umožňuje [cloudový e-mailovou službu] poskytuje spolehlivé [doručování e-mailem transakční], škálovatelnost a analýzu v reálném čase společně s flexibilní rozhraní API, které umožňují snadnou vlastní integrace. Běžné případy použití sendgrid vám umožňuje patří:

* Automatické odesílání potvrzení nebo potvrzení nákupní zákazníkům.
* Správa distribuce uvádí pro odesílání zákazníkům měsíční letáků a reklamními nabídkami.
* Shromažďování metriky v reálném čase pro takové věci, jako e-mailu blokovaný a zákaznické engagement.
* Předávání dotazy zákazníků.
* Zpracování příchozích e-mailů.

Další informace najdete v článku [ https://sendgrid.com ](https://sendgrid.com) nebo sendgrid vám umožňuje na [knihovna jazyka C#] [ sendgrid-csharp] úložiště GitHub.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu sendgrid vám umožňuje
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odkaz na knihovně tříd rozhraní .NET sendgrid vám umožňuje
[Balíček NuGet sendgrid vám umožňuje](https://www.nuget.org/packages/Sendgrid) Nejsnadnějším způsobem, chcete-li získat sendgrid vám umožňuje rozhraní API a ke konfiguraci vaší aplikace s všechny závislosti. NuGet je součástí sady Microsoft Visual Studio 2015 rozšíření sady Visual Studio a vyšší usnadňuje instalace a aktualizace knihovny a nástroje.

> [!NOTE]
> Nainstalovat NuGet, pokud používáte verzi sady Visual Studio starší než Visual Studio 2015, najdete [ http://www.nuget.org ](http://www.nuget.org)a klikněte na **nainstalovat NuGet** tlačítko.
>
>

Chcete-li nainstalovat balíček NuGet sendgrid vám umožňuje v aplikaci, postupujte takto:

1. Klikněte na **nový projekt** a vyberte **šablony**.

   ![Vytvoření nového projektu][create-new-project]
2. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **odkazy**, pak klikněte na tlačítko **spravovat balíčky NuGet**.

   ![Balíček NuGet sendgrid vám umožňuje][SendGrid-NuGet-package]
3. Vyhledejte **sendgrid vám umožňuje** a vyberte **sendgrid vám umožňuje** položky v seznamu výsledků.
4. Vyberte z rozevíracího seznamu verze mohli pracovat v objektovém modelu nejnovější stabilní verze balíčku Nuget a rozhraní API ukázáno v tomto článku.

   ![Sendgrid vám umožňuje balíčku][sendgrid-package]
5. Klikněte na tlačítko **nainstalovat** k dokončení instalace a zavřete toto dialogové okno.

Knihovna tříd rozhraní .NET sendgrid vám umožňuje na nazývá **sendgrid vám umožňuje**. Obsahuje následující obory názvů:

* **Sendgrid vám umožňuje** pro komunikaci s rozhraním API sendgrid vám umožňuje společnosti.
* **SendGrid.Helpers.Mail** pro pomocné metody snadno vytvářet SendGridMessage objekty, které určují, jak odeslat e-mailů.

Přidejte následující deklarace oborů názvů kódu do horní části souboru žádné C# ve kterém chcete programový přístupu k e-mailovou službu sendgrid vám umožňuje.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Použití **SendGridMessage** objekt k vytvoření e-mailovou zprávu. Po vytvoření objektu zprávu můžete nastavit vlastnosti a metody, včetně e-mailu odesílatel, příjemce e-mailu a předmět a text e-mailu.

Následující příklad ukazuje, jak vytvořit objekt plně vyplněná e-mailu:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Další informace o všech vlastností a metod podporovaných **sendgrid vám umožňuje** zadejte najdete v tématu [sendgrid vám umožňuje csharp] [ sendgrid-csharp] na Githubu.

## <a name="how-to-send-an-email"></a>Postupy: odeslání e-mailu
Po vytvoření e-mailovou zprávu, můžete ho pomocí rozhraní API sendgrid vám umožňuje společnosti odeslat. Alternativně můžete použít [. NET je součástí knihovny][NET-library].

Odesílání e-mailu vyžaduje, že je klíč rozhraní API sendgrid vám umožňuje zadat. Pokud potřebujete podrobnosti o tom, jak nakonfigurovat klíče rozhraní API, navštivte klíče rozhraní API sendgrid vám umožňuje na [dokumentace][documentation].

Tyto přihlašovací údaje prostřednictvím portálu Azure může uložit kliknutím na tlačítko Nastavení aplikace a přidáním páry klíč/hodnota v části Nastavení aplikace.

 ![Nastavení aplikace Azure][azure_app_settings]

 Potom můžete získat z nich následujícím způsobem:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Následující příklady ukazují, jak k odesílání e-mailovou zprávu pomocí webového rozhraní API sendgrid vám umožňuje pomocí konzolové aplikace.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Postupy: odeslání e-mailu z ASP .NET Core API pomocí MailHelper – třída

Následujícím příkladu lze poslat jednu e-mailovou více osob z ASP .NET Core API pomocí `MailHelper` třídu `SendGrid.Helpers.Mail` oboru názvů. V tomto příkladu používáme ASP .NET Core 1.0. 

V tomto příkladu klíč rozhraní API byla uložena v `appsettings.json` souboru, který může být přepsáno z portálu Azure, jak je znázorněno v předchozích příkladech.

Obsah `appsettings.json` soubor by měl vypadat podobně jako:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Nejdřív je potřeba přidat níže uvedeného kódu v `Startup.cs` souboru projektu rozhraní API .NET Core. To je potřeba, takže jsme získat přístup `SENDGRID_API_KEY` z `appsettings.json` souboru pomocí vkládání závislostí do kontroleru rozhraní API. `IConfiguration` Rozhraní může vložit v konstruktoru řadiče po přidání do `ConfigureServices` metoda níže. Obsah `Startup.cs` souboru vypadá jako následující po přidání požadovaných kódu:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

V řadiči po vložení `IConfiguration` rozhraní, můžeme použít `CreateSingleEmailToMultipleRecipients` metodu `MailHelper` třída k odesílání několika příjemcům jednu e-mailovou. Metoda přijímá jeden další logickým parametrem s názvem `showAllRecipients`. Tento parametr můžete slouží ke kontrole, jestli příjemců e-mailu bude moci zobrazit všechny ostatní e-mailovou adresu v části na hlavičky e-mailu. Ukázkový kód pro řadič by měla být jako níže 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Přílohy mohou být přidány do zprávy voláním **AddAttachment** metoda a minimálně zadáte název souboru a kódováním Base64 obsahu, které chcete připojit. Může obsahovat více přiložených, po pro každý soubor chcete připojit, a to voláním této metody nebo pomocí **AddAttachments** metoda. Následující příklad ukazuje, přidání přílohu zprávu:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Postupy: nastavení e-mailu slouží k povolení zápatí stránky, sledování a analýzy
Sendgrid vám umožňuje poskytuje funkce další e-mailu pomocí e-mailu nastavení a nastavení sledování. Tato nastavení lze přidat do e-mailovou zprávu povolit konkrétní funkce, jako je sledování klikněte na tlačítko, Google analytics, předplatné, sledování a tak dále. Úplný seznam aplikací, najdete v článku [nastavení dokumentaci][settings-documentation].

Aplikace lze použít pro **sendgrid vám umožňuje** e-mailové zprávy pomocí metody implementované jako součást **SendGridMessage** třídy. Následující příklady ukazují zápatí a klikněte na tlačítko Sledování filtry:

Následující příklady ukazují zápatí a klikněte na tlačítko Sledování filtry:

### <a name="footer-settings"></a>Nastavení zápatí
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klikněte na tlačítko Sledování
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití služeb další sendgrid vám umožňuje
Sendgrid vám umožňuje nabízí několik rozhraní API a pomocí webhooků, které můžete využít další funkcionalitu v rámci aplikace Azure. Další podrobnosti najdete v tématu [referenční dokumentace rozhraní API sendgrid vám umožňuje][SendGrid API documentation].

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby sendgrid vám umožňuje e-mailu, postupujte podle následujících odkazech na další informace.

* C sendgrid vám umožňuje\# knihovny úložišti: [sendgrid vám umožňuje csharp][sendgrid-csharp]
* Dokumentaci k rozhraní API sendgrid vám umožňuje: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[cloudový e-mailovou službu]: https://sendgrid.com/solutions
[doručování e-mailem transakční]: https://sendgrid.com/use-cases/transactional-email

