---
title: Microsoft Identity Platform – rychlý Start pro Windows Desktop | Microsoft Docs
description: Přečtěte si, jak může aplikace Windows Desktop .NET (XAML) získat přístupový token a volat rozhraní API chráněné koncovým bodem platformy Microsoft identity.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a85cafce13cffcebbcce3ebf022941ce899fa72b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852750"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Rychlý start: Získání tokenu a volání Microsoft Graph API z desktopové aplikace pro Windows

V tomto rychlém startu se dozvíte, jak napsat desktopovou aplikaci .NET pro Windows (WPF), která podporuje přihlášení osobním, pracovním a školním účtem, jak získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * Express [Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Zásah [Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.
>
> 1. Přejít na nový [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://aka.ms/MobileAppReg) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Win-App-calling-MsGraph`.
>      - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)** .
>      - Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
> 1. Rozbalte část **plocha a zařízení** .  (Pokud není **plocha a zařízení** viditelná, nejdřív klikněte na banner s náhledem, abyste viděli prostředí pro ověřování ve verzi Preview.)
> 1. V části **identifikátor URI pro přesměrování** vyberte **Přidat identifikátor URI**.  Zadejte **urn: IETF: WG: OAuth: 2.0: OOB**.
> 1. Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby mohl tento rychlý start fungovat, je nutné pro ukázkový kód přidat adresu URL odpovědi, například **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-windows-desktop/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stažení projektu sady Visual Studio

[Stažení projektu sady Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)  ([Zobrazit projekt na GitHubu](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/))

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
1. Otevřete projekt v sadě Visual Studio.
1. Upravte soubor **App.Xaml.cs** a nahraďte hodnoty polí `ClientId` a `Tenant` následujícím kódem:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Tento rychlý Start podporuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Kde:
> - Hodnota `Enter_the_Application_Id_here` je **ID aplikace (klienta)** , kterou jste zaregistrovali.
> - Hodnota `Enter_the_Tenant_Info_Here` je nastavená na jednu z následujících možností:
>   - Pokud vaše aplikace podporuje režim **Účty jen v tomto organizačním adresáři**, nahraďte tuto hodnotu za **ID tenanta** nebo **Název tenanta** (například contoso.microsoft.com).
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>   - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři a osobní účty Mircosoft**, nahraďte tuto hodnotu za `common`.
>
> > [!TIP]
> > Hodnoty **ID aplikace (klienta)** , **ID adresáře (tenanta)** a **Podporované typy účtu** najdete na stránce **Přehled** aplikace na webu Azure Portal.

## <a name="more-information"></a>Další informace

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. MSAL můžete nainstalovat spuštěním následujícího příkazu v **Konzole Správce balíčků** v sadě Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Kde: ||
> |---------|---------|
> | `ClientId` | Je **ID aplikace (klienta)** , kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `AcquireTokenInteractive` a `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Některé situace vyžadují, aby uživatelé mohli komunikovat s koncovým bodem Microsoft Identity Platform prostřednictvím místního okna a ověřit své přihlašovací údaje nebo udělit souhlas. Možné příklady:

- Při prvním přihlášení uživatele k aplikaci
- Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Když vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
- Když je nutné dvoufaktorové ověřování

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Kde:||
> |---------|---------|
> | `_scopes` | Obsahuje požadované obory, jako například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Nechcete vyžadovat, aby uživatel ověřoval přihlašovací údaje pokaždé, když potřebuje přístup k prostředku. Ve většině případů budete chtít tokeny pořizovat a obnovovat bez nutnosti zásahu uživatele. Po počáteční metodě `AcquireTokenInteractive` můžete použít metodu `AcquireTokenSilent` a získat tokeny pro přístup k chráněným prostředkům:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Kde: ||
> |---------|---------|
> | `scopes` | Obsahuje požadované obory, jako například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |
> | `firstAccount` | Určuje prvního uživatele v mezipaměti (MSAL podporuje více uživatelů v jediné aplikaci). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další postup

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz volání rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
