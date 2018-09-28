---
title: Rychlý start pro Univerzální platformu Windows Azure AD v2 | Microsoft Docs
description: Zjistěte, jak může aplikace Universal Windows Platform (XAML) získat přístupový token a volat rozhraní API chráněné koncovým bodem Azure Active Directory v2.0.
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
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970815"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Volání rozhraní Microsoft Graph API z aplikace pro univerzální platformu Windows (UPW)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý start obsahuje ukázku kódu, která předvádí, jak může aplikace univerzální platformy Windows (UPW) přihlásit uživatele v rámci osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Jak funguje ukázková aplikace vygenerovaná v tomto rychlém startu](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrace a stažení
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrace a konfigurace aplikace a vzorek kódu
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Postup při registraci aplikace a přidání informací o registraci zařízení k řešení:
> 1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace.
> 1. Zkontrolujte, že není zaškrtnuté políčko **Guided Setup** (Instalační program s asistencí) a vyberte **Create** (Vytvořit).
> 1. Vyberte **Add Platform** (Přidat platformu), **Native Application** (Nativní aplikace) a pak **Save** (Uložit).

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Aby mohl tento rychlý start fungovat, je nutné pro ukázkový kód přidat adresu URL přesměrování, například **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-uwp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stažení projektu sady Visual Studio

 - [Stažení projektu sady Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky (například **C:\Azure-Samples**)
1. Otevřete projekt v sadě Visual Studio.
1. Upravte **App.Xaml.cs** a nahraďte řádek začínající `private static string ClientId` pomocí:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>Další informace

Podívejte se na přehled tohoto rychlého startu:

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna, která slouží k přihlašování uživatelů a vyžádání tokenů pro přístup k rozhraní API chráněného službou Microsoft Azure Active Directory. Můžete ji nainstalovat tak, že spustíte následující příkaz v *Konzole Správce balíčků* v sadě Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující řádek:

```csharp
using Microsoft.Identity.Client;
```

Potom inicializujte knihovnu MSAL pomocí následujícího řádku:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Kde: ||
> |---------|---------|
> |ClientId | ID aplikace z aplikace zaregistrované na webu *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `AcquireTokenAsync` a `AcquireTokenSilentAsync`:

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

 V některých situacích je nutné, aby uživatelé komunikovali s koncovým bodem Azure Active Directory v2 prostřednictvím automaticky otevíraných oken, kde se ověřují jejich přihlašovací údaje nebo kde udělují souhlas. Příklady:

- Při prvním přihlášení uživatelů k aplikaci
- Může být potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
- Vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
- Je nutné dvoufaktorové ověřování

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Kde:||
> |---------|---------|
> |obory | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API) |

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Nechcete, aby uživatelé ověřovali svoje přihlašovací údaje pokaždé, když potřebují přístup k prostředku – ve většině případů budete chtít pořízení tokenů a obnovení bez zásahu uživatele – `AcquireTokenSilentAsync` je metoda běžně používaná k získání tokenů, které slouží k přístupu k chráněným prostředkům po počáteční metodě `AcquireTokenAsync`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Kde: ||
> |---------|---------|
> |obory | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "api://<Application ID>/access_as_user" }` pro vlastní webová rozhraní API) |
> |accounts.FirstOrDefault() | První uživatel v mezipaměti (MSAL podporuje více uživatelů v jediné aplikaci) |

## <a name="next-steps"></a>Další kroky

Vyzkoušejte kurz pro plochu Windows, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu:

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Naučte se vytvořit aplikaci za použití tohoto rychlého startu

> [!div class="nextstepaction"]
> [Kurz pro UWP – volání rozhraní Graph API](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]