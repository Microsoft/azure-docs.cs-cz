---
title: Azure AD .NET klasické plochy (WPF) Začínáme | Dokumentace Microsoftu
description: Jak sestavit aplikaci .NET Windows Desktop, která se integruje s Azure AD pro přihlášení a vyžaduje Azure AD chráněné rozhraní API pomocí OAuth.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c4bc777c36608ff9aeeec3428af0103fa3ae29c4
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578958"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Azure AD .NET klasické plochy (WPF) Začínáme
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pokud vyvíjíte aplikace klasické pracovní plochy, Azure AD umožňuje jednoduché a nekomplikované můžete ověřovat uživatele pomocí jejich účtů služby Active Directory. Také umožňuje vaší aplikaci bezpečně využívat všechny webového rozhraní API chráněného službou Azure AD, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Pro .NET native klienty, kteří potřebují přístup k chráněným prostředkům Azure AD poskytuje Active Directory Authentication Library nebo ADAL. Jediným účelem společnosti ADAL v životě je k tomu, aby pro vaši aplikaci k získání přístupových tokenů. Abychom si předvedli, jak snadné je, tady vytvoříme aplikaci seznamu úkolů WPF .NET, který:

* Získá přístupové tokeny pro volání pomocí Azure AD Graph API [ověřovací protokol OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Vyhledá v adresáři uživatele pomocí daného aliasu.
* Značky uživatele navýšení kapacity.

Pokud chcete sestavit kompletní funkční aplikaci, bude nutné:

1. Registrace aplikace v Azure AD.
2. Instalace a konfigurace knihovny ADAL.
3. Používají knihovnu ADAL k získání tokenů z Azure AD.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) nebo [stáhnout úplnou vzorovou](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Budete také potřebovat tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě nemáte tenanta, [zjistěte, jak získat](quickstart-create-new-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registrace aplikace DirectorySearcher
Pokud chcete povolit aplikaci k získání tokenů, budete nejdřív muset zaregistrovat ve vašem tenantovi Azure AD a udělit mu oprávnění pro přístup k Azure AD Graph API:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu, klikněte na tlačítko na vašem účtu a v části **Directory** , zvolte tenanta Active Directory, ve kterém chcete registrace vaší aplikace.
3. Klikněte na **všechny služby** v levém navigačním podokně a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikací** a zvolte **přidat**.
5. Postupujte podle zobrazených výzev a vytvořte nový **nativní klientská aplikace**.
  * **Název** aplikace popíše vaše aplikace pro koncové uživatele
  * **Identifikátor Uri pro přesměrování** je kombinace schématu a řetězec, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například `http://DirectorySearcher`.
6. Po dokončení registrace AAD, přiřadí se vaše aplikace jedinečné ID aplikace. Tuto hodnotu budete potřebovat v dalších částech, proto zkopírujte ho ze stránky aplikace.
7. Z **nastavení** zvolte **požadovaná oprávnění** a zvolte **přidat**. Vyberte **Microsoft Graphu** jako rozhraní API a přidejte **čtení dat adresáře** oprávnění v rámci **delegovaná oprávnění**. To vám umožní vaši aplikaci k dotazování rozhraní Graph API pro uživatele.

## <a name="2-install--configure-adal"></a>2. Instalace a konfigurace ADAL
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat knihovnu ADAL a napsat kód souvisejícím s identitou. V pořadí pro ADAL mohl komunikovat s Azure AD je potřeba poskytnout určité informace o registraci vaší aplikace.

* Začněte tím, že ADAL do projektu přidáte, DirectorySearcher používání konzoly Správce balíčků.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Otevřete v projektu DirectorySearcher `app.config`. Nahraďte hodnoty prvků v `<appSettings>` části tak, aby odrážely hodnoty vstup na webu Azure Portal. Váš kód bude odkazovat na tyto hodnoty pokaždé, když se používá ADAL.
  * `ida:Tenant` Je doména tenanta Azure AD, např. contoso.onmicrosoft.com
  * `ida:ClientId` Je clientId vaší aplikace, které jste zkopírovali z portálu.
  * `ida:RedirectUri` Přesměrování je adresa url, které jste zaregistrovali na portálu.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Použití knihovny ADAL k získání tokenů z AAD
Základním principem za ADAL je, že pokaždé, když se vaše aplikace potřebuje přístupového tokenu, jednoduše volá `authContext.AcquireTokenAsync(...)`, a udělá zbytek ADAL. 

* V `DirectorySearcher` projekt, otevřete `MainWindow.xaml.cs` a vyhledejte `MainWindow()` metody. Prvním krokem je inicializace vaší aplikace `AuthenticationContext` -ADAL je primární třídou. To je, pokud předáte ADAL souřadnice potřebuje ke komunikaci s Azure AD a určit, jak pro ukládání do mezipaměti tokenů.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Nyní vyhledejte `Search(...)` metoda, která bude volána, když uživatel klikne na tlačítko "Vyhledat" v Uživatelském rozhraní aplikace. Tato metoda provádí požadavek GET na Azure AD Graph API k dotazování pro uživatele, jejichž hlavní název uživatele začíná daný hledaný termín. Ale pokud chcete dotazování rozhraní Graph API, je potřeba zahrnout access_token v `Authorization` záhlaví požadavku – to přichází ADAL.

```csharp
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Pokud vaše aplikace požaduje token voláním `AcquireTokenAsync(...)`, ADAL se pokusí vrátit token bez s žádostí o zadání přihlašovacích údajů. Pokud ADAL zjistí, že uživatel musí přihlásit a získat token, se zobrazí dialogové okno přihlášení, přihlašovacích údajů uživatele ke shromažďování a vrácení tokenu po úspěšném ověření. Pokud k vrácení tokenu z nějakého důvodu nedokáže ADAL, vyvolá výjimku `AdalException`.
* Všimněte si, že `AuthenticationResult` obsahuje objekt `UserInfo` objekt, který můžete použít ke shromažďování informací, které vaše aplikace může potřebovat. V DirectorySearcher `UserInfo` slouží k přizpůsobení uživatelského rozhraní aplikace s id uživatele.
* Když uživatel klikne na tlačítko "Odhlásit", chceme zajistit, aby další volání `AcquireTokenAsync(...)` vyzve uživatele k přihlášení. Ověřování adal to je stejně jednoduché jako vymazává se mezipaměť tokenů:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Pokud uživatel na tlačítko "Odhlásit", bude však chcete udržovat uživatelské relace, které provozují DirectorySearcher příště. Při spuštění aplikace můžete zkontrolovat mezipaměť tokenů ADAL pro existující token a odpovídajícím způsobem aktualizaci uživatelského rozhraní. V `CheckForCachedToken()` metoda, proveďte jiné volání `AcquireTokenAsync(...)`, tentokrát předávajícího `PromptBehavior.Never` parametr. `PromptBehavior.Never` ADAL bude informovat, že by neměla se uživateli výzva pro přihlášení a ADAL místo toho vyvoláním výjimky, pokud nemůže vrátit token.

```csharp
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Blahopřejeme! Nyní máte funkční aplikaci .NET WPF, která má schopnost ověřovat uživatele, bezpečně volat webové rozhraní API pomocí OAuth 2.0 a získat základní informace o uživateli. Pokud jste tak dosud neučinili, teď je čas k naplnění vašeho tenanta s některými uživateli. Spusťte aplikaci DirectorySearcher a přihlaste se pomocí jedné z těchto uživatelů. Hledání jiných uživatelů podle jejich hlavní název uživatele. Zavřete aplikaci a znovu jej spusťte. Všimněte si, jak uživatelské relace zůstává beze změny. Odhlásit a zase přihlásit jako jiný uživatel.

ADAL umožňuje snadno zahrnout všechny tyto běžné funkce identity do vaší aplikace. Postará veškeré práce čistý, Správa mezipaměti, podporu protokolu OAuth, nabízí ten samý uživatel s přihlášením uživatelského rozhraní, aktualizace vypršení platnosti tokenů a další. Všechno, co skutečně potřebujete znát jediného volání rozhraní API je `authContext.AcquireTokenAsync(...)`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) poskytuje [tady](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Se teď můžete přejít k další scénáře. Můžete vyzkoušet:

[Zabezpečení webového rozhraní API s využitím Azure AD .NET >>](quickstart-v1-dotnet-webapi.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

