---
title: Microsoft identity platforma Python daemon | Azure
description: Zjistěte, jak může proces Pythonu získat přístupový token a volat rozhraní API chráněné koncovým bodem platformy identit Microsoftu pomocí vlastní identity aplikace
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3c6cb6303734b5336b3e9a7646e5eb3310d0f236
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536042"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace

V tomto rychlém startu napište aplikaci Pythonu, která získá přístupový token pomocí identity aplikace, a pak zavolá rozhraní Microsoft Graph API pro zobrazení [seznamu uživatelů](https://docs.microsoft.com/graph/api/user-list) v adresáři. Tento scénář je užitečný v situacích, kdy bezhlavá, bezobslužná úloha nebo služba systému Windows musí být spuštěna s identitou aplikace namísto identity uživatele.

> [!div renderon="docs"]
> ![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, potřebujete:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) nebo [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start

> [!div renderon="docs" class="sxs-lookup"]
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý start: Express (možnost 1 níže) a Ruční (Možnost 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejděte na nové [podokno Registrace aplikací azure.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **možnost Nová registrace**.
> 1. Po zobrazení stránky **Registrovat přihlášku** zadejte registrační údaje vaší žádosti.
> 1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí `Daemon-console`uživatelům aplikace, například , a pak vyberte **Registrovat** a vytvořte aplikaci.
> 1. Po registraci vyberte nabídku **Certifikáty & tajných kódů.**
> 1. V části **Tajné klíče klienta**vyberte **možnost + Nový tajný klíč klienta**. Pojmenujte ho a vyberte **Přidat**. Zkopírujte tajný klíč na bezpečném místě. Budete potřebovat k použití ve vašem kódu.
> 1. Nyní vyberte nabídku **Oprávnění rozhraní API,** vyberte + Přidat tlačítko **oprávnění,** vyberte **Microsoft Graph**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V části **Uživatelský** uzel vyberte **User.Read.All**a pak vyberte **Přidat oprávnění.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Stažení a konfigurace aplikace quickstart
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
> Pro ukázku kódu pro tento rychlý start pracovat, musíte vytvořit tajný klíč klienta a přidat rozhraní **User.Read.All** rozhraní api rozhraní graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-netcore-daemon/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-python-project"></a>Krok 2: Stažení projektu Pythonu

> [!div renderon="docs"]
> [Stažení projektu daemon pythonu](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Krok 3: Konfigurace projektu Pythonu
>
> 1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
> 1. Přejděte do podsložky **1-Call-MsGraph-WithSecret"**.
> 1. Upravte **parametry.json** a nahraďte `client_id`hodnoty `secret` polí `authority`, a tímto fragmentem:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Kde:
>    - Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
>    - `Enter_the_Tenant_Id_Here`- nahradit tuto hodnotu **Id klienta** nebo **název klienta** (například contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`- nahradit tuto hodnotu tajným jménem klienta vytvořeným v kroku 1.
>
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta),** **ID adresáře (tenanta),** přejděte na stránku **Přehled** aplikace na webu Azure Portal. Chcete-li vygenerovat nový klíč, přejděte na stránku **Certifikáty & tajných kódů.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: Souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: Souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte HTTP `Insufficient privileges to complete the operation` *403 - Zakázaná* chyba: . K této chybě dochází, protože všechna *oprávnění pouze pro aplikace* vyžadují souhlas správce: globální správce vašeho adresáře musí udělit souhlas s vaší aplikací. Vyberte jednu z níže uvedených možností v závislosti na vaší roli:

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste správce globálního klienta, přejděte na stránku **Oprávnění rozhraní API** v registraci aplikací na webu Azure Portal (Preview) a vyberte **Udělit souhlas správce pro {Název klienta}** (Kde {Název klienta} je název vašeho adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, přejděte na stránku **Oprávnění rozhraní API** a vyberte **Udělit souhlas správce pro Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Přejít na stránku Oprávnění rozhraní API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardním uživatelem vašeho tenanta, musíte požádat globálního správce o udělení souhlasu správce pro vaši aplikaci. Chcete-li to provést, zadejte správci následující adresu URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Kde:
>> * `Enter_the_Tenant_Id_Here`- nahradit tuto hodnotu **Id klienta** nebo **název klienta** (například contoso.microsoft.com)
>> * Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: Spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5: Spuštění aplikace

Budete muset nainstalovat závislosti této ukázky jednou

```console
pip install -r requirements.txt
```

Potom spusťte aplikaci pomocí příkazového řádku nebo konzoly:

```console
python confidential_client_secret_sample.py parameters.json
```

Měli byste vidět na výstupu konzoly některé Fragment Json představující seznam uživatelů v adresáři Azure AD.

> [!IMPORTANT]
> Tato aplikace rychlého startu používá tajný klíč klienta k identifikaci jako důvěrný klient. Vzhledem k tomu, že tajný klíč klienta je přidán jako prostý text do souborů projektu, z bezpečnostních důvodů se doporučuje použít certifikát namísto tajného klíče klienta před zvážením aplikace jako produkční aplikace. Další informace o použití certifikátu naleznete v [těchto pokynech](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) ve stejném úložišti GitHub pro tuto ukázku, ale ve druhé složce **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Další informace

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) je knihovna používaná k přihlášení uživatelů a vyžádání tokenů používaných pro přístup k rozhraní API chráněnému platformou identit microsoftu. Jak je popsáno, tento rychlý start požaduje tokeny pomocí vlastní identity aplikace namísto delegovaných oprávnění. Tok ověřování použitý v tomto případě se označuje jako *[tok pověření klienta .](v2-oauth2-client-creds-grant-flow.md)* Další informace o tom, jak používat MSAL Python s aplikacemi pro daemon, naleznete v [tomto článku](scenario-daemon-overview.md).

 MSAL Python můžete nainstalovat spuštěním následujícího příkazu pip.

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

> | Kde: ||
> |---------|---------|
> | `config["secret"]` | Je tajný klíč klienta vytvořený pro aplikaci na webu Azure Portal. |
> | `config["client_id"]` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `config["authority"]`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, Obvykle <https://login.microsoftonline.com/{tenant}> pro veřejný cloud, kde {tenant} je název vašeho klienta nebo Id vašeho klienta.|

Další informace naleznete v [referenční `ConfidentialClientApplication` dokumentaci pro](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Žádosti o tokeny

Chcete-li požádat o token pomocí `AcquireTokenForClient` identity aplikace, použijte metodu:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Kde:| |
> |---------|---------|
> | `config["scope"]` | Obsahuje požadované obory. Pro důvěrné klienty by to `{Application ID URI}/.default` mělo použít formát podobný k označení, že požadované obory jsou ty, které jsou `{Application ID URI}` staticky `https://graph.microsoft.com`definované v objektu aplikace nastaveném na webu Azure Portal (pro Microsoft Graph, odkazuje na). Pro vlastní webová `{Application ID URI}` rozhraní API je definována v části **Vystavit rozhraní API** v registraci aplikací (Preview) portálu Azure Portal. |

Další informace naleznete v [referenční `AcquireTokenForClient` dokumentaci pro](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o aplikacích pro daemon najdete na vstupní stránce scénáře.

> [!div class="nextstepaction"]
> [Daemon aplikace, která volá webová api](scenario-daemon-overview.md)

Kurz aplikace pro daemon naleznete v následujících tématech:

> [!div class="nextstepaction"]
> [Daemon Python konzole tutorial](https://github.com/Azure-Samples/ms-identity-python-daemon)

Další informace o oprávněních a souhlasu:

> [!div class="nextstepaction"]
> [Oprávnění a souhlas](v2-permissions-and-consent.md)

Další informace o toku ověřování pro tento scénář naleznete v toku pověření klienta Oauth 2.0:

> [!div class="nextstepaction"]
> [Tok pověření klienta Oauth](v2-oauth2-client-creds-grant-flow.md)
