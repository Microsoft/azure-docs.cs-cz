---
title: Rychlý start pro desktopovou aplikaci Windows Azure AD v2 | Microsoft Docs
description: Zjistěte, jak může aplikace plochy Windows .NET (XAML) získat přístupový token a volat rozhraní API chráněné koncovým bodem Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 28c5f025b59b4adbb33a59edd225a839e11dad97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965088"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Rychlý start: Získání tokenu a volání rozhraní API Microsoft Graph z desktopové aplikace Windows

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

V tomto rychlém startu se dozvíte, jak se přihlásit do desktopové aplikace pro Windows .NET (WPF) s osobním, pracovním a školním účtem, jak získat přístupový token a volat rozhraní Microsoft Graph API.

![Jak funguje ukázková aplikace vygenerovaná v tomto rychlém startu](media/quickstart-v2-windows-desktop/windesktop-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrace a stažení
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrace a konfigurace aplikace a vzorek kódu
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Postup při registraci aplikace a přidání informací o registraci zařízení k řešení:
> 1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace.
> 1. Zkontrolujte, že není zaškrtnuté políčko **Guided Setup** (Instalační program s asistencí) a vyberte **Create** (Vytvořit).
> 1. Vyberte **Add Platform** (Přidat platformu), **Native Application** (Nativní aplikace) a pak **Save** (Uložit).

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Aby mohl tento rychlý start fungovat, je nutné pro ukázkový kód přidat adresu URL odpovědi, například **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-windows-desktop/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stažení projektu sady Visual Studio

[Stažení projektu sady Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky (například **C:\Azure-Samples**)
1. Otevřete projekt v sadě Visual Studio.
1. Upravte **App.Xaml.cs** a nahraďte řádek, který má na začátku `private static string ClientId`, identifikátorem aplikace z právě zaregistrované aplikace:

```csharp
private static string ClientId = "Enter_the_Application_Id_here";
```

## <a name="more-information"></a>Další informace

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna, která slouží k přihlašování uživatelů a vyžádání tokenů pro přístup k rozhraní API chráněného službou Microsoft Azure Active Directory (Azure AD). Můžete ji nainstalovat tak, že spustíte následující příkaz v **Konzole Správce balíčků** v sadě Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Kde: ||
> |---------|---------|
> | `ClientId` | ID aplikace z aplikace zaregistrované na webu *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `AcquireTokenAsync` a `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

V některých situacích je nutné, aby uživatelé komunikovali s koncovým bodem Azure AD v2.0 prostřednictvím automaticky otevíraných oken, kde se ověřují jejich přihlašovací údaje nebo kde udělují souhlas. Možné příklady:

- Při prvním přihlášení k aplikaci
- Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Když vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
- Když je nutné dvoufaktorové ověřování

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
```

> |Kde:||
> |---------|---------|
> | `_scopes` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API) |

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Nechcete vyžadovat, aby uživatel ověřoval přihlašovací údaje pokaždé, když potřebuje přístup k prostředku. Ve většině případů budete chtít tokeny pořizovat a obnovovat bez nutnosti zásahu uživatele. Po počáteční metodě `AcquireTokenAsync` můžete použít metodu `AcquireTokenSilentAsync` a získat tokeny pro přístup k chráněným prostředkům:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Kde: ||
> |---------|---------|
> |obory | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API) |
> |accounts.FirstOrDefault() | První uživatel v mezipaměti (MSAL podporuje více uživatelů v jediné aplikaci) |

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Naučte se vytvořit aplikaci za použití tohoto rychlého startu

> [!div class="nextstepaction"]
> [Kurz volání rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
