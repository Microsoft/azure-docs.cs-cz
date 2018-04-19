---
title: "Nativní aplikace .NET v2.0 Azure Active Directory | Microsoft Docs"
description: "Jak vytvářet nativní aplikace .NET, která podepisuje uživatele přihlašují pomocí Account Microsoft i osobní a pracovní nebo školní účty."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88679e7dd71011f767cbe4de295c284516375d20
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Přidání přihlášení do aplikace Windows Desktop
S koncový bod v2.0, můžete rychle přidat ověřování desktopových aplikací s podporou pro oba osobní účty Microsoft a pracovní nebo školní účty.  Umožňuje také aplikace k bezpečné komunikaci s back-end webové rozhraní api, a také [Microsoft Graph](https://graph.microsoft.io) a několik [Office 365 jednotné rozhraní API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory (AD) a funkce jsou podporovány koncového bodu v2.0.  Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

Pro [nativní aplikace .NET, které běží na zařízení](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD poskytuje knihovnu ověřování Identity Microsoft nebo MSAL.  Jediným účelem je MSAL v životnosti je snadno své aplikace a získat tokeny pro volání webové služby.  Do ukazují, jak lze snadno je, zde jsme budete sestavení aplikace seznam úkolů WPF .NET který:

* Přihlásí uživatele & získá přístup k tokeny pomocí [protokol ověřování OAuth 2.0](active-directory-v2-protocols.md).
* Bezpečně volá back-end seznamu úkolů webové služby, která je také zabezpečeny OAuth 2.0.
* Odhlášení uživatele.

## <a name="download-sample-code"></a>Stáhněte si ukázkový kód
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Chcete-li sledovat, můžete [stáhnout kostru aplikace jako ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) nebo tuto kostru klonovat:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

Dokončená aplikace je k dispozici na konci tohoto kurzu také.

## <a name="register-an-app"></a>Registrace aplikace
Vytvoření nové aplikace v [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle těchto [podrobné kroky](active-directory-v2-app-registration.md).  Zkontrolujte, že:

* Zkopírování **Id aplikace** přiřazené vaší aplikaci, budete ho potřebovat brzy k dispozici.
* Přidat **Mobile** platformu pro vaši aplikaci.

## <a name="install--configure-msal"></a>Instalace a konfigurace MSAL
Teď, když máte aplikaci zaregistrovat se společností Microsoft, můžete nainstalovat MSAL a zadejte kód, týkající se identity.  Aby MSAL být schopni komunikovat se koncový bod v2.0 budete muset poskytnout některé informace o registraci vaší aplikace.

* Začněte tím, že přidání MSAL projekt TodoListClient pomocí konzoly Správce balíčků.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* Otevřete v projektu TodoListClient `app.config`.  Nahraďte hodnoty elementů v `<appSettings>` oddílu tak, aby odrážela hodnoty vstup na portálu pro registraci aplikace.  Váš kód bude odkazovat tyto hodnoty vždy, když používá MSAL.
  
  * `ida:ClientId` Je **Id aplikace** vaší aplikace, které jste zkopírovali z portálu.
* V seznamu úkolů služby projektu otevřete `web.config` v kořenovém adresáři projektu.  
  
  * Nahraďte `ida:Audience` hodnota se stejným **Id aplikace** z portálu.

## <a name="use-msal-to-get-tokens"></a>Získat tokeny pomocí MSAL
Základní princip za MSAL je vždy, když aplikace potřebuje přístupový token, jednoduše volat `app.AcquireToken(...)`, a MSAL nedojde zbytek.  

* V `TodoListClient` projekt, otevřete `MainWindow.xaml.cs` a najděte `OnInitialized(...)` metoda.  Prvním krokem je k chybě při inicializaci aplikace `PublicClientApplication` -MSAL na primární Třída reprezentující nativních aplikací.  Toto je, kde můžete předat MSAL souřadnice, které jsou zapotřebí ke komunikaci s Azure AD a určit, jak pro ukládání do mezipaměti tokenů.

```csharp
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Při spuštění aplikace, chceme, můžete zkontrolovat, pokud je uživatel již přihlášen k aplikaci.  Ale nechceme vyvolání uživatelského rozhraní přihlášení zatím – vytočit uživatele, klikněte na tlačítko "Přihlásit" Uděláte to tak.  Také v `OnInitialized(...)` metoda:

```csharp
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Pokud uživatel není přihlášený a kliknutí na tlačítko "Přihlásit", chceme vyvolání přihlášení uživatelského rozhraní a uživatelské zadat své přihlašovací údaje.  Implementace přihlašovací tlačítko obslužná rutina:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Pokud uživatel úspěšně přihlásí, bude přijímat a token do mezipaměti pro vás MSAL a můžete přejít k volání `GetTodoList()` metoda s jistotou.  Již zbývá k získání úloh uživatele je implementace `GetTodoList()` metoda.

```csharp
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Spusťte
Blahopřejeme! Nyní máte pracovní aplikace WPF v rozhraní .NET, který má schopnost ověřovat uživatele & bezpečně volání webového rozhraní API pomocí OAuth 2.0.  Spusťte oba projekty a přihlaste se pomocí osobního účtu Microsoft nebo pracovní nebo školní účet.  Přidání úkolů do seznamu úkolů daného uživatele.  Odhlásit se a přihlaste se zpět jako jiný uživatel Chcete-li zobrazit jejich seznam úkolů.  Zavřete aplikaci a znovu jej spusťte.  Všimněte si, jak uživatelské relace zůstává nedotčeno – je to způsobeno aplikace ukládá do mezipaměti tokenů v místním souboru.

MSAL usnadňuje začlenit běžné funkce identity do vaší aplikace pomocí osobní i pracovní účty.  Postará všechny dirty činností, které pro vás - Správa mezipaměti, podpora protokolu OAuth, představuje uživatele s přihlašovacími údaji uživatelského rozhraní, aktualizace vypršení platnosti tokenů a další.  Všechny skutečně potřebujete vědět, je jednoho volání rozhraní API `app.AcquireTokenAsync(...)`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [je k dispozici jako ZIP zde](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), nebo ji můžete klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout na pokročilejší témata.  Můžete se pokusit:

* [Zabezpečení webového rozhraní API TodoListService s koncovým bodem v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Další zdroje projděte si:  

* [Příručka vývojáře v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Značka "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostních incidentech tak, že navštívíte [tuto stránku](https://technet.microsoft.com/security/dd252948) a přihlásíte se k odběru služby Security Advisory Alerts.

