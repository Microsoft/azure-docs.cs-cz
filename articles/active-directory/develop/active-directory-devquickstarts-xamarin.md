---
title: Začínáme se službou Azure AD Xamarin | Microsoft Docs
description: Sestavení Xamarin aplikace, které integrují s Azure AD pro přihlášení a volání Azure AD chráněné rozhraní API pomocí metody OAuth.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ac04cddc00bf76bb366a249a5a2ec4c56d5212c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156683"
---
# <a name="azure-ad-xamarin-getting-started"></a>Začínáme se službou Azure AD Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

S Xamarinem může zapisovat mobilní aplikace v jazyce C#, můžete spustit na iOS, Android a Windows (mobilních zařízení a počítačů). Pokud vytváříte aplikace pomocí Xamarinu, Azure Active Directory (Azure AD) usnadňuje ověřování uživatelů s účty služby Azure AD. Aplikace může také bezpečně využívat žádné webové rozhraní API, který je chráněný službou Azure AD, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Pro aplikace Xamarin, které potřebují přístup k chráněným prostředkům Azure AD poskytuje službě Active Directory Authentication Library (ADAL). Jediný účel ADAL je snadno a získat tokeny přístupu aplikace. K předvedení, jak je snadné, tento článek ukazuje, jak vytvářet aplikace DirectorySearcher který:

* Spusťte na iOS, Android, Windows Desktop, Windows Phone a Windows Store.
* Použití jedné přenosných tříd knihovny (PCL) k ověření uživatelů a získat tokeny pro Azure AD Graph API.
* Vyhledejte adresář pro uživatele s danou UPN.

## <a name="before-you-get-started"></a>Než začnete
* Stažení [kostru projektu](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), nebo stáhnout [hotová ukázka](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Každého stažení je řešením sady Visual Studio 2013.
* Musíte taky klient služby Azure AD, ve kterém se má vytvořit uživatele a registraci aplikace. Pokud ještě nemáte klienta, [zjistěte, jak získat](active-directory-howto-tenant.md).

Až budete připravení, postupujte podle pokynů v další čtyři části.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Krok 1: Nastavení vývojového prostředí Xamarin
Tento kurz zahrnuje projekty pro iOS, Android a Windows, a proto musíte Visual Studio a Xamarin. Vytvořte potřebné prostředí, dokončete proces v [nastavit až a nainstalujte Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) na webu MSDN. Pokyny zahrnují materiál, který najdete další informace o Xamarin, zatímco čekáte pro instalace dokončit.

Po dokončení instalace, otevřete řešení v sadě Visual Studio. Zde najdete šesti projekty: pět projekty specifické pro platformu a jeden PCL DirectorySearcher.cs, které budou sdílet všechny platformy.

## <a name="step-2-register-the-directorysearcher-app"></a>Krok 2: Registraci DirectorySearcher aplikace
Pokud chcete povolit aplikaci získat tokeny, musíte nejprve zaregistrovat v klientovi služby Azure AD a udělit mu oprávnění k přístupu k Azure AD Graph API. Zde je uveden postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na váš účet. Potom v části **Directory** vyberte klienta služby Active Directory, ve které chcete zaregistrovat aplikaci.
3. Klikněte na tlačítko **všechny služby** v levém podokně a potom vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikace**a potom vyberte **přidat**.
5. Chcete-li vytvořit novou **nativní klientská aplikace**, postupujte podle pokynů.
  * **Název** popis aplikace pro uživatele.
  * **Identifikátor URI pro přesměrování** je kombinace schématu a řetězec, Azure AD se používá k vrácení odpovědi tokenu. Zadejte hodnotu (například http://DirectorySearcher).
6. Po dokončení registrace Azure AD přiřadí aplikace ID jedinečný aplikace. Zkopírujte hodnotu z **aplikace** kartě, protože ho budete potřebovat později.
7. Na **nastavení** vyberte **požadovaných oprávnění**a potom vyberte **přidat**.
8. Vyberte **Microsoft Graph** jako rozhraní API. V části **delegovaná oprávnění**, přidejte **čtení dat adresáře** oprávnění. Tato akce umožní aplikaci dotaz rozhraní Graph API pro uživatele.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalace a konfigurace ADAL
Nyní když máte aplikaci ve službě Azure AD, můžete nainstalovat ADAL a zadejte kód, týkající se identity. Pokud chcete povolit ADAL ke komunikaci s Azure AD, jí některé informace o registraci aplikace.

1. Přidání ADAL do projektu DirectorySearcher pomocí konzoly Správce balíčků.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Všimněte si, že dva odkazy knihovny jsou přidány do jednotlivých projektů: část PCL ADAL a část specifické pro platformu.
2. V projektu DirectorySearcherLib otevřete DirectorySearcher.cs.
3. Nahraďte hodnoty členů třídy hodnoty, které jste zadali v portálu Azure. Váš kód odkazoval na tyto hodnoty vždy, když ho využívá ADAL.

  * *Klienta* je doména klienta služby Azure AD (například contoso.onmicrosoft.com).
  * *ClientId* je ID klienta aplikace, které jste zkopírovali z portálu.
  * *ReturnUri* je identifikátor URI, který jste zadali v portálu přesměrování (například http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Použití ADAL získat tokeny z Azure AD
Téměř všechny aplikace logiky ověřování spočívá v `DirectorySearcher.SearchByAlias(...)`. Všechny, které je nezbytné v projektech specifických pro platformy je předat kontextové parametr, který se `DirectorySearcher` PCL.

1. Otevřete DirectorySearcher.cs a pak přidejte nový parametr, který se `SearchByAlias(...)` metoda. `IPlatformParameters` je kontextová parametr, který zapouzdřuje specifické pro platformu objekty, které potřebuje provést ověřování ADAL.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializace `AuthenticationContext`, což je primární třídou adal. Tato akce předá ADAL souřadnice ke komunikaci s Azure AD.
3. Volání `AcquireTokenAsync(...)`, který přijímá `IPlatformParameters` objektu a vyvolá tok ověřování, které jsou nutné k návratu token do aplikace.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` Nejprve se pokusí vrátit token pro požadovaný prostředek (rozhraní Graph API v tomto případě) bez vyzvání uživatele k zadání přihlašovacích údajů (prostřednictvím ukládání do mezipaměti nebo obnovení původního tokeny). Podle potřeby zobrazuje uživatele, kteří na přihlašovací stránku služby Azure AD před získávání požadovaný token.
4. Připojit k rozhraní Graph API požadavku v tokenu přístupu **autorizace** hlavičky:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

To je všechno pro `DirectorySearcher` PCL a aplikace je spojené s kódem. Zbývá k volání `SearchByAlias(...)` metoda v zobrazení pro každou platformu a v případě potřeby přidejte kód pro zpracování správně životní cyklus uživatelského rozhraní.

### <a name="android"></a>Android
1. V MainActivity.cs, že přidáte volání `SearchByAlias(...)` v tlačítko klikněte na obslužnou rutinu:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Přepsání `OnActivityResult` životního cyklu metoda předávat žádné ověřování přesměruje zpět na odpovídající metodu. ADAL poskytuje Pomocná metoda pro to v Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows Desktop
V MainWindow.xaml.cs, zkontrolujte volání `SearchByAlias(...)` předáním `WindowInteropHelper` na ploše Desktop `PlatformParameters` objektu:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
V DirSearchClient_iOSViewController.cs, iOS `PlatformParameters` objekt trvá odkaz na řadiče zobrazení:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Univerzální pro Windows
Otevřete MainPage.xaml.cs v univerzální pro Windows a potom implementovat `Search` metoda. Tato metoda používá metodu helper v projektu sdíleného aktualizace uživatelského rozhraní podle potřeby.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Kam dál
Teď máte funkční aplikaci Xamarin, můžete ověřovat uživatele a bezpečně volat webové rozhraní API pomocí standardu OAuth 2.0 napříč pět různých platformách.

Pokud již nejsou naplněny vašeho klienta s uživateli, nyní je čas Uděláte to tak.

1. Spuštění aplikace DirectorySearcher a pak se přihlaste pomocí jednoho uživatele.
2. Hledání jiných uživatelů podle jejich UPN.

ADAL usnadňuje začlenit běžné funkce identity do aplikace. Se postará všechnu práci dirty, jako je například Správa mezipaměti podpora protokolu OAuth, představuje uživatele s přihlašovacími údaji uživatelského rozhraní, a aktualizovat platnost tokenů. Je nutné znát jenom jednoho volání rozhraní API, `authContext.AcquireToken*(…)`.

Odkaz, stáhněte si [hotová ukázka](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (bez vašich hodnot nastavení).

Nyní se můžete přesunout další identity scénářů. Zkuste například [zabezpečení webového rozhraní API .NET s Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
