---
title: 'Rychlý Start: přihlášení uživatelů a volání Microsoft Graph v aplikaci Univerzální platforma Windows | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může aplikace Univerzální platforma Windows (UWP) získat přístupový token a volat rozhraní API chráněné platformou Microsoft identity.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 5b954c5eae9c203efa65dc9dc1883d8e00f3937a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630521"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Rychlý start: Volání rozhraní Microsoft Graph API z aplikace pro Univerzální platformu Windows (UPW)

Tento rychlý Start obsahuje ukázku kódu, která předvádí, jak se aplikace Univerzální platforma Windows (UWP) může přihlašovat uživatelům pomocí osobních účtů nebo pracovních a školních účtů, získat přístupový token a volat rozhraní Microsoft Graph API. (Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.)

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Požadavky
>
> * Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> [!div renderon="docs" class="sxs-lookup"]
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Expresní] [Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://aka.ms/MobileAppReg) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `UWP-App-calling-MsGraph`.
>      - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)**.
>      - Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
> 1. V části **identifikátory URI přesměrování**  |  **navrhované identifikátory URI pro přesměrování pro veřejné klienty (mobilní počítače)** ověřte **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Vyberte **Uložit**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat identifikátor URI přesměrování jako **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-uwp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stažení projektu sady Visual Studio

> [!div renderon="docs"]
> [Stažení projektu sady Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připraven ke spuštění.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Nakonfigurujte si projekt sady Visual Studio
>
> 1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
> 1. Otevřete projekt v sadě Visual Studio. Může se zobrazit výzva k instalaci sady UWP SDK. V takovém případě přijměte.
> 1. Upravte **MainPage.XAML.cs** a nahraďte hodnoty v `ClientId` poli:
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
> Kde:
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
>
> > [!TIP]
> > Pokud chcete zjistit hodnotu *ID aplikace*, přečtěte si část **Přehled** na portálu.

#### <a name="step-4-run-your-application"></a>Krok 4: spuštění aplikace

Pokud chcete vyzkoušet rychlý Start v počítači s Windows:

1. Na panelu nástrojů sady Visual Studio vyberte správnou platformu (pravděpodobně **x64** nebo **x86**, ne ARM). Zjistíte, že se cílové zařízení změní ze *zařízení* na *místní počítač* .
1. Vyberte ladit | **Spustit bez ladění**

## <a name="more-information"></a>Další informace

Tato část obsahuje další informace o tomto rychlém startu.

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny zabezpečení. Tokeny zabezpečení se používají pro přístup k rozhraní API chráněnému platformou Microsoft identity pro vývojáře. MSAL můžete nainstalovat spuštěním následujícího příkazu v *Konzole Správce balíčků* v sadě Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Pak se MSAL inicializuje pomocí následujícího kódu:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

> |Kde: | Popis |
> |---------|---------|
> | `ClientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

MSAL má dvě metody pro získání tokenů v aplikaci pro UWP: `AcquireTokenInteractive` a `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Některé situace vyžadují, aby uživatelé vynutili interakci s koncovým bodem Microsoft Identity Platform prostřednictvím místního okna, aby ověřili své přihlašovací údaje nebo udělili souhlas. Možné příklady:

- První přihlášení uživatelů do aplikace
- Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Když vaše aplikace požaduje přístup k prostředku, musí si uživatel udělit souhlas s
- Když je nutné dvoufaktorové ověřování

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Kde:| Popis |
> |---------|---------|
> | `scopes` | Obsahuje požadované obory, například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Použijte `AcquireTokenSilent` metodu k získání tokenů pro přístup k chráněným prostředkům po počáteční `AcquireTokenInteractive` metodě. Nechcete vyžadovat, aby uživatel ověřoval své přihlašovací údaje pokaždé, když potřebují přístup k prostředku. Většina času, který požadujete získání a obnovení tokenu bez zásahu uživatele

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Kde: | Popis |
> |---------|---------|
> | `scopes` | Obsahuje požadované obory, například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API. |
> | `firstAccount` | Určuje první uživatelský účet v mezipaměti (MSAL podporuje více uživatelů v jedné aplikaci). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz pro UWP – volání rozhraní Graph API](tutorial-v2-windows-uwp.md)
