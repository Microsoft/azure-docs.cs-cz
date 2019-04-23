---
title: Rychlý start Microsoft identity platform UPW pro Windows | Azure
description: Zjistěte, jak získat přístupový token a volat rozhraní API chráněné službou Microsoft identity platform koncového bodu aplikace Universal Windows Platform (XAML).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d2e9aa5e5e805b302763f5417110cdd078eb3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298675"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Rychlý start: Volání rozhraní Microsoft Graph API z aplikace pro univerzální platformu Windows (UPW)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý Start obsahuje ukázku kódu, který ukazuje, jak aplikace univerzální platformy Windows (UPW) může přihlásit uživatele pomocí osobních účtů nebo pracovní a školní účty, získat přístupový token a volání rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> [!div renderon="docs" class="sxs-lookup"]
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) podokně.
> 1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Pokud chcete zaregistrovat aplikaci a přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `UWP-App-calling-MsGraph`.
>      - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)**.
>      - Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
> 1. V části **Adresy URL pro přesměrování** vyhledejte část **Navrhované identifikátory URI pro přesměrování pro veřejné klienty (mobilní, desktopové)** a vyberte **urn:ietf:wg:oauth:2.0:oob**.
> 1. Vyberte **Uložit**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Aby vzorový kód pro tento rychlý start fungoval, musíte jako identifikátor URI pro přesměrování přidat **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-uwp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stáhněte si svůj projekt sady Visual Studio

 - [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
1. Otevřete projekt v sadě Visual Studio. Vám může zobrazit výzva k instalaci UWP SDK. V takovém případě přijměte.
1. Upravit **MainPage.Xaml.cs** a nahraďte hodnoty `ClientId` pole:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```

> [!div renderon="docs"]
> Kde:
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
>
> > [!TIP]
> > Najít hodnotu *ID aplikace*, přejděte **přehled** části portálu

#### <a name="step-4-run-your-application"></a>Krok 4: Spusťte aplikaci

Pokud chcete vyzkoušet tento rychlý start na svém počítači s Windows:

1. Na panelu nástrojů sady Visual Studio zvolte platformu vpravo (pravděpodobně **x64** nebo **x86**, ne ARM).
   > Podívejte se, že cílové zařízení se změní z *zařízení* k *místního počítače*
1. Vyberte ladění | **Spustit bez ladění**

## <a name="more-information"></a>Další informace

Tato část obsahuje další informace o tomto rychlém startu.

### <a name="msalnet"></a>MSAL.NET

Knihovna MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna používaná k přihlášení uživatelů a požádat o tokeny zabezpečení. Tokeny zabezpečení používané pro přístup k rozhraní API chráněné službou Microsoft Identity platform pro vývojáře. MSAL můžete nainstalovat spuštěním následujícího příkazu v *Konzole Správce balíčků* v sadě Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Potom MSAL je inicializován pomocí následujícího kódu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Kde: ||
> |---------|---------|
> | `ClientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL má dvě metody pro získávání tokenů v aplikaci UWP: `AcquireTokenInteractive` a `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Některé situace vyžadují vynucení uživatelům interakci s Microsoft identity platform koncového bodu prostřednictvím automaticky otevíraném okně buď ověření přihlašovacích údajů nebo udělit souhlas. Možné příklady:

- První uživatelé přihlašují k aplikaci
- Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Pokud vaše aplikace požaduje přístup k prostředku, který uživatel musí vyjádřit souhlas
- Když je nutné dvoufaktorové ověřování

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Kde:||
> |---------|---------|
> | `scopes` | Obsahuje požadované obory, jako například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Použití `AcquireTokenSilent` metodu k získání tokenů pro přístup k chráněným prostředkům po počáteční `AcquireTokenAsync` metody. Nechcete vyžaduje, aby uživatel k ověření přihlašovacích údajů pokaždé, když potřebují přístup k prostředku. Ve většině případů budete chtít token pořízení a obnovení bez nutnosti zásahu uživatele

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Kde: ||
> |---------|---------|
> | `scopes` | Obsahuje požadované obory, jako například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |
> | `firstAccount` | Určuje první účet uživatele v mezipaměti (MSAL podporuje více uživatelů v jediné aplikaci) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další postup

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz pro UWP – volání rozhraní Graph API](tutorial-v2-windows-uwp.md)
