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
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022921"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Rychlý Start: získání tokenu a volání rozhraní Microsoft Graph API pomocí identity konzolové aplikace

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak může Konzolová aplikace .NET Core získat přístupový token pro volání rozhraní Microsoft Graph API a zobrazit [seznam uživatelů](/graph/api/user-list) v adresáři. Ukázka kódu také ukazuje, jak může úloha nebo služba systému Windows běžet pomocí identity aplikace namísto identity uživatele. Ukázková Konzolová aplikace v tomto rychlém startu je také aplikací démona, takže se jedná o důvěrnou klientskou aplikaci.

> [!div renderon="docs"]
> Následující diagram ukazuje, jak ukázková aplikace funguje:
>
> ![Diagram, který ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start vyžaduje [.NET core 3,1](https://www.microsoft.com/net/download/dotnet-core) , ale bude fungovat i s .net Core 5,0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrace a stažení aplikace

> [!div renderon="docs" class="sxs-lookup"]
>
> Máte dvě možnosti, jak začít sestavovat aplikaci: Automatická nebo ruční konfigurace.
>
> ### <a name="automatic-configuration"></a>Automatická konfigurace
>
> Pokud chcete aplikaci zaregistrovat a automaticky nakonfigurovat a potom stáhnout ukázku kódu, postupujte podle následujících kroků:
>
> 1. Přejít na <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">stránku Azure Portal pro registraci aplikace</a>.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci jediným kliknutím.
>
> ### <a name="manual-configuration"></a>Ruční konfigurace
>
> Pokud chcete ručně nakonfigurovat ukázku aplikace a kódu, použijte následující postupy.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k </span> Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář a odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Jako **název** zadejte název vaší aplikace. Například zadejte **démon-Console**. Uživatelé vaší aplikace uvidí tento název a později ho můžete změnit.
> 1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
> 1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**, zadejte název a pak vyberte **Přidat**. Poznamenejte si tajnou hodnotu v bezpečném umístění pro použití v pozdějším kroku.
> 1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**. Vyberte **Microsoft Graph**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V uzlu **uživatel** vyberte možnost **uživatel. číst. vše** a potom vyberte možnost **Přidat oprávnění**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Stažení a konfigurace aplikace pro rychlý Start
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu v tomto rychlém startu fungovala, vytvořte tajný klíč klienta a přidejte uživatele Graph API **. číst. všechna** oprávnění aplikace.
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

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Nakonfigurujte si projekt sady Visual Studio
>
> 1. Extrahujte soubor. zip do místní složky, která je blízko kořenu disku. Například extrahujte do *C:\Azure-Samples*.
>
>    K tomu, aby se předešlo chybám způsobeným omezeními délky cest ve Windows, doporučujeme extrakci archivu do adresáře v blízkosti vaší jednotky.
>
> 1. Otevřete řešení v aplikaci Visual Studio: *1-Call-MSGraph\daemon-Console.sln* (volitelné).
> 1. V *appsettings.jsna*, nahraďte hodnoty `Tenant` , `ClientId` a `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    V tomto kódu:
>    - `Enter_the_Application_Id_Here` je ID aplikace (klienta) pro aplikaci, kterou jste zaregistrovali.
        Pokud chcete najít hodnoty pro ID aplikace (klienta) a ID adresáře (tenanta), na stránce **přehledu** aplikace na Azure Portal.
>    - Nahraďte `Enter_the_Tenant_Id_Here` ID tenanta nebo názvem tenanta (například `contoso.microsoft.com` ).
>    - Nahraďte `Enter_the_Client_Secret_Here` tajným klíčem klienta, který jste vytvořili v kroku 1.
    Pokud chcete vygenerovat nový klíč, otevřete stránku **certifikáty & tajných** kódů.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte chybu *HTTP 403 – zakázáno* : "nedostatečná oprávnění k dokončení operace". K této chybě dochází, protože jakékoli oprávnění pouze k aplikacím vyžaduje globálního správce vašeho adresáře, aby bylo možné udělit souhlas vaší aplikaci. V závislosti na vaší roli vyberte jednu z následujících možností.

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste globální správce klienta, přečtěte si v Azure Portal **podnikové aplikace** . Vyberte registraci aplikace a v levém podokně vyberte **oprávnění** v části **zabezpečení** . Pak vyberte velké tlačítko s popiskem **udělení souhlasu správce pro {název tenanta}** (kde **{název tenanta}** je název vašeho adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, otevřete stránku **oprávnění rozhraní API** a vyberte **udělit souhlas správce pro Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Přejít na stránku oprávnění API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardní uživatel vašeho tenanta, požádejte globálního správce, aby pro vaši aplikaci udělil souhlas správce. Pokud to chcete provést, poskytněte správci následující adresu URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> V této adrese URL:
> * Nahraďte `Enter_the_Tenant_Id_Here` ID tenanta nebo názvem tenanta (například `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` je ID aplikace (klienta) pro aplikaci, kterou jste zaregistrovali.

Může se zobrazit chyba "AADSTS50011: žádná adresa pro odpověď není zaregistrovaná v aplikaci" po udělení souhlasu aplikace pomocí předchozí adresy URL. K této chybě dochází, protože tato aplikace a adresa URL nemají identifikátor URI přesměrování. Můžete ho ignorovat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5: spuštění aplikace

Pokud používáte aplikaci Visual Studio nebo Visual Studio pro Mac, spusťte aplikaci stisknutím klávesy **F5** . V opačném případě spusťte aplikaci prostřednictvím příkazového řádku, konzoly nebo terminálu:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
V tomto kódu:
* `{ProjectFolder}` je složka, do které jste extrahovali soubor. zip. Příklad: `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Jako výsledek by se měl zobrazit seznam uživatelů v Azure Active Directory.

V tomto rychlém startu aplikace se k identifikaci jako důvěrného klienta používá tajný klíč klienta. Tajný kód klienta se přidá do souborů projektu jako textový soubor. Z bezpečnostních důvodů doporučujeme použít certifikát místo tajného klíče klienta, než aplikaci vyberou jako produkční aplikaci. Další informace o použití certifikátu najdete v [těchto pokynech](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) v úložišti GitHub pro tuto ukázku.

## <a name="more-information"></a>Další informace
V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, co jsou hlavní argumenty a jak přidat přihlášení do existující konzolové aplikace .NET Core.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak ukázka funguje
>
> ![Diagram, který ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL) je [](https://www.nuget.org/packages/Microsoft.Identity.Client) knihovna, která se používá k přihlašování uživatelů a žádosti o tokeny pro přístup k rozhraní API chráněnému platformou Microsoft identity. Tento rychlý Start žádá o tokeny pomocí vlastní identity aplikace namísto delegovaných oprávnění. Tok ověřování v tomto případě se označuje jako [tok OAuth přihlašovacími údaji klienta](v2-oauth2-client-creds-grant-flow.md). Další informace o tom, jak používat MSAL.NET s tokem přihlašovacích údajů klienta, najdete v [tomto článku](https://aka.ms/msal-net-client-credentials).

 MSAL.NET můžete nainstalovat spuštěním následujícího příkazu v konzole správce balíčků sady Visual Studio:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```csharp
using Microsoft.Identity.Client;
```

Pak inicializujte MSAL pomocí následujícího kódu:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Popis |
 |---------|---------|
 | `config.ClientSecret` | Tajný kód klienta vytvořený pro aplikaci v Azure Portal. |
 | `config.ClientId` | ID aplikace (klienta) pro aplikaci zaregistrovanou v Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace v Azure Portal. |
 | `config.Authority`    | Volitelné Koncový bod služby tokenu zabezpečení (STS) pro uživatele, který se má ověřit Obvykle je ve `https://login.microsoftonline.com/{tenant}` veřejném cloudu, kde `{tenant}` je název vašeho TENANTA nebo ID tenanta.|

Další informace najdete v [referenční dokumentaci pro `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Žádosti o tokeny

K vyžádání tokenu pomocí identity aplikace použijte `AcquireTokenForClient` metodu:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Popis |
|---------|---------|
| `scopes` | Obsahuje požadované obory. U důvěrných klientů by tato hodnota měla používat formát podobný `{Application ID URI}/.default` . Tento formát označuje, že požadované obory jsou ty, které jsou staticky definovány v sadě objektů aplikace v Azure Portal. Pro Microsoft Graph `{Application ID URI}` odkazuje na `https://graph.microsoft.com` . Pro vlastní webová rozhraní API `{Application ID URI}` je definována v Azure Portal v části **Registrace aplikace (Preview)**  >  **zpřístupňuje rozhraní API**. |

Další informace najdete v [referenční dokumentaci pro `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o aplikacích démona najdete v přehledu scénářů:

> [!div class="nextstepaction"]
> [Aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md)
