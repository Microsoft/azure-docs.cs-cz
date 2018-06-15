---
title: Azure AD .NET Desktop (WPF) Začínáme | Microsoft Docs
description: Jak vytvořit aplikaci .NET Windows Desktop, který se integruje s Azure AD pro přihlašování a volá Azure AD chráněné rozhraní API pomocí metody OAuth.
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
ms.openlocfilehash: 1097d4b1f7d8c4feae500bf46b40e61029781d68
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156258"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Azure AD .NET Desktop (WPF) Začínáme
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pokud vyvíjíte aplikace pracovní plochy, Azure AD je jednoduchá a přímočará pro vás k ověřování uživatelů s účty služby Active Directory. Taky umožňuje vaší aplikaci bezpečně využívat žádné webové rozhraní API, které jsou chráněné službou Azure AD, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Azure AD pro rozhraní .NET nativní klienty, kteří potřebují přístup k chráněným prostředkům, poskytuje knihovna ověřování Active Directory nebo ADAL. Jediným účelem je ADAL v životní je snadno pro aplikaci, kterou chcete získat přístupové tokeny. Do ukazují, jak lze snadno je, zde jsme budete sestavení aplikace seznam úkolů WPF .NET který:

* Získá přístup k tokeny pro volání pomocí Azure AD Graph API [protokol ověřování OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Vyhledá adresář pro uživatele s danou alias.
* Uživatelé přihlásí se.

K vytvoření dokončení funkční aplikaci, musíte:

1. Registrace vaší aplikace s Azure AD.
2. Instalace a konfigurace ADAL.
3. Pomocí ADAL získat tokeny z Azure AD.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) nebo [stažení je hotová ukázka](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Budete také potřebovat klient služby Azure AD, ve kterém můžete vytvořit uživatele a zaregistrovat aplikaci. Pokud ještě nemáte klienta, [zjistěte, jak získat](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registrace aplikace DirectorySearcher
Chcete-li aplikaci, kterou chcete získat tokeny, budete nejprve muset zaregistrovat v klientovi služby Azure AD a udělit mu oprávnění k přístupu k Azure AD Graph API:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na tlačítko na vašem účtu a v části **Directory** vyberte klienta služby Active Directory, kam chcete registrace vaší aplikace.
3. Klikněte na **všechny služby** v navigaci vlevo a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikace** a zvolte **přidat**.
5. Postupujte podle výzev a vytvořte novou **nativní klientská aplikace**.
  * **Název** aplikace popíše aplikaci pro koncové uživatele
  * **Identifikátor Uri pro přesměrování** je kombinace schéma a řetězec, který Azure AD použije k vrácení odpovědi tokenu. Zadejte hodnotu konkrétní k vaší aplikaci, například `http://DirectorySearcher`.
6. Po dokončení registrace AAD přiřadí vaší aplikace, jedinečné ID aplikace Tuto hodnotu budete potřebovat v další části, zkopírujte jej ze stránky aplikace.
7. Z **nastavení** vyberte **požadovaných oprávnění** a zvolte **přidat**. Vyberte **Microsoft Graph** jako rozhraní API a přidejte **čtení dat adresáře** oprávnění v rámci **delegovaná oprávnění**. Tato akce povolí aplikaci zpracovat dotaz rozhraní Graph API pro uživatele.

## <a name="2-install--configure-adal"></a>2. Instalace a konfigurace ADAL
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat ADAL a zadejte kód, týkající se identity. Aby ADAL být schopni komunikovat se službou Azure AD budete muset poskytnout některé informace o registraci vaší aplikace.

* Začněte tím, že přidávání ADAL k DirectorySearcher projektu pomocí konzoly Správce balíčků.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Otevřete v projektu DirectorySearcher `app.config`. Nahraďte hodnoty elementů v `<appSettings>` oddílu tak, aby odrážela hodnoty vstup na portálu Azure. Váš kód bude odkazovat tyto hodnoty vždy, když ho využívá ADAL.
  * `ida:Tenant` Je doména klienta služby Azure AD, např. contoso.onmicrosoft.com
  * `ida:ClientId` Je clientId vaší aplikace, které jste zkopírovali z portálu.
  * `ida:RedirectUri` Je přesměrování url jste zaregistrovali na portálu.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Získat tokeny z AAD pomocí ADAL
Základní princip za ADAL je, že vždy, když aplikace potřebuje přístupový token, jednoduše volá `authContext.AcquireTokenAsync(...)`, a zbývající ADAL. 

* V `DirectorySearcher` projekt, otevřete `MainWindow.xaml.cs` a najděte `MainWindow()` metoda. Prvním krokem je k chybě při inicializaci aplikace `AuthenticationContext` -ADAL je primární třídou. Toto je, kde je předat ADAL souřadnice musí komunikovat s Azure AD a určit, jak pro ukládání do mezipaměti tokenů.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Nyní najít `Search(...)` metoda, která se vyvolá, když uživatel klikne na tlačítko "Vyhledat" v uživatelském rozhraní aplikace. Tato metoda vytváří požadavek GET na Azure AD Graph API k dotazu pro uživatele, jehož UPN začíná zadaný hledaný termín. Pro dotaz na rozhraní Graph API, musíte zahrnout access_token v, ale `Authorization` hlavičky požadavku – to přichází ADAL.

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
* Když vaše aplikace vyžaduje token voláním `AcquireTokenAsync(...)`, ADAL se pokusí o vrací token, aniž by požádal uživatele přihlašovací údaje. Pokud ADAL zjistí, že uživatel musí pro přihlášení k získání tokenu, se zobrazí dialogové okno přihlášení, shromažďuje přihlašovací údaje uživatele a vrací token, po úspěšném ověření. Pokud se nepodařilo vrátit token z jakéhokoli důvodu ADAL, vyvolá výjimku `AdalException`.
* Všimněte si, že `AuthenticationResult` objekt obsahuje `UserInfo` objekt, který můžete použít ke shromažďování informací může být nutné vaší aplikace. V DirectorySearcher `UserInfo` slouží k přizpůsobení uživatelského rozhraní aplikace s id uživatele.
* Když uživatel klikne na tlačítko "Odhlásit", chceme, abyste ověřili, že další volání `AcquireTokenAsync(...)` požádá uživatele k přihlášení. Pomocí knihovny ADAL to je stejně snadná jako vymazání mezipamětí tokenů:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Ale pokud uživatel není klikněte na tlačítko "Odhlásit", budete chtít zachovat relace uživatele pro příští, na které poběží DirectorySearcher. Při spuštění aplikace můžete zkontrolovat mezipamětí tokenů na ADAL pro existující token a uživatelského rozhraní se aktualizují odpovídajícím způsobem. V `CheckForCachedToken()` metoda, proveďte jiné volání `AcquireTokenAsync(...)`, tentokrát předávání v `PromptBehavior.Never` parametr. `PromptBehavior.Never` ADAL bude informovat, že by neměl být uživatel vyzván pro přihlašování a ADAL místo toho by měla vyvolána výjimka, pokud nelze vrátit token.

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

Blahopřejeme! Teď máte funkční aplikaci WPF rozhraní .NET, který má schopnost ověřovat uživatele, bezpečně volání webového rozhraní API pomocí OAuth 2.0 a získat základní informace o uživateli. Pokud jste to ještě neudělali, nyní je čas k naplnění vašeho klienta s některými uživateli. Spuštění aplikace DirectorySearcher a přihlaste se pomocí jeden z těchto uživatelů. Hledání jiných uživatelů podle jejich UPN. Zavřete aplikaci a znovu jej spusťte. Všimněte si, jak uživatelské relace zůstává beze změn. Odhlásit se a přihlaste se zpět jako jiný uživatel.

ADAL usnadňuje všechny tyto běžné funkce identity začlenit do vaší aplikace. Postará všechny dirty činností, které pro vás - Správa mezipaměti, podpora protokolu OAuth, představuje uživatele s přihlašovacími údaji uživatelského rozhraní, aktualizace vypršení platnosti tokenů a další. Všechny skutečně potřebujete vědět, je jednoho volání rozhraní API `authContext.AcquireTokenAsync(...)`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) zajišťuje [zde](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Nyní se můžete přesunout dalších scénářů. Můžete se pokusit:

[Zabezpečení rozhraní .NET webového rozhraní API pomocí Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

