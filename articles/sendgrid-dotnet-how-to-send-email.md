---
title: Jak používat e-mailovou službu SendGrid (.NET) | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odesílat e-maily pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v c# a použít rozhraní .NET API.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: 33df6b5c8c5c16a6eb896944de05068affc2b407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062204"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Jak odeslat e-mail pomocí SendGrid s Azure
## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné úlohy programování s e-mailovou službou SendGrid v Azure. Ukázky jsou zapsány v C\# a podporuje .NET Standard 1.3. Zahrnuté scénáře zahrnují vytváření e-mailů, odesílání e-mailů, přidávání příloh a povolení různých nastavení pošty a sledování. Další informace o sendgridu a odesílání e-mailů najdete v části [Další kroky.][Next steps]

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [cloudová e-mailová služba,] která poskytuje spolehlivé [transakční doručování e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními api, která usnadňují vlastní integraci. Běžné případy použití SendGrid zahrnují:

* Automatické odesílání příjmů nebo potvrzení o nákupu zákazníkům.
* Správa distribučních seznamů pro zasílání měsíčních letáků a propagačních akcí zákazníkům.
* Shromažďování metrik v reálném čase pro věci, jako je blokovaný e-mail a zapojení zákazníků.
* Předávání dotazů zákazníků.
* Zpracování příchozích e-mailů.

Další informace najdete na webu [https://sendgrid.com](https://sendgrid.com) nebo sendgrid je [C# knihovna][sendgrid-csharp] GitHub úložiště.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odkaz na knihovnu tříd SendGrid .NET
Balíček [SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) je nejjednodušší způsob, jak získat rozhraní API SendGrid a nakonfigurovat aplikaci se všemi závislostmi. NuGet je rozšíření sady Visual Studio, které je součástí sady Microsoft Visual Studio 2015 a vyšší, které usnadňuje instalaci a aktualizaci knihoven a nástrojů.

> [!NOTE]
> Pokud nainstalujete aplikaci NuGet, pokud používáte verzi sady Visual [https://www.nuget.org](https://www.nuget.org)Studio starší než Visual Studio 2015, navštivte stránku a klikněte na tlačítko **Instalovat NuGet.**
>
>

Chcete-li nainstalovat balíček SendGrid NuGet do aplikace, postupujte takto:

1. Klikněte na **Nový projekt** a vyberte **šablonu**.

   ![Vytvoření nového projektu][create-new-project]
2. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **odkazy**a potom klepněte na příkaz **Spravovat balíčky NuGet**.

   ![Balíček SendGrid NuGet][SendGrid-NuGet-package]
3. Vyhledejte **sendgrid** a vyberte položku **SendGrid** v seznamu výsledků.
4. Vyberte nejnovější stabilní verzi balíčku Nuget z rozbalovací verze verze, aby bylo možné pracovat s objektovým modelem a api demonstroval v tomto článku.

   ![Balíček SendGrid][sendgrid-package]
5. Chcete-li dokončit instalaci, klepněte na **tlačítko Nainstalovat** a zavřete toto dialogové okno.

Knihovna tříd SendGrid .NET se nazývá **SendGrid**. Obsahuje následující obory názvů:

* **SendGrid** pro komunikaci s Rozhraní API SendGrid.
* **SendGrid.Helpers.Mail** pro pomocné metody snadno vytvářet SendGridMessage objekty, které určují, jak odesílat e-maily.

Přidejte následující deklarace oboru názvů kódu na začátek libovolného souboru jazyka C#, ve kterém chcete programově přistupovat k e-mailové službě SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Postup: Vytvoření e-mailu
K vytvoření e-mailové zprávy použijte objekt **SendGridMessage.** Po vytvoření objektu zprávy můžete nastavit vlastnosti a metody, včetně odesílatele e-mailu, příjemce e-mailu a předmětu a těla e-mailu.

Následující příklad ukazuje, jak vytvořit plně vyplněný e-mailový objekt:

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

Další informace o všech vlastnostech a metodách podporovaných typem **SendGrid** najdete v tématu [sendgrid-csharp][sendgrid-csharp] na GitHubu.

## <a name="how-to-send-an-email"></a>Postup: Odeslání e-mailu
Po vytvoření e-mailové zprávy ji můžete odeslat pomocí rozhraní API sendgridu. Alternativně můžete použít [. NET je postaven v knihovně][NET-library].

Odesílání e-mailu vyžaduje, abyste zadávali klíč rozhraní API SendGrid. Pokud potřebujete podrobnosti o konfiguraci klíčů rozhraní API, navštivte dokumentaci k [klíčům][documentation]rozhraní API sendgridu .

Tato pověření můžete uložit prostřednictvím portálu Azure kliknutím na nastavení aplikace a přidáním párů klíč/hodnota v části Nastavení aplikace.

 ![Nastavení aplikace Azure][azure_app_settings]

 Potom k nim můžete přistupovat následujícím způsobem:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Následující příklady ukazují, jak odeslat e-mailovou zprávu pomocí rozhraní SendGrid Web API s konzolovou aplikací.

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
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Postup: Odeslání e-mailu z rozhraní ASP .NET Core API pomocí třídy MailHelper

Níže uvedený příklad lze použít k odeslání jednoho e-mailu více osobám `MailHelper` z `SendGrid.Helpers.Mail` rozhraní ASP .NET Core API pomocí třídy oboru názvů. V tomto příkladu používáme rozhraní ASP .NET Core 1.0. 

V tomto příkladu byl klíč rozhraní `appsettings.json` API uložen v souboru, který může být přepsán z portálu Azure, jak je znázorněno ve výše uvedených příkladech.

Obsah souboru `appsettings.json` by měl vypadat podobně jako:

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

Nejprve musíme přidat níže uvedený `Startup.cs` kód v souboru projektu rozhraní .NET Core API. To je nutné, abychom `SENDGRID_API_KEY` mohli `appsettings.json` přistupovat ze souboru pomocí vkládání závislostí v řadiči rozhraní API. Rozhraní `IConfiguration` může být vloženo na konstruktor regulátoru po `ConfigureServices` přidání do níže uvedené metody. Obsah souboru `Startup.cs` vypadá takto po přidání požadovaného kódu:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Na řadiči, po `IConfiguration` vložení rozhraní, můžeme `CreateSingleEmailToMultipleRecipients` použít `MailHelper` metodu třídy k odeslání jednoho e-mailu více příjemcům. Metoda přijímá jeden další logický `showAllRecipients`parametr s názvem . Tento parametr lze použít k řízení, zda příjemci e-mailu budou moci zobrazit navzájem e-mailovou adresu v části Do záhlaví e-mailu. Ukázkový kód pro regulátor by měl být jako níže 

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
    
## <a name="how-to-add-an-attachment"></a>Postup: Přidání přílohy
Přílohy lze přidat do zprávy voláním **AddAttachment** metody a minimálně zadáním názvu souboru a base64 kódovaný obsah, který chcete připojit. Můžete zahrnout více příloh voláním této metody jednou pro každý soubor, který chcete připojit, nebo pomocí **AddAttachments** metody. Následující příklad ukazuje přidání přílohy ke zprávě:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Postup: Povolení zápatí, sledování a analýzy pomocí nastavení pošty
SendGrid poskytuje další funkce e-mailu pomocí nastavení pošty a nastavení sledování. Tato nastavení lze přidat do e-mailové zprávy, aby bylo možné povolit konkrétní funkce, jako je sledování kliknutí, google analytics, sledování předplatného a tak dále. Úplný seznam aplikací najdete v [dokumentaci k nastavení][settings-documentation].

Aplikace lze použít na **sendgrid** e-mailové zprávy pomocí metod implementovaných jako součást **SendGridMessage** třídy. Následující příklady ukazují filtry pro sledování zápatí a kliknutí:

Následující příklady ukazují filtry pro sledování zápatí a kliknutí:

### <a name="footer-settings"></a>Nastavení zápatí
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Sledování kliknutí
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Postup: Použití dalších služeb SendGrid
SendGrid nabízí několik api a webhooky, které můžete použít k využití dalších funkcí v rámci aplikace Azure. Další podrobnosti naleznete v [odkazu rozhraní API sendgrid .][SendGrid API documentation]

## <a name="next-steps"></a>Další kroky
Nyní, když jste se naučili základy služby SendGrid Email, postupujte podle těchto odkazů, abyste se dozvěděli více.

* SendGrid\# C knihovna repo: [sendgrid-csharp][sendgrid-csharp]
* Dokumentace rozhraní API SendGrid:<https://sendgrid.com/docs>

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

[cloudová e-mailová služba]: https://sendgrid.com/solutions
[transakční doručování e-mailů]: https://sendgrid.com/use-cases/transactional-email

