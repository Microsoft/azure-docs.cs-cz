---
title: Azure AD v2 .NET Core démon | Dokumentace Microsoftu
description: Zjistěte, jak získat přístupový token a volat rozhraní API chráněné službou Azure Active Directory v2.0 koncový bod pomocí identity aplikace vlastní proces .NET Core
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22486bf507d5b40521fceabd7569728c45beae3d
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911757"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Rychlý start: Získání tokenu a volat Microsoft Graph API z konzoly aplikace pomocí identity aplikace

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

V tomto rychlém startu se dozvíte jak psát aplikace .NET Core, který můžete získat přístupový token pomocí aplikace pro vlastní identity a pak volání rozhraní Microsoft Graph API k zobrazení [seznam uživatelů](https://docs.microsoft.com/graph/api/user-list) v adresáři. Tento scénář je vhodný pro situace, kdy bezobslužného, bezobslužné úlohy nebo služby systému windows je potřeba spustit s identitou aplikace místo na identitě uživatele.

![Jak funguje ukázková aplikace vygenerovaná v tomto rychlém startu](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.png)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start vyžaduje [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start

> [!div renderon="docs" class="sxs-lookup"]
>
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
>
> 1. Přejděte na [Azure Portal – Registrace aplikace (Preview)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací (Preview)** > **Nová registrace**.
> 1. V **název** části, zadejte název smysluplné aplikace, která se zobrazí uživatelům aplikace, například `Daemon-console`a pak vyberte **zaregistrovat** k vytvoření aplikace.
> 1. Po registraci, vyberte **certifikáty a tajné kódy** nabídky.
> 1. V části **tajné klíče klienta**vyberte **+ nový tajný kód klienta**. Přiřaďte jí název a vyberte **přidat**. Zkopírujte tajný klíč na bezpečném místě. Potřebujete ji k použití ve vašem kódu.
> 1. Teď vyberte **oprávnění k rozhraní API** nabídce vyberte možnost **+ přidat oprávnění** tlačítka, vyberte **Microsoft Graphu**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V části **uživatele** uzlu, vyberte **User.Read.All**a pak vyberte **přidat oprávnění**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Stažení a konfigurace aplikace pro rychlý start
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure portal
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset vytvořit tajný kód klienta a přidat rozhraní Graph API **User.Read.All** oprávnění k aplikaci.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tyto změny provést pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-windows-desktop/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stáhněte si svůj projekt sady Visual Studio

[Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurace projektu sady Visual Studio

1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
1. Otevřete řešení v sadě Visual Studio – **démon console.sln** (volitelné).
1. Upravit **appsettings.json** a nahraďte hodnoty polí `ClientId`, `Tenant` a `ClientSecret` následujícím kódem:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Vygenerujte nový tajný kód klienta]()
    
    > [!div renderon="docs"]
    >> Kde:
    >> * Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
    >> * `Enter_the_Tenant_Id_Here` -Nahraďte tuto hodnotu **Id Tenanta** nebo **název Tenanta** (například contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here` -Nahraďte tuto hodnotu s tajným klíčem klienta vytvořili v kroku 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Pokud chcete najít hodnoty z **ID aplikace (klient)**, **ID adresáře (tenant)**, přejděte na aplikaci **přehled** stránky na webu Azure Portal. Pokud chcete generovat nový klíč, přejděte na **certifikáty a tajné kódy** stránky.
    
#### <a name="step-4-admin-consent"></a>Krok 4: Souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, zobrazí se *HTTP 403 – Zakázáno* Chyba: `Insufficient privileges to complete the operation`. K tomu dojde, protože všechny *oprávnění jen pro aplikace* vyžaduje souhlas správce, což znamená, že globální správce adresáře musí udělit souhlas pro vaši aplikaci. Vyberte jednu z možností níže podle vaší roli:

##### <a name="global-tenant-administrator"></a>Správcem globálního tenanta

> [!div renderon="docs"]
> Pokud jste správcem globálního tenanta, přejděte na **oprávnění k rozhraní API** stránku registrace aplikace na webu Azure Portal (Preview) a vyberte **udělit souhlas správce pro {název Tenantu}** (kde je {název Tenantu} Název adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, přejděte na **oprávnění k rozhraní API** stránce vyberte **udělit souhlas správce pro Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Přejděte na stránku oprávnění k rozhraní API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardní uživatel tenanta, budete muset požádejte globální správce o udělení souhlasu správce pro vaši aplikaci. Provedete to tak, zadejte následující adresu URL správci:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Kde:
>> * `Enter_the_Tenant_Id_Here` -Nahraďte tuto hodnotu **Id Tenanta** nebo **název Tenanta** (například contoso.microsoft.com)
>> * Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.

> [!NOTE]
> Může se zobrazit chyba *"AADSTS50011: Pro aplikaci není zaregistrován žádný zpáteční adresu "* po udělení souhlasu do aplikace pomocí předchozí adresou URL. To možné tuto aplikaci a adresu URL přesměrování URI - nemají prosím dokončená, chybu ignorujte.

#### <a name="step-5-run-the-application"></a>Krok 5: Spuštění aplikace

Pokud používáte Visual Studio, stiskněte **F5** ke spuštění aplikace, v opačném případě spusťte aplikaci pomocí příkazového řádku nebo konzoly:

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Kde:
> * *{ProjectFolder}*  je složka, ve které jste extrahovali soubor zip. Příklad **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Zobrazí se seznam uživatelů v adresáři služby Azure AD jako výsledek.

> [!IMPORTANT]
> Tato aplikace rychlý start používá tajný kód klienta identifikuje jako důvěrnému klientovi. Protože tajný kód klienta se přidá jako prostého textu do souborů projektu, z bezpečnostních důvodů se doporučuje použití certifikátu místo tajného klíče klienta předtím, než aplikace jako produkční aplikace. Další informace o tom, jak používat certifikát, najdete v části [tyto pokyny](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) v úložišti GitHub pro tuto ukázku.

## <a name="more-information"></a>Další informace

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna, která slouží k přihlašování uživatelů a vyžádání tokenů pro přístup k rozhraní API chráněného službou Microsoft Azure Active Directory (Azure AD). Jak je popsáno, v tomto rychlém startu požádat o tokeny pomocí vlastní identity aplikace namísto delegovaná oprávnění. Tok ověřování použité v tomto případě se označuje jako  *[údajů klienta oauth tok](v2-oauth2-client-creds-grant-flow.md)*. Další informace o tom, jak pomocí MSAL.NET tok přihlašovacích údajů klienta, najdete v tématu [v tomto článku](https://aka.ms/msal-net-client-credentials).

 Spuštěním následujícího příkazu v sadě Visual Studio můžete nainstalovat MSAL.NET **Konzola správce balíčků**:

```powershell
Install-Package Microsoft.Identity.Client
```

Případně pokud nepoužíváte Visual Studio, spustíte následující příkaz pro přidání MSAL do projektu:

```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```csharp
ClientCredential clientCredentials = new ClientCredential(secret: config.ClientSecret);

var app = new ConfidentialClientApplication(
    clientId: config.ClientId, 
    authority: config.Authority, 
    redirectUri: "https://daemon", 
    clientCredential: clientCredentials, 
    userTokenCache: null, 
    appTokenCache: new TokenCache()
);
```

> | Kde: ||
> |---------|---------|
> | `secret` | Vytvoření tajného klíče klienta pro aplikaci na webu Azure Portal. |
> | `clientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `Authority`    | (Volitelné) Koncový bod služby tokenů zabezpečení pro uživatele k ověření. Obvykle https://login.microsoftonline.com/{tenant} pro veřejný cloud, kde je název vašeho tenanta nebo vaše ID tenanta {klient}|
> | `redirectUri`  | Adresa URL, kde jsou uživatelé nasměrovaní po ověření. V tomto případě protože je to konzoly/neinteraktivní aplikace, tento parametr se nepoužívá |
> | `clientCredentials`  | Objekt přihlašovacích údajů klienta, obsahující tajný klíč nebo certifikát |
> | `userTokenCache`  | Instance mezipaměti tokenů pro daného uživatele. V tomto případě protože tato aplikace běží v kontextu aplikace a ne uživatele, je tato hodnota null|
> | `appTokenCache`  | Instance mezipaměť tokenu pro aplikaci|

Další informace najdete v tématu [referenční dokumentaci pro `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.-ctor?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication__ctor_System_String_System_String_System_String_Microsoft_Identity_Client_ClientCredential_Microsoft_Identity_Client_TokenCache_Microsoft_Identity_Client_TokenCache)

### <a name="requesting-tokens"></a>Žádosti o tokeny

Chcete-li požádat o token pomocí identity aplikace, použijte `AcquireTokenForClientAsync` metody:

```csharp
result = await app.AcquireTokenForClientAsync(scopes);
```

> |Kde:| |
> |---------|---------|
> | `scopes` | Obsahuje požadovaný obory. Pro důvěrní klienti by měl použít formát podobný `{Application ID URI}/.default` k označení, že obory žádá jsou ty, které staticky určené v objektu aplikace nastavit na webu Azure Portal (pro Microsoft Graph `{Application ID URI}` odkazuje na `https://graph.microsoft.com`). Pro vlastní webová rozhraní API `{Application ID URI}` je definována v rámci **vystavit rozhraní API** části v registraci aplikace na webu Azure Portal (Preview). |

Další informace najdete v tématu [referenční dokumentaci pro `AcquireTokenForClientAsync`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclientasync?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication_AcquireTokenForClientAsync_System_Collections_Generic_IEnumerable_System_String__)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další postup

Další informace o oprávnění a vyjádření souhlasu:

> [!div class="nextstepaction"]
> [Oprávnění a souhlas](v2-permissions-and-consent.md)

Další informace o toku ověřování pro tento scénář, naleznete v tématu tok přihlašovacích údajů klienta Oauth 2.0:

> [!div class="nextstepaction"]
> [Tok Oauth přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Toky přihlašovacích údajů klienta pomocí MSAL.NET](https://aka.ms/msal-net-client-credentials)