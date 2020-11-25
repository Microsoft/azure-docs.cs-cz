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
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: ab8942b473ad980da22d451116bea6a759aeb461
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95995110"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Rychlý start: Volání rozhraní Microsoft Graph API z aplikace pro Univerzální platformu Windows (UPW)

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se aplikace Univerzální platforma Windows (UWP) může přihlašovat uživatelům a získat přístupový token pro volání rozhraní API Microsoft Graph. 

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

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
> 1. V **registrování aplikace** zadejte registrační informace aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `UWP-App-calling-MsGraph`.
>      - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)**.
> 1. Vyberte **Registrovat** , chcete-li vytvořit aplikaci, a pak poznamenejte **ID aplikace (klienta)** pro použití v pozdějším kroku.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Vyberte **Přidat platformu**  >  **mobilní a desktopové aplikace**.
> 1. V části **identifikátory URI pro přesměrování** vyberte `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Vyberte **Konfigurovat**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>Krok 1: Konfigurace aplikace
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat identifikátor URI přesměrování jako **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-uwp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-visual-studio-project"></a>Krok 2: stažení projektu sady Visual Studio

> [!div renderon="docs"]
> [Stažení projektu sady Visual Studio](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace a je připraven ke spuštění.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>Krok 3: konfigurace projektu sady Visual Studio
>
> 1. Extrahujte archiv zip do místní složky blízko do kořene jednotky. Například do **C:\Azure-Samples**.
> 1. Otevřete projekt v sadě Visual Studio. Pokud se zobrazí výzva, nainstalujte **Univerzální platforma Windows vývojové** úlohy a všechny součásti sady SDK.
> 1. V *MainPage.XAML.cs* změňte hodnotu `ClientId` proměnné na **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali dříve.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    **ID aplikace (klienta)** najdete v podokně **přehledu** aplikace v Azure Portal (**Azure Active Directory**  >  **Registrace aplikací**  >  *{Registrace aplikace}*).
> 1. Vytvořte a potom vyberte nový testovací certifikát podepsaný svým držitelem pro balíček:
>     1. V **Průzkumník řešení** dvakrát klikněte na soubor *Package. appxmanifest* .
>     1. Vybrat **balení**  >  **zvolit certifikát...**  >  **Vytvořit...**.
>     1. Zadejte heslo a pak vyberte **OK**.
>     1. Vyberte **vybrat ze souboru...** a pak vyberte soubor *Native_UWP_V2_TemporaryKey. pfx* , který jste právě vytvořili, a pak vyberte **OK**.
>     1. Zavřete soubor *Package. appxmanifest* (Pokud se zobrazí výzva k uložení souboru), vyberte **OK** .
>     1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt **Native_UWP_V2** a vyberte možnost **vlastnosti**.
>     1. Vyberte **podepisování** a potom vyberte soubor. pfx, který jste vytvořili v rozevíracím seznamu **Vyberte soubor klíče se silným názvem** .

#### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

Spuštění ukázkové aplikace na místním počítači:

1. Na panelu nástrojů sady Visual Studio vyberte správnou platformu (pravděpodobně **x64** nebo **x86**, ne ARM). Cílové zařízení by se mělo změnit ze *zařízení* na *místní počítač*.
1. Vyberte **Ladit** > **Spustit bez ladění**.
    
    Pokud se zobrazí výzva k tomu, možná budete muset nejprve povolit **vývojářský režim** a pak znovu **Spustit bez ladění** , aby se aplikace spustila.

Jakmile se zobrazí okno aplikace, můžete vybrat tlačítko **rozhraní API pro volání Microsoft Graph** , zadat své přihlašovací údaje a vyjádřit souhlas s oprávněními požadovanými aplikací. V případě úspěchu aplikace zobrazí některé informace o tokenech a data získaná z volání rozhraní Microsoft Graph API.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

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

Hodnota `ClientId` je **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali v Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal.

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

`scopes`Parametr obsahuje požadované obory, například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Použijte `AcquireTokenSilent` metodu k získání tokenů pro přístup k chráněným prostředkům po počáteční `AcquireTokenInteractive` metodě. Nechcete vyžadovat, aby uživatel ověřoval své přihlašovací údaje pokaždé, když potřebují přístup k prostředku. Většina času, který požadujete získání a obnovení tokenu bez zásahu uživatele

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` obsahuje požadované obory, například `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API.
* `firstAccount` Určuje první uživatelský účet v mezipaměti (MSAL podporuje více uživatelů v jedné aplikaci).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz pro UWP – volání rozhraní Graph API](tutorial-v2-windows-uwp.md)
