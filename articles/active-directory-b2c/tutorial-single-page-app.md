---
title: 'Kurz: povolení ověřování v jedné stránce aplikace'
titleSuffix: Azure AD B2C
description: V tomto kurzu se naučíte používat Azure Active Directory B2C k poskytnutí přihlašovacích údajů uživatele pro jednostránkové aplikaci založenou na JavaScriptu (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96171224"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Kurz: povolení ověřování v aplikaci s jednou stránkou s Azure AD B2C

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory B2C (Azure AD B2C) se zaregistrovat a přihlašovat uživatele v rámci jednostránkové aplikace (SPA) pomocí těchto akcí:
* [Tok autorizačního kódu OAuth 2,0](./authorization-code-flow.md) (s použitím [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* [Tok implicitního udělení OAuth 2,0](./implicit-flow-single-page-application.md) (použití [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

V tomto kurzu první v řadě dvou částí:

> [!div class="checklist"]
> * Přidání adresy URL odpovědi do aplikace zaregistrované ve vašem tenantovi Azure AD B2C
> * Stažení ukázky kódu z GitHubu
> * Úprava kódu ukázkové aplikace pro spolupráci s vaším klientem
> * Zaregistrujte se pomocí uživatelského toku pro registraci a přihlašování.

V [dalším kurzu](tutorial-single-page-app-webapi.md) v řadě se povoluje část webové rozhraní API v ukázce kódu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat v krocích v tomto kurzu, budete potřebovat následující Azure AD B2C prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace zaregistrovaná](tutorial-register-spa.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

V místním vývojovém prostředí budete navíc potřebovat následující:

* [Visual Studio Code](https://code.visualstudio.com/) nebo jiný Editor kódu
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

V [druhém kurzu](./tutorial-register-spa.md) , který jste dokončili v rámci požadavků, jste zaregistrovali jednostránkovou aplikaci v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkami kódu v tomto kurzu, přidejte do registrace aplikace adresu URL odpovědi (označuje se taky jako identifikátor URI přesměrování).

Pokud chcete aktualizovat aplikaci ve vašem tenantovi Azure AD B2C, můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations-auth-code-flow"></a>[Registrace aplikací (tok kódu ověřování)](#tab/app-reg-auth/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**, vyberte kartu **vlastněné aplikace** a pak vyberte aplikaci *spaapp1* .
1. V části **jednostránková aplikace** vyberte odkaz **Přidat identifikátor URI** a pak zadejte `http://localhost:6420` .
1. Vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

#### <a name="app-registrations-implicit-flow"></a>[Registrace aplikací (implicitní tok)](#tab/app-reg-implicit/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**, vyberte kartu **vlastněné aplikace** a pak vyberte aplikaci *spaapp1* .
1. V části **jednostránková aplikace** vyberte odkaz **Přidat identifikátor URI** a pak zadejte `http://localhost:6420` .
1. V části **implicitní udělení** vyberte zaškrtávací políčka pro **přístupové tokeny** a **tokeny ID** , pokud ještě není vybraná, a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte aplikaci *spaapp1* .
1. V části **Adresa URL odpovědi** přidejte `http://localhost:6420` .
1. Vyberte **Uložit**.
1. Na stránce vlastnosti si poznamenejte **ID aplikace**. ID aplikace použijete v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, který stáhnete z GitHubu, abyste mohli pracovat s vaším klientem B2C. Ukázka předvádí, jak může aplikace s jednou stránkou používat Azure AD B2C pro registraci a přihlašování uživatelů a volání chráněného webového rozhraní API (povolení webového rozhraní API v dalším kurzu v řadě).

* Ukázka toku autorizačního kódu MSAL.js 2. x:

    [Stáhněte si soubor zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) nebo naklonujte ukázku z GitHubu:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* Ukázka implicitního toku MSAL.js 1. x:

    [Stáhněte si soubor zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali ukázku, aktualizujte kód pomocí Azure AD B2C název tenanta a ID aplikace, které jste si poznamenali v předchozím kroku.

#### <a name="auth-code-flow-sample"></a>[Ukázka toku kódu ověřování](#tab/config-auth/)

1. Otevřete soubor *authConfig.js* ve složce *aplikace* .
1. V `msalConfig` objektu vyhledejte přiřazení `clientId` a nahraďte ho pomocí **ID aplikace (klienta)** , které jste si poznamenali v předchozím kroku.
1. Otevřete soubor `policies.js`.
1. Vyhledejte položky v části `names` a nahraďte jejich přiřazení názvem uživatelských toků, které jste vytvořili v předchozím kroku, například `B2C_1_signupsignin1` .
1. Vyhledejte položky v části `authorities` a podle potřeby je nahraďte názvy uživatelských toků, které jste vytvořili v předchozím kroku, například `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Najděte přiřazení pro `authorityDomain` a nahraďte ho `<your-tenant-name>.b2clogin.com` .
1. Otevřete soubor `apiConfig.js`.
1. Vyhledejte přiřazení k adrese `b2cScopes` URL oboru, kterou jste vytvořili pro webové rozhraní API, a nahraďte ji `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Najděte přiřazení pro `webApi` a nahraďte aktuální adresu URL adresou URL, na které jste nasadili webové rozhraní API v kroku 4, například `webApi: http://localhost:5000/hello` .

#### <a name="implicit-flow-sample"></a>[Ukázka implicitního toku](#tab/config-implicit/)

1. Otevřete soubor *authConfig.js* ve složce *JavaScriptSPA* .
1. V `msalConfig` objektu vyhledejte přiřazení `clientId` a nahraďte ho pomocí **ID aplikace (klienta)** , které jste si poznamenali v předchozím kroku.
1. Otevřete soubor `policies.js`.
1. Vyhledejte položky v části `names` a nahraďte jejich přiřazení názvem uživatelských toků, které jste vytvořili v předchozím kroku, například `B2C_1_signupsignin1` .
1. Vyhledejte položky v části `authorities` a podle potřeby je nahraďte názvy uživatelských toků, které jste vytvořili v předchozím kroku, například `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Otevřete soubor `apiConfig.js`.
1. Vyhledejte přiřazení k adrese `b2cScopes` URL oboru, kterou jste vytvořili pro webové rozhraní API, a nahraďte ji `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Najděte přiřazení pro `webApi` a nahraďte aktuální adresu URL adresou URL, na které jste nasadili webové rozhraní API v kroku 4, například `webApi: http://localhost:5000/hello` .

* * *

Výsledný kód by měl vypadat nějak takto:

#### <a name="auth-code-flow-sample"></a>[Ukázka toku kódu ověřování](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Ukázka implicitního toku](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete okno konzoly a přejděte do adresáře obsahujícího ukázku. 

    - Pro MSAL.js 2. x – ukázka toku autorizačního kódu:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - Ukázka implicitního toku pro MSAL.js 1. x: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    npm start
    ```

    V okně konzoly se zobrazí číslo portu místně běžícího Node.js serveru:

    ```console
    Listening on port 6420...
    ```
1. Přejděte k `http://localhost:6420` zobrazení webové aplikace spuštěné v místním počítači.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webový prohlížeč, na kterém běží samoobslužná aplikace spuštěná místně":::

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

Tato ukázková aplikace podporuje registraci, přihlášení a resetování hesla. V tomto kurzu se přihlásíte pomocí e-mailové adresy.

1. Vyberte možnost **Přihlásit** se a zahajte tok *B2C_1_signupsignin1ho* uživatele, který jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku, která obsahuje odkaz na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Registrovat nyní** .
1. Pracovní postup registrace představuje stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Stránka pro registraci zobrazená pomocí Azure AD B2C toku uživatele":::

1. Vyberte **vytvořit** k vytvoření místního účtu v adresáři Azure AD B2C.

Když vyberete **vytvořit**, aplikace zobrazí jméno přihlášeného uživatele.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webový prohlížeč zobrazující jednostránkovou aplikaci s přihlášeným uživatelem":::

Pokud chcete otestovat přihlášení, vyberte tlačítko **Odhlásit** se a pak vyberte **Přihlásit** se a přihlaste se pomocí e-mailové adresy a hesla, které jste zadali při registraci.

### <a name="what-about-calling-the-api"></a>Jak volat rozhraní API?

Pokud po přihlášení vyberete tlačítko pro **volání rozhraní API** , zobrazí se místo výsledků volání rozhraní API stránka pro registraci a přihlášení uživatele s uživatelským tokem. Očekává se, že jste ještě nenakonfigurovali část aplikace API, aby komunikovala s aplikací webového rozhraní API registrovanou ve *vašem* Azure AD B2C tenantovi.

V tomto okamžiku se aplikace stále snaží komunikovat s rozhraním API registrovaným v ukázkovém tenantovi (fabrikamb2c.onmicrosoft.com), a protože s tímto klientem nejste ověřeni, zobrazí se stránka pro registraci a přihlášení.

Přejděte k dalšímu kurzu v řadě v tématu a povolte chráněné rozhraní API (viz část [Další kroky](#next-steps) ).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali jednostránkovou aplikaci, která bude pracovat s uživatelským tokem v tenantovi Azure AD B2C, aby se zajistila možnost registrace a přihlášení. Dokončili jste tyto kroky:

> [!div class="checklist"]
> * Do aplikace zaregistrované ve vašem Azure AD B2C tenantovi se přidala adresa URL odpovědi.
> * Stáhli ukázku kódu z GitHubu
> * Upravení kódu ukázkové aplikace pro spolupráci s vaším klientem
> * Zaregistrovali jste se pomocí uživatelského toku pro registraci a přihlašování.

Teď přejděte k dalšímu kurzu v řadě, abyste udělili přístup k chráněnému webovému rozhraní API z hesla SPA:

> [!div class="nextstepaction"]
> [Kurz: ochrana a udělení přístupu k webovému rozhraní API z jednostránkové aplikace >](tutorial-single-page-app-webapi.md)