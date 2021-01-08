---
title: 'Rychlý Start: získání tokenu & volání Microsoft Graph v konzolové aplikaci | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může ukázková aplikace .NET Core používat tok přihlašovacích údajů klienta k získání tokenu a volání Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 476261181ca4a8f32bb307a13aa6faa2f1ef9d26
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017387"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Rychlý Start: získání tokenu a volání Microsoft Graph API pomocí identity konzolové aplikace

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak může Konzolová aplikace .NET Core získat přístupový token pro volání rozhraní Microsoft Graph API a zobrazit [seznam uživatelů](/graph/api/user-list) v adresáři. Ukázka kódu také ukazuje, jak může úloha nebo služba systému Windows běžet pomocí identity aplikace namísto identity uživatele. 

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start vyžaduje [.NET Core 3,1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start

> [!div renderon="docs" class="sxs-lookup"]
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1 níže) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `Daemon-console` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
> 1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**, zadejte název a pak vyberte **Přidat**. Poznamenejte si tajnou hodnotu v bezpečném umístění pro použití v pozdějším kroku.
> 1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**. Vyberte **Microsoft Graph**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V části **uživatelský** uzel vyberte **uživatel. číst. vše** a pak vyberte **Přidat oprávnění**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Stažení a konfigurace aplikace pro rychlý Start
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné vytvořit tajný klíč klienta a přidat **uživatele Graph API. číst. všechna** oprávnění aplikace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-netcore-daemon/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-visual-studio-project"></a>Krok 2: Stažení projektu sady Visual Studio

> [!div renderon="docs"]
> [Stažení projektu sady Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Poskytnutý projekt lze spustit buď v aplikaci Visual Studio, nebo v Visual Studio pro Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Nakonfigurujte si projekt sady Visual Studio
>
> 1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
> 1. Otevřete řešení v aplikaci Visual Studio – **1-Call-MSGraph\daemon-Console.sln** (volitelné).
> 1. Upravte **appsettings.js** a nahraďte hodnoty polí `ClientId` `Tenant` a `ClientSecret` následujícím způsobem:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Kde:
>   - Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
>   - `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>   - `Enter_the_Client_Secret_Here` – Nahraďte tuto hodnotu tajným klíčem klienta vytvořeným v kroku 1.

> [!div renderon="docs"]
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)**, navštivte stránku **Přehled** aplikace v Azure Portal. Pokud chcete vygenerovat nový klíč, otevřete stránku **certifikáty & tajných** kódů.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte chybu *HTTP 403 – zakázáno* : `Insufficient privileges to complete the operation` . K tomu dochází, protože všechna *oprávnění jenom pro aplikace* vyžadují souhlas správce, což znamená, že globální správce vašeho adresáře musí udělit souhlas vaší aplikaci. V závislosti na vaší roli vyberte jednu z následujících možností:

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste globální správce klienta, na webu Azure Portal přejděte na **podnikové aplikace** > vyberte registraci vaší aplikace > v části zabezpečení v levém navigačním podokně vyberte **oprávnění** . Vyberte velké tlačítko s popiskem **udělení souhlasu správce pro {název tenanta}** (kde {název tenanta} je název vašeho adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, přejít na stránku **oprávnění rozhraní API** , vyberte **udělit souhlas správce pro Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Přejít na stránku oprávnění API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardní uživatel vašeho tenanta, musíte požádat globálního správce o udělení souhlasu správce vaší aplikace. Pokud to chcete provést, poskytněte správci následující adresu URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Kde:
>> * `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>> * Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.

> [!NOTE]
> Může se zobrazit chyba "AADSTS50011: po udělení souhlasu aplikace pomocí předchozí adresy URL *není zaregistrována žádná odpověď pro aplikaci"* . K tomu dochází, protože tato aplikace a adresa URL nemají identifikátor URI přesměrování – ignorujte prosím tuto chybu.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5: spuštění aplikace

Pokud používáte aplikaci Visual Studio nebo Visual Studio pro Mac, stiskněte klávesu **F5** ke spuštění aplikace, jinak spusťte aplikaci prostřednictvím příkazového řádku, konzoly nebo terminálu:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Kde:
> * *{ProjectFolder}* je složka, do které jste extrahovali soubor zip. Příklad **C:\Azure-Samples\active-Directory-dotnetcore-daemon-v2**

Jako výsledek by se měl zobrazit seznam uživatelů v adresáři služby Azure AD.

> [!IMPORTANT]
> Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se místo toho použít certifikát namísto tajného klíče klienta před tím, než aplikaci vyberou jako produkční aplikaci. Další informace o použití certifikátu najdete v [těchto pokynech](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) v úložišti GitHub pro tuto ukázku.

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. Jak je popsáno, tento rychlý Start žádá o tokeny pomocí vlastní identity aplikace namísto delegovaných oprávnění. Tok ověřování použitý v tomto případě se označuje jako *[tok OAuth přihlašovacími údaji klienta](v2-oauth2-client-creds-grant-flow.md)*. Další informace o tom, jak používat MSAL.NET s tokem přihlašovacích údajů klienta, najdete v [tomto článku](https://aka.ms/msal-net-client-credentials).

 MSAL.NET můžete nainstalovat spuštěním následujícího příkazu v **konzole správce balíčků** sady Visual Studio:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Kde: | Popis |
> |---------|---------|
> | `config.ClientSecret` | Vytvoří se tajný klíč klienta pro aplikaci na webu Azure Portal. |
> | `config.ClientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `config.Authority`    | Volitelné Koncový bod služby STS, který se má ověřit pro uživatele. Obvykle `https://login.microsoftonline.com/{tenant}` pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta.|

Další informace najdete v [referenční dokumentaci pro `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Žádosti o tokeny

K vyžádání tokenu pomocí identity aplikace použijte `AcquireTokenForClient` metodu:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Kde:| Popis |
> |---------|---------|
> | `scopes` | Obsahuje požadované obory. U důvěrných klientů by se měla použít formát podobný tomuto jako `{Application ID URI}/.default` k označení toho, že požadované obory jsou staticky definované v sadě objektů aplikace na webu Azure Portal (pro Microsoft Graph, `{Application ID URI}` které odkazují na `https://graph.microsoft.com` ). Pro vlastní webová rozhraní API `{Application ID URI}` se definuje v části **vystavení rozhraní API** v registraci aplikace na webu Azure Portal (Preview). |

Další informace najdete v [referenční dokumentaci pro `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o aplikacích démona najdete v přehledu scénářů:

> [!div class="nextstepaction"]
> [Aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md)
