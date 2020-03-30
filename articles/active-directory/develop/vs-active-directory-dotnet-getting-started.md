---
title: Začínáme s Azure AD v projektech .NET MVC | Azure
description: Jak začít používat Azure Active Directory v projektech .NET MVC po připojení nebo vytvoření Azure AD pomocí připojených služeb Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: eae649a4de88373ee79e49ecb7d5f14564a3054b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159484"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Začínáme s Azure Active Directory (ASP.NET Projekty MVC)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek obsahuje další pokyny po přidání služby Active Directory do projektu mvc ASP.NET prostřednictvím příkazu **Project > Connected Services** sady Visual Studio. Pokud jste službu ještě do projektu nepřidali, můžete tak učinit kdykoli.

Podívejte [se, co se stalo s mým projektem MVC?](vs-active-directory-dotnet-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu byly `[Authorize]` ozdobeny atributem. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k těmto řadičům. Chcete-li povolit přístup k řadiči anonymně, odeberte tento atribut z řadiče. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto jeho použití na třídu kontroleru.

## <a name="adding-signin--signout-controls"></a>Přidání ovládacích prvků signin / signout

Chcete-li do zobrazení přidat ovládací prvky Přihlášení/odhlášení, můžete pomocí částečného `_LoginPartial.cshtml` zobrazení přidat funkce do jednoho ze zobrazení. Zde je příklad funkcí přidaných do `_Layout.cshtml` standardního zobrazení. (Všimněte si posledního prvku v div s navbar-collapse třídy):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Službu Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlášení s Microsoftem do ASP.NET webové aplikace](quickstart-v2-aspnet-webapp.md)
