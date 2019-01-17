---
title: Ověřování, registrace, upravte profil v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak sestavit aplikaci klasické pracovní plochy Windows, která zahrnuje přihlášení, registraci, a správy profilů pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fc32cf40266bdad1aa5365b30a27210ad735f299
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354378"
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Vytváření desktopových aplikací pro Windows
Pomocí Azure Active Directory (Azure AD) B2C můžete přidat funkce správy identity výkonné samoobslužné služby do aplikace klasické pracovní plochy v několika krocích. Tento článek vám ukáže jak vytvořit aplikaci "seznam úkolů".NET Windows Presentation Foundation (WPF), která zahrnuje uživatelské registrace, přihlašování a správy profilů. Aplikace bude zahrnovat podporu registrace a přihlášení pomocí uživatelského jména nebo e-mailu. Bude také zahrnovat podporu registrace a přihlášení pomocí účtů na sociálních sítích, jako je například Facebook nebo Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace
Dále musíte vytvořit aplikaci v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Chcete-li vytvořit aplikaci, postupujte podle [těchto pokynů](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Zahrnout **nativního klienta** v aplikaci.
* Kopírovat **identifikátor URI pro přesměrování** `urn:ietf:wg:oauth:2.0:oob`. To je výchozí URL pro tento příklad.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Budete ho potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady
V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato ukázka kódu obsahuje tři činnosti identity: registrace, přihlášení a úpravy profilu. Při vytváření zásady nezapomeňte na následující:

* Zvolit v okně zprostředkovatelé identity buď **Registrace pomocí ID uživatele** nebo **Registrace pomocí e-mailu**.
* Zvolit **Zobrazovaný název** a další atributy registrace ve svojí registrační zásadě.
* Zvolit **Zobrazovaný název** a deklarace identity **ID objektu** jako deklarace identity aplikace v každé zásadě. Můžete zvolit i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Měl by mít předponu `b2c_1_`. Tyto názvy zásad budete potřebovat později.

Po úspěšném vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-code"></a>Stáhněte si kód
Kód k tomuto kurzu [je udržovaný na GitHubu](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Chcete-li během čtení tohoto návodu rovnou sestavit ukázku, můžete si [stáhnout kostru projektu v souboru ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Kostru můžete také klonovat:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Dokončená aplikace je také [k dispozici jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) nebo ve větvi `complete` stejného úložiště.

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. `TaskClient` Projektu je desktopová aplikace WPF, kterou uživatel komunikuje. Pro účely tohoto kurzu volá úkol back endové webové rozhraní API, hostované v Azure, které ukládá seznam úkolů každého uživatele. Není potřeba vytvářet webová rozhraní API, jsme již je spuštěna za vás.

Informace o tom, jak webové rozhraní API bezpečně ověřuje požadavky s využitím Azure AD B2C, podívejte se [webového rozhraní API Začínáme článku](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Spuštění zásady
Vaše aplikace komunikovala s Azure AD B2C odesláním zprávy o ověřování, které určují zásady, kterou chce spouštět jako součást požadavku HTTP. Pro desktopové aplikace .NET můžete pomocí Microsoft Authentication Library (MSAL) ve verzi preview můžete odesílat zprávy o ověřování OAuth 2.0, spuštění zásad a získat tokeny, které volání webového rozhraní API.

### <a name="install-msal"></a>Nainstalujte MSAL
Přidat MSAL k `TaskClient` projektu pomocí konzole Správce balíčků Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Zadejte podrobnosti o svém B2C
Otevřete soubor `Globals.cs` a všechny hodnoty vlastností nahradit vlastními. Tato třída se používá v rámci `TaskClient` odkaz běžně používané hodnoty.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Vytvořte PublicClientApplication
Primární třída MSAL je `PublicClientApplication`. Tato třída reprezentuje vaši aplikaci v systému Azure AD B2C. Když initalizes aplikace vytvořit instanci `PublicClientApplication` v `MainWindow.xaml.cs`. To je možné v rámci okna.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default. Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Zahájení registrace toku
Když uživatel požádá o na příznaky nahoru, budete chtít zahájit registraci tok, který používá zásady registrace, který jste vytvořili. S použitím MSAL ho prostě zavoláte `pca.AcquireTokenAsync(...)`. Parametry předání `AcquireTokenAsync(...)` určit, které token se zobrazí, zásady používané v žádosti o ověření a další.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API). Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Zahájení toku přihlášení
Stejným způsobem zahájení registrace tok můžete spustit tok přihlášení. Když se uživatel přihlásí, provede stejné volání MSAL, tentokrát pomocí vaší registrační zásady:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Spustit tok, který upravit profil
Zásady úprav profilu můžete znovu spustit stejným způsobem:

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Ve všech těchto případech MSAL buď vrátí token v `AuthenticationResult` nebo vyvolá výjimku. Pokaždé, když získáváte token z MSAL, můžete použít `AuthenticationResult.User` objektu k aktualizaci dat uživatele v aplikacích, jako je například uživatelské rozhraní. ADAL také ukládá do mezipaměti tokenu pro použití v ostatních částech aplikace.

### <a name="check-for-tokens-on-app-start"></a>Kontrolovat tokeny při spuštění aplikace
Můžete také použití MSAL k udržovat přehled o stavu přihlášení uživatele. V této aplikaci chceme, aby uživatel zůstane přihlášený i po zavření aplikace a znovu otevřete. Vrátí zpět do `OnInitialized` přepsat, použijte pro MSAL `AcquireTokenSilent` metodu ke kontrole s mezipamětí tokenů:

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache. Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Volání rozhraní API úkolů
Knihovna MSAL mají nyní používají ke spouštění zásad a získat tokeny. Pokud chcete použít jeden tyto tokeny pro volání rozhraní API úkolů, můžete opět pomocí vaší MSAL `AcquireTokenSilent` metodu ke kontrole s mezipamětí tokenů:

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Při volání `AcquireTokenSilentAsync(...)` úspěšné a nebude nalezen token v mezipaměti, můžete přidat token, který má `Authorization` hlavičku požadavku HTTP. Úlohy webové rozhraní API bude tuto hlavičku používají k ověření žádosti o čtení seznamu úkolů uživatele:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Odhlásit uživatele
Nakonec můžete použití MSAL k ukončení relace uživatele s aplikací, když uživatel vybere **Odhlásit**. Při použití MSAL toho dosahuje tím, že zrušíte všechny tokeny z mezipamětí tokenů:

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace
Nakonec sestavte a spusťte ukázku. Zaregistrujte se pro aplikaci s použitím e-mailové adresy nebo uživatelského jména. Odhlaste se a znovu se přihlásit pod stejným uživatelem. Upravte profil daného uživatele. Odhlaste se a zaregistrujte s použitím jiného uživatele.

## <a name="add-social-idps"></a>Přidání sociálních sítí zprostředkovatelů identity
V současné době aplikace podporuje pouze registrace uživatele a přihlašování, použít **místní účty**. Toto jsou účty uložené v adresáři B2C, které používají uživatelské jméno a heslo. Pomocí Azure AD B2C, můžete přidat podporu pro jiných zprostředkovatelů identity (IDP) bez změny vašich kód.

Chcete-li přidat zprostředkovatelů sociálních sítí do vaší aplikace, začněte podle podrobných pokynů v těchto článcích. Pro každého zprostředkovatele identity, které chcete podporovat budete muset zaregistrovat aplikaci v daném systému a získat ID klienta.

* [Nastavení sítě Facebook jako identity](active-directory-b2c-setup-fb-app.md)
* [Nastavení Google jako identity](active-directory-b2c-setup-goog-app.md)
* [Nastavení Amazon jako identity](active-directory-b2c-setup-amzn-app.md)
* [Nastavení Linkedinu jako identity](active-directory-b2c-setup-li-app.md)

Po přidání zprostředkovatele identity do vašeho tenanta B2C, budete muset upravit každé tři zásady zahrnout nové zprostředkovatelů identity, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). Po uložení zásady znovu spusťte aplikaci. Měli byste vidět nové zprostředkovatelů identity, které jsou přidány jako přihlášení a prostředí možností registrace v jednotlivých vaši identitu.

Můžete experimentovat s vašimi zásadami a podívejte se, na ukázkovou aplikaci. Přidat nebo odebrat zprostředkovatelů identity, manipulovat s deklaracemi identity aplikace nebo změnit atributy registrace. Experiment, dokud se nezobrazí, jak spojovat MSAL, zásady a žádosti o ověření.

Pro srovnání je hotová ukázka [k dispozici jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Můžete ho také klonovat z GitHubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
