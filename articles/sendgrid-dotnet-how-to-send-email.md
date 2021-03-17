---
title: Jak používat e-mailovou službu SendGrid (.NET) | Microsoft Docs
description: Naučte se odesílat e-maily pomocí e-mailové služby SendGrid v Azure. Ukázky kódu napsané v jazyce C# a používají rozhraní .NET API.
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
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196550"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Odeslání e-mailu pomocí SendGrid s Azure
## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné programovací úlohy pomocí e-mailové služby SendGrid v Azure. Ukázky jsou napsané v jazyce C \# a podporují .NET Standard 1,3. Mezi zahrnuté scénáře patří vytváření e-mailů, posílání e-mailů, přidávání příloh a povolení různých nastavení pošty a sledování. Další informace o SendGrid a odesílání e-mailů najdete v části [Další kroky][Next steps] .

## <a name="what-is-the-sendgrid-email-service"></a>Co je e-mailová služba SendGrid?
SendGrid je [Cloudová e-mailová služba] , která poskytuje spolehlivé zasílání [transakčních e-mailů], škálovatelnost a analýzy v reálném čase spolu s flexibilními rozhraními API, která usnadňují vlastní integraci. Mezi běžné případy použití SendGrid patří:

* Automatické odesílání potvrzení nebo potvrzení nákupu zákazníkům
* Správa distribučních seznamů pro posílání zákazníků měsíčně letáků a propagační akce.
* Shromažďování metrik v reálném čase pro věci, jako je Blokovaný e-mail a zákaznická zapojení.
* Předávání dotazů zákazníků.
* Zpracování příchozích e-mailů.

Další informace najdete v [https://sendgrid.com](https://sendgrid.com) úložišti GitHub [knihovny C#][sendgrid-csharp] nebo SendGrid.

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Odkaz na knihovnu tříd rozhraní .NET SendGrid
[SendGrid balíček NuGet](https://www.nuget.org/packages/Sendgrid) je nejjednodušší způsob, jak získat rozhraní SendGrid API a nakonfigurovat aplikaci se všemi závislostmi. NuGet je rozšíření sady Visual Studio, které je součástí Microsoft Visual Studio 2015 a novějšího, které usnadňuje instalaci a aktualizaci knihoven a nástrojů.

> [!NOTE]
> Pokud chcete nainstalovat NuGet, pokud používáte verzi sady Visual Studio starší než Visual Studio 2015, přejděte na stránku [https://www.nuget.org](https://www.nuget.org) a klikněte na tlačítko **instalovat NuGet** .
>
>

Pokud chcete do aplikace nainstalovat balíček NuGet SendGrid, udělejte toto:

1. Klikněte na **Nový projekt** a vyberte **šablonu**.

   ![Vytvoření nového projektu][create-new-project]
2. V **Průzkumník řešení** klikněte pravým tlačítkem na **odkazy** a pak klikněte na **Spravovat balíčky NuGet**.

   ![Balíček NuGet SendGrid][SendGrid-NuGet-package]
3. Vyhledejte **SendGrid** a v seznamu výsledků vyberte položku **SendGrid** .
4. Vyberte nejnovější stabilní verzi balíčku NuGet z rozevírací nabídky verze, abyste mohli pracovat s objektovým modelem a rozhraními API, které jsou znázorněné v tomto článku.

   ![Balíček SendGrid][sendgrid-package]
5. Instalaci dokončíte kliknutím na **instalovat** a potom zavřete toto dialogové okno.

Knihovna tříd .NET SendGrid se nazývá **SendGrid**. Obsahuje následující obory názvů:

* **SendGrid** pro komunikaci s rozhraním API SendGrid.
* **SendGrid. helps. mail** pro pomocné metody, které umožňují snadno vytvářet objekty SendGridMessage, které určují, jak se mají posílat e-maily.

Přidejte následující deklarace oboru názvů kódu na začátek jakéhokoli souboru C#, ve kterém chcete programově získat přístup k e-mailové službě SendGrid.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Postupy: vytvoření e-mailu
Pomocí objektu **SendGridMessage** můžete vytvořit e-mailovou zprávu. Po vytvoření objektu zprávy můžete nastavit vlastnosti a metody, včetně odesílatele e-mailu, příjemce e-mailu a předmětu a těla e-mailu.

Následující příklad ukazuje, jak vytvořit plně vyplněný objekt e-mailu:

```csharp
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
```

Další informace o všech vlastnostech a metodách, které podporuje typ **SendGrid** , najdete v tématu [SendGrid-CSharp][sendgrid-csharp] na GitHubu.

## <a name="how-to-send-an-email"></a>Postupy: odesílání e-mailů
Po vytvoření e-mailové zprávy ji můžete poslat pomocí rozhraní API pro SendGrid. Případně můžete použít [. SÍŤ je vestavěná v knihovně][NET-library].

Odesílání e-mailů vyžaduje zadání klíče rozhraní API SendGrid. Pokud potřebujete podrobnosti o konfiguraci klíčů rozhraní API, navštivte prosím [dokumentaci k][documentation]KLÍČŮM rozhraní API pro SendGrid.

Tyto přihlašovací údaje můžete uložit prostřednictvím Azure Portal kliknutím na nastavení aplikace a přidáním párů klíč/hodnota do nastavení aplikace.

![Nastavení aplikace Azure][azure_app_settings]

Pak k nim můžete přistupovat následujícím způsobem:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

Následující příklady ukazují, jak odeslat e-mailovou zprávu pomocí webového rozhraní API SendGrid s konzolovou aplikací.

```csharp
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
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Postupy: odesílání e-mailů z rozhraní API ASP .NET Core pomocí třídy MailHelper

Níže uvedený příklad lze použít k odeslání jediného e-mailu několika osobám z rozhraní API ASP .NET Core pomocí `MailHelper` třídy `SendGrid.Helpers.Mail` Namespace. V tomto příkladu používáme rozhraní ASP .NET Core 1,0. 

V tomto příkladu byl klíč rozhraní API uložený v souboru, `appsettings.json` který se dá přepsat z Azure Portal, jak je znázorněno v předchozích příkladech.

Obsah `appsettings.json` souboru by měl vypadat nějak takto:

```json
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
```

Nejdřív musíme v `Startup.cs` souboru projektu .NET Core API přidat následující kód. Tato možnost je nutná, aby bylo možné získat přístup k `SENDGRID_API_KEY` `appsettings.json` souboru ze souboru pomocí injektáže závislosti v řadiči rozhraní API. `IConfiguration`Rozhraní lze vložit do konstruktoru kontroleru po jeho přidání do `ConfigureServices` níže uvedené metody. Obsah `Startup.cs` souboru vypadá takto po přidání požadovaného kódu:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

Po vložení `IConfiguration` rozhraní můžeme na řadiči použít `CreateSingleEmailToMultipleRecipients` metodu `MailHelper` třídy k odeslání jediného e-mailu více příjemcům. Metoda přijímá jeden další logický parametr s názvem `showAllRecipients` . Tento parametr se dá použít k řízení, jestli příjemci e-mailu uvidí e-mailovou adresu každé jiné e-mailové adresy v hlavičce e-mailu. Vzorový kód pro kontroler by měl vypadat níže. 

```csharp
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
```

## <a name="how-to-add-an-attachment"></a>Postupy: Přidání přílohy
Přílohy lze do zprávy přidat voláním metody **AddAttachment** a minimálním zadáním názvu souboru a obsahu kódovaného pomocí Base64, který chcete připojit. Můžete zahrnout několik příloh voláním této metody jednou pro každý soubor, který chcete připojit, nebo pomocí metody **AddAttachments** . Následující příklad ukazuje přidání přílohy do zprávy:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Postupy: použití nastavení pošty k povolení zápatí, sledování a analýz
SendGrid poskytuje další funkce e-mailu prostřednictvím nastavení pošty a nastavení sledování. Tato nastavení se dají přidat do e-mailové zprávy, která umožní konkrétní funkce, jako je třeba sledování, Google Analytics, sledování předplatného atd. Úplný seznam aplikací najdete v [dokumentaci k nastavení][settings-documentation].

Aplikace lze použít pro **SendGrid** e-mailových zpráv pomocí metod implementovaných jako součást třídy **SendGridMessage** . Následující příklady znázorňují zápatí a klikněte na sledovací filtry:

Následující příklady znázorňují zápatí a klikněte na sledovací filtry:

### <a name="footer-settings"></a>Nastavení zápatí

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Klikněte na sledování.

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Postupy: používání dalších služeb SendGrid
SendGrid nabízí několik rozhraní API a webhooků, které můžete použít k využití dalších funkcí v rámci aplikace Azure. Další podrobnosti najdete v referenčních informacích k [rozhraní SendGrid API][SendGrid API documentation].

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy e-mailové služby SendGrid, získáte další informace na následujících odkazech.

* \#Úložiště knihovny C SendGrid: [SendGrid-CSharp][sendgrid-csharp]
* Dokumentace k rozhraní SendGrid API: <https://sendgrid.com/docs>

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
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[cloudové e-mailové služby]: https://sendgrid.com/solutions
[doručení transakčního e-mailu]: https://sendgrid.com/use-cases/transactional-email

