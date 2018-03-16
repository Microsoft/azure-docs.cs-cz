---
title: "Začínáme s Azure AD v projektech Visual Studio .NET MVC | Microsoft Docs"
description: "Jak začít používat Azure Active Directory v projektech .NET MVC po připojení k nebo vytváření Azure AD pomocí sady Visual Studio připojené služby"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 07fa4655d9e7ad74cae33391d55c7c9be1d446a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Začínáme s Azure Active Directory (projekty ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek obsahuje další pokyny po přidání do projektu ASP.NET MVC prostřednictvím služby Active Directory **Projekt > připojené služby** příkaz sady Visual Studio. Pokud jste již přidali službu do projektu, můžete tak učinit kdykoli.

V tématu [co se stalo s Moje projektu MVC?](vs-active-directory-dotnet-what-happened.md) pro změny provedené při přidání připojené služby v projektu.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžádání ověření řadičům přístup

Všechny řadiče ve vašem projektu byly ozdobené s `[Authorize]` atribut. Tento atribut vyžaduje, aby uživatel k ověření před přístupem k tyto řadiče. Chcete-li umožňují řadiči získat anonymní přístup, odeberte tento atribut z řadiče. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje ověření, namísto aplikace do třídy kontroleru.

## <a name="adding-signin--signout-controls"></a>Přidání přihlášení / odhlášení ovládací prvky

K přidávání ovládacích prvků přihlášení/odhlášení do zobrazení, můžete použít `_LoginPartial.cshtml` částečné zobrazení k přidání funkce do jednoho zobrazení. Tady je příklad funkci přidat do standardní `_Layout.cshtml` zobrazení. (Všimněte si posledním prvkem v div s navigační panel sbalí třída):

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

- [Scénáře ověřování pro Azure Active Directory](active-directory-authentication-scenarios.md)
- [Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
