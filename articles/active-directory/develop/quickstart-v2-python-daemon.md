---
title: 'Rychlý Start: volání Microsoft Graph z démona Pythonu | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může proces Pythonu získat přístupový token a volat rozhraní API chráněné koncovým bodem Microsoft Identity Platform, pomocí vlastní identity aplikace.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3d4d671fed675de4cb2684d205f8e8b62d3b95cd
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107902"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Rychlý Start: získání tokenu a volání Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak může aplikace Python získat přístupový token pomocí identity aplikace pro volání rozhraní API Microsoft Graph a zobrazení [seznamu uživatelů](/graph/api/user-list) v adresáři. Ukázka kódu ukazuje, jak lze spustit bezobslužnou úlohu nebo službu systému Windows pomocí identity aplikace namísto identity uživatele. 

> [!div renderon="docs"]
> ![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Předpoklady

K provedení této ukázky budete potřebovat:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) nebo [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start

> [!div renderon="docs" class="sxs-lookup"]
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1 níže) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se na [Azure Portal](https://portal.azure.com).
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `Daemon-console` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Spravovat** vyberte **certifikáty & tajných** kódů.
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

#### <a name="step-2-download-your-python-project"></a>Krok 2: stažení projektu Python

> [!div renderon="docs"]
> [Stáhnout projekt démona Pythonu](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Krok 3: konfigurace projektu Pythonu
>
> 1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
> 1. Přejděte do dílčí složky **1-Call-MsGraph-WithSecret**.
> 1. Upravte **parameters.js** a nahraďte hodnoty polí `authority` , `client_id` a `secret` následujícím fragmentem kódu:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Kde:
>    - Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
>    - `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>    - `Enter_the_Client_Secret_Here` – Nahraďte tuto hodnotu tajným klíčem klienta vytvořeným v kroku 1.
>
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)**, navštivte stránku **Přehled** aplikace v Azure Portal. Pokud chcete vygenerovat nový klíč, otevřete stránku **certifikáty & tajných** kódů.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte chybu *HTTP 403 – zakázáno* : `Insufficient privileges to complete the operation` . K této chybě dochází, protože jakékoli *oprávnění pouze k aplikacím* vyžaduje souhlas správce: globální správce vašeho adresáře musí udělit souhlas vaší aplikaci. V závislosti na vaší roli vyberte jednu z následujících možností:

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste globální správce klienta, v registraci aplikace na webu Azure Portal klikněte na stránku **oprávnění rozhraní API** (Preview) a vyberte **udělit souhlas správce pro {název tenanta}** (kde {název tenanta} je název vašeho adresáře).

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

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5: spuštění aplikace

Budete muset nainstalovat závislosti této ukázky.

```console
pip install -r requirements.txt
```

Pak spusťte aplikaci prostřednictvím příkazového řádku nebo konzoly:

```console
python confidential_client_secret_sample.py parameters.json
```

Měli byste vidět, že výstup na konzole je nějaký fragment JSON, který představuje seznam uživatelů v adresáři služby Azure AD.

> [!IMPORTANT]
> Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se místo toho použít certifikát namísto tajného klíče klienta před tím, než aplikaci vyberou jako produkční aplikaci. Další informace o tom, jak použít certifikát, najdete v [těchto pokynech](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) ve stejném úložišti GitHub pro tuto ukázku, ale ve druhé složce **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Další informace

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny, které se používají pro přístup k rozhraní API chráněnému platformou Microsoft identity. Jak je popsáno, tento rychlý Start žádá o tokeny pomocí vlastní identity aplikace namísto delegovaných oprávnění. Tok ověřování použitý v tomto případě se označuje jako *[tok OAuth přihlašovacími údaji klienta](v2-oauth2-client-creds-grant-flow.md)*. Další informace o tom, jak používat MSAL Python s aplikacemi démon, najdete v [tomto článku](scenario-daemon-overview.md).

 MSAL Python můžete nainstalovat spuštěním následujícího příkazu PIP.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```Python
import msal
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Kde: |Popis |
> |---------|---------|
> | `config["secret"]` | Vytvoří se tajný klíč klienta pro aplikaci na webu Azure Portal. |
> | `config["client_id"]` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `config["authority"]`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, Obvykle `https://login.microsoftonline.com/{tenant}` pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta.|

Další informace najdete v [referenční dokumentaci pro `ConfidentialClientApplication` ](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Žádosti o tokeny

K vyžádání tokenu pomocí identity aplikace použijte `AcquireTokenForClient` metodu:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Kde:| Popis |
> |---------|---------|
> | `config["scope"]` | Obsahuje požadované obory. U důvěrných klientů by se měla použít formát podobný tomuto jako `{Application ID URI}/.default` k označení toho, že požadované obory jsou staticky definované v sadě objektů aplikace na webu Azure Portal (pro Microsoft Graph, `{Application ID URI}` které odkazují na `https://graph.microsoft.com` ). Pro vlastní webová rozhraní API `{Application ID URI}` se definuje v části **vystavení rozhraní API** v registraci aplikace na webu Azure Portal (Preview). |

Další informace najdete v [referenční dokumentaci pro `AcquireTokenForClient` ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o aplikacích démona najdete na cílové stránce scénáře.

> [!div class="nextstepaction"]
> [Aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md)
