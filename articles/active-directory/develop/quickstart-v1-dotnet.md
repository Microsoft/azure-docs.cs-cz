---
title: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace .NET klasické plochy (WPF) | Dokumentace Microsoftu
description: Zjistěte, jak sestavit aplikaci .NET Windows Desktop, která se integruje s Azure AD pro přihlášení a volání služby Azure AD chráněné rozhraní API pomocí OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b06ceb86e8406657766be1375889fcfe1b4673d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204538"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace .NET klasické plochy (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) pro .NET native klienty, kteří potřebují přístup k chráněným prostředkům, poskytuje Active Directory Authentication Library (ADAL). ADAL umožňuje snadno pro aplikaci k získání přístupových tokenů. 

V tomto rychlém startu se dozvíte jak vytvořit aplikaci seznamu úkolů WPF .NET, který:

* Získá přístupové tokeny pro volání rozhraní Azure AD Graph API pomocí ověřování protokolu OAuth 2.0.
* Vyhledá v adresáři uživatele pomocí daného aliasu.
* Značky uživatele navýšení kapacity.

Pokud chcete sestavit úplnou funkční aplikaci, budete muset:

1. Zaregistrovat aplikaci v Azure AD
2. Nainstalovat a nakonfigurovat knihovnu ADAL
3. Použít knihovnu ADAL k získání tokenů z Azure AD

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* [Stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) nebo [stáhnout úplnou vzorovou](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Mají tenanta služby Azure AD, kde můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ho ještě nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Registrace aplikace DirectorySearcher

Umožňuje aplikaci získat tokeny svou aplikaci zaregistrovat ve vašem tenantovi Azure AD a udělit mu oprávnění pro přístup k Azure AD Graph API:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu, vyberte svůj účet a v části **Directory** , zvolte tenanta Active Directory, ve kterém chcete registrace vaší aplikace.
3. Vyberte na **všechny služby** v levém navigačním podokně a zvolte **Azure Active Directory**.
4. Na **registrace aplikací**, zvolte **přidat**.
5. Postupujte podle zobrazených výzev a vytvořte nový **nativní** klientské aplikace.
    * **Název** aplikace popíše aplikaci pro koncové uživatele
    * **Identifikátor Uri pro přesměrování** je kombinace schématu a řetězec, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci, například `http://DirectorySearcher`.

6. Po dokončení registrace AAD, přiřadí se vaše aplikace jedinečné ID aplikace. Tuto hodnotu budete potřebovat v dalších částech, proto zkopírujte ho ze stránky aplikace.
7. Z **nastavení** zvolte **požadovaná oprávnění** a zvolte **přidat**. Vyberte **Microsoft Graphu** jako rozhraní API a v části **delegovaná oprávnění** přidat **čtení dat adresáře** oprávnění. Nastavení tohoto oprávnění umožňuje vaší aplikaci k dotazování rozhraní Graph API pro uživatele.

## <a name="step-2-install-and-configure-adal"></a>Krok 2: Instalace a konfigurace ADAL

Když už máte v Azure AD aplikaci, můžete nainstalovat knihovnu ADAL a zapsat svůj kód související s identitou. Aby knihovna ADAL komunikovala s Azure AD, musíte jí poskytnout určité informace o registraci aplikace.

1. Začátek přidáním knihovnou ADAL `DirectorySearcher` projektu pomocí konzoly Správce balíčků.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. V `DirectorySearcher` projekt, otevřete `app.config`.
1. Nahraďte hodnoty prvků v `<appSettings>` části tak, aby odrážely hodnoty vstup na webu Azure portal. Váš kód bude tyto hodnoty využívat vždy, když bude používat ADAL.
  * `ida:Tenant` Je doména tenanta Azure AD, například contoso.onmicrosoft.com
  * `ida:ClientId` Je ID klienta vaší aplikace, které jste zkopírovali z portálu.
  * `ida:RedirectUri` Je adresa URL pro přesměrování registrovaný na portálu.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Krok 3: Použití knihovny ADAL k získání tokenů z Azure AD

Základním principem za ADAL je, že pokaždé, když se vaše aplikace potřebuje přístupového tokenu, vaše aplikace jednoduše volá `authContext.AcquireTokenAsync(...)`, a udělá zbytek ADAL.

1. V `DirectorySearcher` projekt, otevřete `MainWindow.xaml.cs`.
1. Vyhledejte `MainWindow()` metody. 
1. Inicializace vaší aplikace `AuthenticationContext` -ADAL je primární třídou. `AuthenticationContext` Pokud předáte ADAL je souřadnice potřebuje ke komunikaci s Azure AD a určit, jak pro ukládání do mezipaměti tokenů.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Vyhledejte `Search(...)` metoda, která bude volána, když uživatel vybere **hledání** tlačítko v Uživatelském rozhraní aplikace. Tato metoda vytvoří pro rozhraní Azure AD Graph API požadavek GET, aby se uživatelů dotázal, který hlavní název uživatele (UPN) začíná na daný hledaný termín.
1. K dotazování rozhraní Graph API, zahrnují access_token v `Authorization` záhlaví požadavku, který přichází ADAL.

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

    Pokud vaše aplikace požaduje token voláním `AcquireTokenAsync(...)`, ADAL se pokusí vrátit token bez s žádostí o zadání přihlašovacích údajů.
    * Pokud ADAL zjistí, že uživatel musí přihlásit a získat token, se zobrazí dialogové okno přihlášení, přihlašovacích údajů uživatele ke shromažďování a vrácení tokenu po úspěšném ověření. 
    * Pokud k vrácení tokenu z nějakého důvodu nedokáže ADAL, vyvolá výjimku `AdalException`.

1. Všimněte si, že `AuthenticationResult` obsahuje objekt `UserInfo` objekt, který můžete použít ke shromažďování informací, které vaše aplikace může potřebovat. V DirectorySearcher `UserInfo` slouží k přizpůsobení uživatelského rozhraní aplikace pomocí ID uživatele.
1. Když uživatel vybere **Odhlásit** tlačítko, ujistěte se, že na další volání `AcquireTokenAsync(...)` vyzve uživatele k přihlášení. Snadno to můžete to tím, že zrušíte mezipaměť tokenu ověřování adal:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Pokud uživatel není kliknout **Odhlásit** tlačítko, je třeba dodržet uživatelské relace pro příští spuštění DirectorySearcher. Při spuštění aplikace můžete zkontrolovat mezipaměť tokenů ADAL pro existující token a odpovídajícím způsobem aktualizaci uživatelského rozhraní.

1. V `CheckForCachedToken()` metoda, proveďte jiné volání `AcquireTokenAsync(...)`, tentokrát předávajícího `PromptBehavior.Never` parametr. `PromptBehavior.Never` ADAL bude informovat, že by neměla se uživateli výzva pro přihlášení a ADAL místo toho vyvoláním výjimky, pokud nemůže vrátit token.

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

Blahopřejeme! Nyní máte funkční aplikaci .NET WPF, která můžete ověřovat uživatele, bezpečně volat webové rozhraní API pomocí OAuth 2.0 a získat základní informace o uživateli. Pokud jste to ještě neudělali, je teď čas naplnit tenanta několika uživateli. Spusťte aplikaci DirectorySearcher a přihlaste se pomocí jedné z těchto uživatelů. Na základě hlavního názvu uživatele (UPN) vyhledejte další uživatele. Zavřete aplikaci a znovu jej spusťte. Všimněte si, jak uživatelské relace zůstává beze změny. Odhlásit a zase přihlásit jako jiný uživatel.

ADAL umožňuje snadno zahrnout tyto běžné funkce identity do vaší aplikace. Postará o čistý práci za vás, jako je Správa mezipaměti, podporu protokolu OAuth, nabízí ten samý uživatel s přihlášením uživatelského rozhraní, aktualizace vypršení platnosti tokenů a další. Potřebujete znát jen jedno volání rozhraní API – `authContext.AcquireTokenAsync(...)`.

Odkaz, naleznete v tématu je hotová ukázka (bez vašich hodnot nastavení) [na Githubu](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Další postup

Zjistěte, jak k ochraně webového rozhraní API pomocí přístupových tokenů nosiče OAuth 2.0.
> [!div class="nextstepaction"]
> [Zabezpečení webového rozhraní API s využitím Azure AD .NET >>](quickstart-v1-dotnet-webapi.md)
