---
title: Začínáme se službou Azure AD v projektech Visual Studio .NET MVC | Azure
description: Jak začít používat Azure Active Directory v projektech .NET MVC po připojení k nebo vytvoření Azure AD pomocí připojených služeb sady Visual Studio
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b0a6265f76cc7c799d162487999c129ec7e6a14
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803611"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Začínáme s Azure Active Directory (projekty ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek poskytuje další doprovodné materiály po přidání služby Active Directory do projektu ASP.NET MVC prostřednictvím příkazu **project > Connected Services** sady Visual Studio. Pokud jste ještě nepřidali službu do projektu, můžete to udělat kdykoli.

Podívejte se, [co se stalo s mým projektem MVC?](vs-active-directory-dotnet-what-happened.md) pro změny provedené v projektu při přidávání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu byly navýšené pomocí atributu `[Authorize]`. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k těmto řadičům. K povolení anonymního přístup k řadiči odeberte tento atribut z kontroleru. Chcete-li nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto použití pro třídu kontroleru.

## <a name="adding-signin--signout-controls"></a>Přidávání ovládacích prvků pro přihlášení a odhlášení

Chcete-li do zobrazení přidat ovládací prvky pro přihlášení a odhlášení, můžete použít částečné zobrazení `_LoginPartial.cshtml` k přidání funkce do jednoho z vašich zobrazení. Tady je příklad funkcí přidaných do standardního zobrazení `_Layout.cshtml`. (Poznamenejte si poslední prvek ve třídě div s navigačním panelem třídy – sbalení):

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

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v1-aspnet-webapp.md)
