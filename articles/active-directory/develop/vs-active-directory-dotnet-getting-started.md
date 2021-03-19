---
title: Začínáme se službou Azure AD v projektech .NET MVC | Azure
description: Jak začít používat Azure Active Directory v projektech .NET MVC po připojení k nebo vytvoření Azure AD pomocí připojených služeb sady Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165460"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Začínáme s Azure Active Directory (projekty ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek poskytuje další doprovodné materiály po přidání služby Active Directory do projektu ASP.NET MVC prostřednictvím příkazu **project > Connected Services** sady Visual Studio. Pokud jste ještě nepřidali službu do projektu, můžete to udělat kdykoli.

Podívejte se, [co se stalo s mým projektem MVC?](vs-active-directory-dotnet-what-happened.md) pro změny provedené v projektu při přidávání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu se napracovaly s `[Authorize]` atributem. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k těmto řadičům. K povolení anonymního přístup k řadiči odeberte tento atribut z kontroleru. Chcete-li nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto použití pro třídu kontroleru.

## <a name="adding-signin--signout-controls"></a>Přidávání ovládacích prvků pro přihlášení a odhlášení

Chcete-li přidat ovládací prvky pro přihlášení a odhlášení do zobrazení, můžete použít `_LoginPartial.cshtml` částečné zobrazení k přidání funkce do některého z vašich zobrazení. Tady je příklad funkcí přidaných do standardního `_Layout.cshtml` zobrazení. (Poznamenejte si poslední prvek ve třídě div s navigačním panelem třídy – sbalení):

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

- [Scénáře ověřování pro Azure Active Directory](./authentication-vs-authorization.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)