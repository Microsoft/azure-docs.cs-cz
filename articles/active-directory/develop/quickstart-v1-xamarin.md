---
title: Začínáme se službou Azure AD Xamarin | Dokumentace Microsoftu
description: Vytváření aplikací v Xamarinu, které se integrují s Azure AD pro přihlášení do Azure AD chráněné rozhraní API volat pomocí metody OAuth.
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
ms.openlocfilehash: 3479aa8dd319c81f320b6c7ead086c266454acc6
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579591"
---
# <a name="azure-ad-xamarin-getting-started"></a>Začínáme se službou Azure AD Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

S využitím kódu Xamarin můžete zapsat mobilní aplikace v jazyce C#, který můžete spustit v iOS, Android a Windows (mobilních zařízení a počítačů). Pokud už vytváříte aplikace pomocí Xamarinu, Azure Active Directory (Azure AD) usnadňuje ověřování uživatelů pomocí jejich účtů služby Azure AD. Aplikace může zároveň se bezpečně využívat jakékoli webové rozhraní API, který je chráněný službou Azure AD, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Azure AD poskytuje pro aplikace Xamarin, které potřebují přístup k chráněným prostředkům, Active Directory Authentication Library (ADAL). Jediným účelem ADAL je k tomu, aby pro aplikace k získání přístupových tokenů. Abychom si předvedli, jak je snadné, tento článek popisuje, jak vytvářet DirectorySearcher aplikace, které:

* Spusťte v iOS, Android, Windows Desktop, Windows Phone a Windows Store.
* Pomocí jednoho přenosnou knihovnu tříd (PCL) k ověřování uživatelů a získat tokeny pro Azure AD Graph API.
* Vyhledejte adresář pro uživatele s danou hlavní název uživatele.

## <a name="before-you-get-started"></a>Než začnete
* Stáhněte si [kostru projektu v](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), nebo si stáhněte [úplnou vzorovou](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Každý soubor ke stažení je řešení sady Visual Studio 2013.
* Budete také potřebovat tenanta služby Azure AD, ve kterém chcete vytvořit uživatele a registraci aplikace. Pokud ještě nemáte tenanta, [zjistěte, jak získat](quickstart-create-new-tenant.md).

Až budete připravení, postupujte podle pokynů v další čtyři části.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Krok 1: Nastavení vývojového prostředí Xamarin
V tomto kurzu zahrnuje projekty pro iOS, Android a Windows, a proto potřebujete Visual Studio a Xamarin. Vytvořte nezbytné prostředí, dokončete proces v [nastavení a instalaci sady Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) na webové stránce MSDN. Pokyny zahrnují materiálů, které můžete zkontrolovat další informace o softwaru Xamarin, zatímco čekáte pro instalace dokončit.

Po dokončení instalace, otevřete řešení v sadě Visual Studio. Tam najdete šest projekty: pět projekty specifické pro platformu a jeden PCL DirectorySearcher.cs, která se bude sdílet mezi všemi platformami.

## <a name="step-2-register-the-directorysearcher-app"></a>Krok 2: Registrace aplikace DirectorySearcher
K tomu, aby aplikace k získání tokenů, musíte nejprve zaregistrovat ve vašem tenantovi Azure AD a udělit mu oprávnění pro přístup k Azure AD Graph API. Zde je uveden postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na svůj účet. Potom v části **Directory** vyberte tenanta Active Directory, ve které chcete zaregistrovat aplikaci.
3. Klikněte na tlačítko **všechny služby** v levém podokně a pak vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikací**a pak vyberte **přidat**.
5. Chcete-li vytvořit nový **nativní klientská aplikace**, postupujte podle zobrazených výzev.
  * **Název** popis aplikace pro uživatele.
  * **Identifikátor URI pro přesměrování** je kombinace schématu a řetězec, který používá Azure AD k vracení odpovědí na tokeny. Zadejte hodnotu (například http://DirectorySearcher).
6. Po dokončení registrace Azure AD přiřadí aplikace jedinečné ID. Zkopírujte hodnotu z **aplikace** kartu, protože ho budete potřebovat později.
7. Na **nastavení** stránce **požadovaná oprávnění**a pak vyberte **přidat**.
8. Vyberte **Microsoft Graphu** jako rozhraní API. V části **delegovaná oprávnění**, přidejte **čtení dat adresáře** oprávnění. Tato akce umožní aplikaci k dotazování rozhraní Graph API pro uživatele.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalace a konfigurace ADAL
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat knihovnu ADAL a napsat kód souvisejícím s identitou. Pokud chcete povolit ADAL ke komunikaci s Azure AD, jí některé informace o registraci aplikace.

1. Přidáte ADAL do projektu DirectorySearcher pomocí konzole Správce balíčků.

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

    Všimněte si, že dva odkazy na knihovnu se přidají do každého projektu: PCL část ADAL a uspořádané specifické pro platformu.
2. V projektu DirectorySearcherLib otevřete DirectorySearcher.cs.
3. Hodnoty členů třídy nahraďte hodnotami, které jste zadali na webu Azure Portal. Váš kód odkazuje na tyto hodnoty pokaždé, když se používá ADAL.

  * *Tenanta* je doména tenanta Azure AD (například contoso.onmicrosoft.com).
  * *ClientId* je ID klienta aplikace, které jste zkopírovali z portálu.
  * *ReturnUri* je identifikátor URI, který jste zadali v portálu pro přesměrování (například http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Použití ADAL k získání tokenů z Azure AD
Téměř všechny aplikace logiky ověřování spočívá v `DirectorySearcher.SearchByAlias(...)`. Vše, co je potřeba v projektech specifické pro platformu je předat kontextový parametr `DirectorySearcher` PCL.

1. Otevřete DirectorySearcher.cs a přidejte nový parametr pro `SearchByAlias(...)` metody. `IPlatformParameters` je kontextové parametr, který zapouzdřuje specifické pro platformu objekty, které je potřeba provádět ověřování ADAL.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializovat `AuthenticationContext`, což je primární třída ADAL. Tato akce předá ADAL souřadnice potřebuje ke komunikaci s Azure AD.
3. Volání `AcquireTokenAsync(...)`, která přijímá `IPlatformParameters` objektů a vyvolá tok ověření, že je potřeba vrátit token do aplikace.

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

    `AcquireTokenAsync(...)` Nejprve se pokusí vrátit token pro požadovaný prostředek (rozhraní Graph API v tomto případě) bez vyzvání uživatele k zadání přihlašovacích údajů (prostřednictvím ukládání do mezipaměti nebo aktualizaci staré tokeny). Podle potřeby zobrazuje uživatele, kteří na přihlašovací stránku Azure AD před získávání požadovaný token.
4. Připojit přístupového tokenu pro požadavek na rozhraní Graph API **autorizace** hlavičky:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

To je všechno pro `DirectorySearcher` PCL a aplikaci prvku souvisejícím s identitou kódu. Už jen zbývá k volání `SearchByAlias(...)` metoda v zobrazení pro každou platformu a v případě potřeby přidejte kód pro zpracování správně životní cyklus uživatelského rozhraní.

### <a name="android"></a>Android
1. V MainActivity.cs, přidejte volání do `SearchByAlias(...)` v seznamu klikněte na tlačítko obslužné rutiny:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Přepsat `OnActivityResult` životního cyklu metoda předávat ověřování přesměruje zpátky na odpovídající metodu. ADAL poskytuje pomocné metody pro tuto v Androidu:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Plocha Windows
V MainWindow.xaml.cs, proveďte volání `SearchByAlias(...)` předáním `WindowInteropHelper` v desktopu `PlatformParameters` objektu:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
V DirSearchClient_iOSViewController.cs iOS `PlatformParameters` objektu používá odkaz na kontroler zobrazení:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Univerzální pro Windows
Ve Windows Universal otevřete MainPage.xaml.cs a pak implementovat `Search` metody. Tato metoda používá pomocnou metodu ve sdíleném projektu k aktualizaci uživatelského rozhraní podle potřeby.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Kam dál
Nyní máte funkční aplikaci Xamarin, která můžete ověřovat uživatele a bezpečně volat webové rozhraní API s použitím OAuth 2.0 na pět různých platformách.

Pokud ještě již předvyplněny tenanta s uživateli, teď je čas Uděláte to tak.

1. Spusťte aplikaci DirectorySearcher a pak se přihlaste pomocí jednoho z uživatelů.
2. Hledání jiných uživatelů podle jejich hlavní název uživatele.

ADAL umožňuje snadno začlenit do aplikace běžné funkce identity. To se postará o všechnu práci čistý, jako je například Správa mezipaměti, podporu protokolu OAuth, nabízí ten samý uživatel s přihlášením uživatelského rozhraní, a aktualizuje vypršení platnosti tokenů. Je potřeba vědět pouze jediného volání rozhraní API, `authContext.AcquireToken*(…)`.

Odkaz, stáhněte si [úplnou vzorovou](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (bez vašich hodnot nastavení).

Nyní se můžete přesunout další identity scénářů. Zkuste například [zabezpečit webové rozhraní API .NET pomocí Azure AD](quickstart-v1-dotnet-webapi.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
