---
title: Ověřování, registrace, upravit profil .NET Azure Active Directory B2C | Microsoft Docs
description: Jak sestavit aplikaci plochy Windows, která zahrnuje přihlášení, registrace, a správy profilů pomocí Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: ec679115259611eb05021a68a552d25777a73356
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Sestavení aplikace na ploše systému Windows
Pomocí Azure Active Directory (Azure AD) B2C můžete přidat výkonné identity samoobslužné funkce pro správu k vaší aplikace na ploše v několika krocích. Tento článek vám ukáže, jak vytvořit aplikaci "seznam úkolů".NET Windows Presentation Foundation (WPF), která zahrnuje uživatelskou registraci, přihlašování a správu profilu. Aplikace bude zahrnují podporu pro registraci a přihlaste se pomocí uživatelského jména nebo e-mailu. Podporu registrace a přihlašování bude zahrnovat také pomocí sociálních účty například Facebook nebo Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta.  Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace
Dále musíte vytvořit aplikaci v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Chcete-li vytvořit aplikaci, postupujte podle [těchto pokynů](active-directory-b2c-app-registration.md).  Ujistěte se, že:

* Zahrnout **nativního klienta** v aplikaci.
* Kopírování **identifikátor URI pro přesměrování** `urn:ietf:wg:oauth:2.0:oob`. To je výchozí URL pro tento příklad.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Budete ho potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady
V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato ukázka kódu obsahuje tři činnosti identity: registrace, přihlášení a úprava profilu. Je třeba vytvořit zásadu pro každý typ, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Když vytváříte tyto tři zásady, nezapomeňte:

* Zvolit v okně zprostředkovatelé identity buď **Registrace pomocí ID uživatele** nebo **Registrace pomocí e-mailu**.
* Zvolit **Zobrazovaný název** a další atributy registrace ve svojí registrační zásadě.
* Zvolit **Zobrazovaný název** a deklarace identity **ID objektu** jako deklarace identity aplikace v každé zásadě. Můžete zvolit i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Měl by mít předponu `b2c_1_`.  Tyto názvy zásad budete potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po úspěšném vytvoření těchto tří zásad jste připraveni k sestavení aplikace.

## <a name="download-the-code"></a>Stáhněte si kód
Kód k tomuto kurzu [je udržovaný na GitHubu](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Chcete-li během čtení tohoto návodu rovnou sestavit ukázku, můžete si [stáhnout kostru projektu v souboru ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Kostru můžete také klonovat:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Dokončená aplikace je také [k dispozici jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) nebo ve větvi `complete` stejného úložiště.

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. `TaskClient` Je projekt WPF plochy aplikace, která uživatel komunikuje. Pro účely tohoto kurzu zavolá back-end úloh webového rozhraní API, hostované v Azure, která ukládá seznam úkolů každého uživatele.  Není potřeba vytvářet webové rozhraní API, jsme ji systémem pro vás už máte.

Informace o tom, jak webové rozhraní API bezpečně ověřuje požadavků pomocí Azure AD B2C, podívejte se [webové rozhraní API Začínáme článku](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Spuštění zásady
Aplikace komunikuje se službou Azure AD B2C odesláním zprávy ověřování, které zadejte zásady, které chtějí spouštět jako součást požadavku HTTP. Pro rozhraní .NET aplikací klasické pracovní plochy můžete pomocí náhledu Microsoft ověřování knihovny (MSAL) odesílat zprávy ověřování OAuth 2.0, spustit zásady a získat tokeny, které volají webové rozhraní API.

### <a name="install-msal"></a>Nainstalujte MSAL
Přidat MSAL k `TaskClient` projektu pomocí konzole Správce balíčků Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Zadejte podrobnosti o svém B2C
Otevřete soubor `Globals.cs` a všechny hodnoty vlastností, nahraďte vlastními. Tato třída se používá v rámci `TaskClient` odkaz běžně používané hodnoty.

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
Primární třída MSAL je `PublicClientApplication`. Tato třída reprezentuje vaší aplikace v systému Azure AD B2C. Když initalizes aplikace, vytvořte instanci `PublicClientApplication` v `MainWindow.xaml.cs`. To lze použít v celé okno.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Zahájení registrace toku
Když uživatel rozhodne pro přihlásí nahoru, chcete zahájit registraci toku, který používá registrační zásadě, kterou jste vytvořili. Pomocí MSAL jen zavoláte `pca.AcquireTokenAsync(...)`. Parametry, které předat `AcquireTokenAsync(...)` určit, které token se zobrazí, zásady používané v žádosti o ověření a další.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
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

### <a name="initiate-a-sign-in-flow"></a>Zahájit toku přihlášení
Tok přihlášení můžete zahájit stejným způsobem zahájení registrace toku. Když se uživatel přihlásí, ujistěte se stejným volání MSAL, tentokrát pomocí přihlášení zásad:

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

### <a name="initiate-an-edit-profile-flow"></a>Zahájit tok úpravy profilu
Znovu můžete zásadu upravit profil spustit stejným způsobem:

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

Ve všech těchto případech MSAL buď vrátí token v `AuthenticationResult` nebo vyvolá výjimku. Pokaždé, když získání tokenu z MSAL, můžete použít `AuthenticationResult.User` objekt, který chcete aktualizovat data uživatele v aplikaci, jako je například uživatelské rozhraní. ADAL také ukládá do mezipaměti token pro použití v dalších částí aplikace.

### <a name="check-for-tokens-on-app-start"></a>Zkontrolujte pro tokeny při spuštění aplikace
MSAL můžete také použít ke sledování stavu přihlášení uživatele.  V této aplikaci chceme uživateli zůstanou přihlášeného i po jejich zavřete aplikaci a znovu ho otevřete.  Zpět v `OnInitialized` přepsat, použijte na MSAL `AcquireTokenSilent` metoda zkontrolujte s mezipamětí tokenů:

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
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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

## <a name="call-the-task-api"></a>Volání rozhraní API úloh
Teď používáte MSAL ke spouštění zásad a získat tokeny.  Když chcete použít tyto tokeny k volání rozhraní API úloh, můžete znovu použít na MSAL `AcquireTokenSilent` metoda zkontrolujte s mezipamětí tokenů:

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

Při volání `AcquireTokenSilentAsync(...)` úspěšné a nebude nalezen token v mezipaměti, můžete přidat token, který má `Authorization` hlavičky požadavku HTTP. Úloha webové rozhraní API bude tuto hlavičku používají k ověření požadavek na čtení seznamu úkolů uživatele:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Odhlášení uživatele
Nakonec můžete MSAL k ukončení relace uživatele s aplikací, když uživatel vybere **Odhlásit se**.  Při použití MSAL, toho dosahuje tím, že zrušíte všechny tokeny z tokenu mezipaměti:

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
Nakonec sestavte a spusťte vzorku.  Zaregistrujte se pro aplikace pomocí e-mailové adresy nebo uživatelské jméno. Odhlásit se a znovu se přihlaste jako jeden uživatel. Upravte profil uživatele. Odhlásit se a přihlásit pomocí jiného uživatele.

## <a name="add-social-idps"></a>Přidat sociálních IDPs
V současné době aplikace podporuje pouze uživatele registrace a přihlášení, použít **místní účty**. Toto jsou účty uložené v adresáři B2C, které používají uživatelské jméno a heslo. Pomocí Azure AD B2C můžete přidat podporu pro jiných poskytovatelů identit (IDPs) beze změny některé z vašeho kódu.

Sociální IDPs přidat do vaší aplikace, začněte tím, že následující podrobné pokyny v těchto článcích. Pro každý deklarací identity, které chcete podporovat je třeba zaregistrovat aplikaci v daném systému a získat ID klienta.

* [Nastavení sítě Facebook jako IDP](active-directory-b2c-setup-fb-app.md)
* [Nastavit Google jako IDP](active-directory-b2c-setup-goog-app.md)
* [Nastavit Amazon jako IDP](active-directory-b2c-setup-amzn-app.md)
* [Nastavit LinkedIn jako IDP](active-directory-b2c-setup-li-app.md)

Po přidání zprostředkovatelů identity do vašeho adresáře B2C, budete muset upravit každou z vaší tři zásady, které zahrnují nové IDPs, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). Po uložení zásad, znovu spusťte aplikaci. Měli byste vidět nové IDPs přidat jako přihlášení a registrace možnosti v každé z vaší identity činnost.

Můžete experimentovat s vašimi zásadami a sledovat účinky na ukázkové aplikace. Přidat nebo odebrat IDPs, pracovat s deklarace identity aplikace nebo změnit atributy registrace. Experiment, dokud se nezobrazí, jak zásady, žádosti o ověření a MSAL tie společně.

Pro srovnání je hotová ukázka [je k dispozici jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Můžete ho také klonovat z GitHubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
