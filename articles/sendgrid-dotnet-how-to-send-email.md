---
title: Jak používat e-mailové služby SendGrid (.NET) | Dokumentace Microsoftu
description: Zjistěte, jak poslat e-mailu pomocí e-mailové služby SendGrid v Azure. Ukázky kódu jsou vytvořeny v jazyce C# a používají .NET API.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634964"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Odeslání e-mailů pomocí Sendgridu s Azure
## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak k provádění běžných programovacích úloh s e-mailové služby SendGrid v Azure. Ukázky jsou napsané v jazyce C\# a podporuje .NET Standard 1.3. Pokryté scénáře zahrnují vytváření e-mailu, odesílání e-mailů, přidávání příloh a povolení různé e-mailu a nastavení sledování. Další informace o SendGrid a odesílání e-mailu, najdete v článku [další kroky] [ Next steps] oddílu.

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailové služby SendGrid?
SendGrid je [založené na cloudu e-mailové služby] , která poskytuje spolehlivé [doručování transakční e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilní API, která usnadňují vlastní integraci. Běžné případy použití SendGrid patří:

* Automaticky odesílá zákazníkům potvrzení nebo potvrzení nákupu.
* Správa distribuční seznamy pro odesílání zákazníkům měsíční letáků a propagační akce.
* Shromažďování metrik v reálném čase pro takové věci, jako jsou e-mailu blokovaný a zapojení zákazníků.
* Předávání dotazy zákazníků.
* Zpracování příchozích e-mailů.

Další informace najdete v článku [ https://sendgrid.com ](https://sendgrid.com) nebo Sendgridu [knihovny v jazyce C#] [ sendgrid-csharp] úložiště GitHub.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referenční dokumentace knihovny tříd .NET SendGrid
[Balíček SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) představuje nejjednodušší způsob, jak rozhraní API SendGrid a nakonfigurovat svoji aplikaci se všemi závislostmi. NuGet je rozšíření sady Visual Studio, který je součástí sady Microsoft Visual Studio 2015 a výše, který umožňuje snadno instalovat a aktualizovat knihovny a nástroje.

> [!NOTE]
> Nainstalovat NuGet, pokud používáte verzi sady Visual Studio starší než Visual Studio 2015, najdete v tématu [ http://www.nuget.org ](http://www.nuget.org)a klikněte na tlačítko **nainstalovat NuGet** tlačítko.
>
>

Chcete-li nainstalovat balíček SendGrid NuGet ve vaší aplikaci, postupujte takto:

1. Klikněte na **nový projekt** a vyberte **šablony**.

   ![Vytvoření nového projektu][create-new-project]
2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **odkazy**, pak klikněte na tlačítko **spravovat balíčky NuGet**.

   ![Balíček NuGet služby SendGrid][SendGrid-NuGet-package]
3. Vyhledejte **SendGrid** a vyberte **SendGrid** položky v seznamu výsledků.
4. Nejnovější stabilní verze balíčku Nuget. Vyberte z rozevíracího seznamu verze bude moct pracovat s modelem objektu a v tomto článku jsme vám ukázali rozhraní API.

   ![Balíček služby SendGrid][sendgrid-package]
5. Klikněte na tlačítko **nainstalovat** dokončení instalace, a pak zavřete toto dialogové okno.

Knihovna tříd rozhraní .NET Sendgridu se nazývá **SendGrid**. Obsahuje následující obory názvů:

* **SendGrid** pro komunikaci s rozhraním API pro SendGrid.
* **SendGrid.Helpers.Mail** pro pomocné metody můžete snadno vytvořit SendGridMessage objekty, které určují způsob odesílání e-mailů.

Přidejte následující deklarace oborů názvů kód na začátek souboru C# ve kterém chcete programový přístup k e-mailové služby SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Použití **SendGridMessage** objekt k vytvoření e-mailovou zprávu. Jakmile je vytvořen objekt zprávy, můžete nastavit vlastnosti a metody, včetně e-mailu odesílatele, příjemce e-mailu a zadání předmětu a textu e-mailu.

Následující příklad ukazuje, jak vytvořit objekt zcela naplněná e-mailu:

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

Další informace o všech vlastností a metod podporovaných **SendGrid** zadejte naleznete v tématu [sendgrid-csharp] [ sendgrid-csharp] na Githubu.

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailu
Po vytvoření e-mailovou zprávu, můžete ho pomocí rozhraní API Sendgridu odeslat. Alternativně můžete použít [. NET společnosti součástí knihovny][NET-library].

Odesílání e-mailů vyžaduje, abyste zadali svůj klíč rozhraní API SendGrid. Pokud potřebujete zobrazit podrobnosti o tom, jak nakonfigurovat klíče rozhraní API, navštivte prosím klíče rozhraní API Sendgridu [dokumentaci][documentation].

Tyto přihlašovací údaje mohou uložíte kliknutím na nastavení aplikace a přidání dvojice klíč/hodnota v části Nastavení aplikace prostřednictvím portálu Azure.

 ![Nastavení aplikace Azure][azure_app_settings]

 Poté je může k nim přistupovat takto:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Následující příklady ukazují, jak k odesílání e-mailovou zprávu pomocí konzolové aplikace webového rozhraní API SendGrid.

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
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Postupy: odeslání e-mailu z horizontálních oddílů pomocí třídy MailHelper rozhraní ASP .NET Core API

Následujícím příkladu je možné odeslat e-mail více osob z pomocí rozhraní API ASP .NET Core `MailHelper` třídu `SendGrid.Helpers.Mail` oboru názvů. V tomto příkladu používáme ASP .NET Core 1.0. 

V tomto příkladu byl uložen do klíč rozhraní API `appsettings.json` souboru, který lze přepsat na webu Azure Portal, jak je znázorněno v předchozích příkladech.

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

Nejdřív potřebujeme přidat níže uvedeného kódu v `Startup.cs` soubor projektu rozhraní API pro .NET Core. To je potřeba, takže jsme se dostanete `SENDGRID_API_KEY` z `appsettings.json` souborů s využitím vkládání závislostí v kontroleru rozhraní API. `IConfiguration` Rozhraní můžete vložený v konstruktoru řadiče po jeho přidání `ConfigureServices` metodu. Obsah `Startup.cs` soubor bude vypadat jako následující po přidání požadovaný kód:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

V řadiči, po vložení `IConfiguration` rozhraní, můžeme použít `CreateSingleEmailToMultipleRecipients` metodu `MailHelper` třídy odeslat e-mail více příjemcům. Metoda přijímá jeden další logický parametr s názvem `showAllRecipients`. Tento parametr lze použít k řízení, zda příjemců e-mailu bude moci zobrazit každé jiné e-mailovou adresu v části do hlavičky e-mailu. Ukázkový kód pro kontroler musí mít jako míň než 

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
Přílohy se dají přidat na zprávu voláním **AddAttachment** metoda a minimálně zadáte název souboru a kódování Base64 obsah ke kterému chcete připojit. Může obsahovat více přiložených, zavoláním této metody jednou pro každý soubor, budete chtít připojit nebo s použitím **AddAttachments** metody. Následující příklad ukazuje, přidávání přílohy ke zprávě:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Postupy: použití nastavení e-mailu k povolení zápatí, sledování a analýza
SendGrid umožňuje zajistit další e-mailové funkce prostřednictvím nastavení pošty a sledování nastavení. Tato nastavení můžete přidat do e-mailovou zprávu povolit konkrétní funkce, jako je například sledování kliknutí, Google analytics, předplatné, sledování a tak dále. Úplný seznam aplikací, najdete v článku [nastavení dokumentaci][settings-documentation].

Aplikace lze použít u **SendGrid** e-mailové zprávy pomocí metod, které implementují jako součást **SendGridMessage** třídy. Následující příklady ukazují zápatí a klikněte na tlačítko filtry pro sledování:

Následující příklady ukazují zápatí a klikněte na tlačítko filtry pro sledování:

### <a name="footer-settings"></a>Nastavení zápatí
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Sledování kliknutí.
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: použití služeb další SendGrid
SendGrid nabízí několik rozhraní API a webhooky, které vám umožní využívat další funkce v aplikaci Azure. Další podrobnosti najdete v tématu [Reference k rozhraní API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy služby e-mailu Sendgridu, použijte tyto odkazy na další informace.

* SendGrid C\# knihovny úložiště: [sendgrid-csharp][sendgrid-csharp]
* Dokumentace k rozhraní API SendGrid: <https://sendgrid.com/docs>

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

[založené na cloudu e-mailové služby]: https://sendgrid.com/solutions
[doručování transakční e-mailů]: https://sendgrid.com/use-cases/transactional-email

