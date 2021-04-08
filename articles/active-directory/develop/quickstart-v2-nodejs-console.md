---
title: 'Rychlý Start: volání Microsoft Graph z konzolové aplikace Node.js | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak Node.js Konzolová aplikace může získat přístupový token a volat rozhraní API chráněné koncovým bodem Microsoft Identity Platform pomocí vlastní identity aplikace.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653741"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Rychlý Start: získání tokenu a volání Microsoft Graph API z konzolové aplikace Node.js pomocí identity aplikace

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak Node.js Konzolová aplikace může získat přístupový token pomocí identity aplikace pro volání rozhraní API Microsoft Graph a zobrazení [seznamu uživatelů](/graph/api/user-list) v adresáři. Ukázka kódu ukazuje, jak lze spustit bezobslužnou úlohu nebo službu systému Windows pomocí identity aplikace namísto identity uživatele.

V tomto rychlém startu se k [udělení přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md)používá [Knihovna Microsoft Authentication Library pro Node.js (uzel MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) .

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrace a stažení ukázkové aplikace
>
> Začněte tím, že Projděte následující kroky.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Krok 1: registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `msal-node-cli` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Správa** vyberte **Certifikáty a tajné kódy**.
> 1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**, zadejte název a pak vyberte **Přidat**. Poznamenejte si tajnou hodnotu v bezpečném umístění pro použití v pozdějším kroku.
> 1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**. Vyberte **Microsoft Graph**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V části **uživatelský** uzel vyberte **uživatel. číst. vše** a pak vyberte **Přidat oprávnění**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Stažení a konfigurace ukázkové aplikace
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné vytvořit tajný klíč klienta a přidat **uživatele Graph API. číst. všechna** oprávnění aplikace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-netcore-daemon/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Krok 2: stažení ukázkového projektu Node.js

> [!div renderon="docs"]
> [Stažení ukázky kódu](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Krok 3: Konfigurace ukázkového projektu Node.js
>
> 1. Extrahujte soubor zip do místní složky blízko kořene disku, například *C:/Azure-Samples*.
> 1. Upravte *. env* a nahraďte hodnoty polí `TENANT_ID` , `CLIENT_ID` a `CLIENT_SECRET` následujícím fragmentem kódu:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Kde:
>    - `Enter_the_Application_Id_Here` – je **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali dříve. Toto ID najdete v podokně s **přehledem** registrace aplikace v Azure Portal.
>    - `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).  Tyto hodnoty najdete v podokně s **přehledem** registrace aplikace v Azure Portal.
>    - `Enter_the_Client_Secret_Here` – Nahraďte tuto hodnotu tajným klíčem klienta, který jste vytvořili dříve. Pokud chcete vygenerovat nový klíč, použijte **certifikáty & tajných** kódů v nastavení registrace aplikace v Azure Portal.
>
> > [!WARNING]
> > Jakýkoli nešifrovaný tajný klíč ve zdrojovém kódu představuje zvýšené bezpečnostní riziko. V tomto článku se pro jednoduchost používá tajný klíč klienta prostého textu. Používejte [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md) místo klientských tajných kódů v tajných klientských aplikacích, zejména u aplikací, které máte v úmyslu nasadit do produkčního prostředí.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte chybu *HTTP 403 – zakázáno* : `Insufficient privileges to complete the operation` . K této chybě dochází, protože jakékoli *oprávnění pouze k aplikacím* vyžaduje **souhlas správce**: globální správce vašeho adresáře musí udělit souhlas vaší aplikaci. V závislosti na vaší roli vyberte jednu z následujících možností:

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste globální správce klienta, v registraci aplikace Azure Portal přejít na stránku **oprávnění rozhraní API** a vyberte **udělit souhlas správce pro {název tenanta}** (kde {tenant Name} je název vašeho adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, přejít na stránku **oprávnění rozhraní API** , vyberte **udělit souhlas správce pro Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Přejít na stránku oprávnění API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardní uživatel vašeho tenanta, musíte požádat globálního správce o udělení **souhlasu správce** vaší aplikace. Pokud to chcete provést, poskytněte správci následující adresu URL:

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

V příkazovém řádku nebo konzole Najděte kořenovou složku ukázky (kde se `package.json` nachází). Je potřeba nainstalovat tyto závislosti této ukázky:

```console
npm install
```

Pak spusťte aplikaci prostřednictvím příkazového řádku nebo konzoly:

```console
node . --op getUsers
```

Měli byste vidět, že výstup na konzole je nějaký fragment JSON, který představuje seznam uživatelů v adresáři služby Azure AD.

## <a name="about-the-code"></a>O kódu

Níže jsou popsány některé důležité aspekty ukázkové aplikace.

### <a name="msal-node"></a>Uzel MSAL

[Uzel MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny, které se používají pro přístup k rozhraní API chráněnému platformou Microsoft identity. Jak je popsáno, tento rychlý Start požaduje tokeny pomocí oprávnění aplikace (pomocí vlastní identity aplikace) místo delegovaných oprávnění. V tomto případě se používá tok ověřování, který se označuje jako [tok přihlašovacích údajů klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md). Další informace o tom, jak používat uzel MSAL s aplikacemi démona, naleznete v tématu [Scenario: démon Application](scenario-daemon-overview.md).

 Uzel MSAL můžete nainstalovat spuštěním následujícího příkazu npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```javascript
const msal = require('@azure/msal-node');
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Kde: |Description |
> |---------|---------|
> | `clientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `authority`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, Obvykle `https://login.microsoftonline.com/{tenant}` pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta.|
> | `clientSecret` | Vytvoří se tajný klíč klienta pro aplikaci na webu Azure Portal. |

Další informace najdete v [referenční dokumentaci pro `ConfidentialClientApplication` ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Žádosti o tokeny

K vyžádání tokenu pomocí identity aplikace použijte `acquireTokenByClientCredential` metodu:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Kde:| Description |
> |---------|---------|
> | `tokenRequest` | Obsahuje požadované obory. U důvěrných klientů by se měla použít formát podobný tomuto jako `{Application ID URI}/.default` k označení toho, že požadované obory jsou staticky definované v sadě objektů aplikace na webu Azure Portal (pro Microsoft Graph, `{Application ID URI}` které odkazují na `https://graph.microsoft.com` ). Pro vlastní webová rozhraní API `{Application ID URI}` se definuje v části **vystavení rozhraní API** v registraci aplikace na webu Azure Portal. |
> | `tokenResponse` | Odpověď obsahuje přístupový token pro požadované obory. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o vývoji démona a konzolových aplikací pomocí uzlu MSAL najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Aplikace démona, která volá webová rozhraní API](tutorial-v2-nodejs-console.md)