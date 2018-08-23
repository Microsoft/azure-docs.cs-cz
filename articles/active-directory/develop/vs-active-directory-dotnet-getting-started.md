---
title: Začínáme s Azure AD v projektech Visual Studio .NET MVC
description: Jak začít používat Azure Active Directory v projektech .NET MVC po připojení k nebo vytváření Azure AD pomocí sady Visual Studio připojené služby
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 437cb8b26cb33dfbb9c259e2842a59a765b78903
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054642"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Začínáme se službou Azure Active Directory (projekty ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek obsahuje další doprovodné materiály po přidání do projektu aplikace ASP.NET MVC prostřednictvím služby Active Directory **Projekt > připojené služby** příkazů sady Visual Studio. Pokud ještě není přidáte službu do projektu, provést kdykoli.

Zobrazit [co se stalo s mým projektem MVC?](vs-active-directory-dotnet-what-happened.md) pro změny do svého projektu při přidání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžádání ověření pro přístup k řadiči

Všechny řadiče ve vašem projektu byly opatřený s `[Authorize]` atribut. Tento atribut vyžaduje, aby uživatel ověřený před použitím těchto řadičů. Povolit kontroleru se dá přistupovat anonymně, odeberte tento atribut z kontroleru. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje ověření místo použití třídy kontroleru.

## <a name="adding-signin--signout-controls"></a>Přidání SignIn nebo SignOut ovládací prvky

Přidání přihlášení/odhlášení ovládacích prvků do zobrazení, můžete použít `_LoginPartial.cshtml` částečné zobrazení přidat funkce do jednoho zobrazení. Tady je příklad funkce přidat do standardních `_Layout.cshtml` zobrazení. (Poznámka: po posledním prvku v div s třída navbar sbalit):

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

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování s Microsoftem do webové aplikace ASP.NET](quickstart-v1-aspnet-webapp.md)
