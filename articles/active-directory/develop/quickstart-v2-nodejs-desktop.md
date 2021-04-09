---
title: 'Rychlý Start: volání Microsoft Graph z desktopové aplikace Node.js | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak se Node.js aplikace pracovní plochy může přihlašovat uživatelům a získat přístupový token pro volání rozhraní API chráněného koncovým bodem platformy Microsoft Identity Platform.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653265"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Rychlý Start: získání přístupového tokenu a volání rozhraní Microsoft Graph API z aplikace pracovní plochy

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se aplikace pracovní plochy může přihlašovat uživatelům a získat přístupové tokeny pro volání rozhraní Microsoft Graph API.

V tomto rychlém startu se používá [Knihovna ověřování Microsoft pro Node.js (uzel MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) s [tokem autorizačního kódu s PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrace a stažení ukázkové aplikace
>
> Začněte tím, že Projděte následující kroky.
>
> #### <a name="step-1-register-the-application"></a>Krok 1: registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `msal-node-desktop` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Vyberte **Přidat platformu**  >  **mobilní a desktopové aplikace**.
> 1. V části **identifikátory URI pro přesměrování** zadejte `msal://redirect` .
> 1. Vyberte **Konfigurovat**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat adresu URL odpovědi jako **msal://Redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-windows-desktop/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-electron-sample-project"></a>Krok 2: stažení ukázkového projektu

> [!div renderon="docs"]
> [Stažení ukázky kódu](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Krok 3: konfigurace projektu s elektronickou ukázkou
>
> 1. Extrahujte soubor zip do místní složky blízko kořene disku, například *C:/Azure-Samples*.
> 1. Upravte *. env* a nahraďte hodnoty polí `TENANT_ID` a `CLIENT_ID` následujícím fragmentem kódu:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Kde:
>    - Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
>    - `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)**, navštivte stránku **Přehled** aplikace v Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

Je potřeba nainstalovat tyto závislosti této ukázky:

```console
npm install
```

Pak spusťte aplikaci prostřednictvím příkazového řádku nebo konzoly:

```console
npm start
```

Mělo by se zobrazit uživatelské rozhraní aplikace s **přihlašovacím** tlačítkem.

## <a name="about-the-code"></a>O kódu

Níže jsou popsány některé důležité aspekty ukázkové aplikace.

### <a name="msal-node"></a>Uzel MSAL

[Uzel MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny, které se používají pro přístup k rozhraní API chráněnému platformou Microsoft identity. Další informace o tom, jak používat uzel MSAL s desktopovým aplikacemi, najdete v [tomto článku](scenario-desktop-overview.md).

Uzel MSAL můžete nainstalovat spuštěním následujícího příkazu npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Přidáním následujícího kódu můžete přidat odkaz na uzel MSAL:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Kde: |Description |
> |---------|---------|
> | `clientId` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `authority`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, Obvykle `https://login.microsoftonline.com/{tenant}` pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta.|

### <a name="requesting-tokens"></a>Žádosti o tokeny

V první nožkě toku autorizačního kódu s PKCE Připravte a odešlete požadavek autorizačního kódu s příslušnými parametry. Potom v druhé nožkě toku počkejte na odpověď autorizačního kódu. Po získání kódu ho vyměňovat, aby získal token.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Kde:| Description |
> |---------|---------|
> | `authWindow` | Aktuální elektronické okno v procesu. |
> | `tokenRequest` | Obsahuje požadované obory, například `"User.Read"` pro Microsoft Graph nebo `"api://<Application ID>/access_as_user"` pro vlastní webová rozhraní API. |

## <a name="next-steps"></a>Další kroky

Další informace o vývoji aplikací pro elektronickou plochu pomocí uzlu MSAL najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz: přihlášení uživatelů a volání rozhraní API pro Microsoft Graph v pracovní desktopové aplikaci](tutorial-v2-nodejs-desktop.md)