---
title: Použití MSAL.js s Azure AD B2C
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) umožňuje aplikacím pracovat s Azure AD B2C a získat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API od ostatních nebo vaše vlastní webové rozhraní API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063718"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Použití knihovny Microsoft Authentication Library pro JavaScript pro práci s Azure AD B2C

[Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umožňuje vývojářům JavaScriptu ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

Pomocí Azure AD B2C jako služby správy identit můžete přizpůsobit a řídit, jak se vaši zákazníci při používání vašich aplikací přihlásí, přihlásíte a spravují své profily. Azure AD B2C také umožňují vytvořit vlastní uživatelské rozhraní, které vaše aplikace zobrazí během procesu ověřování.

Následující části ukazují, jak:

- Ochrana Node.js webového rozhraní API
- Podpora přihlašování v aplikaci s jednou stránkou (SPA) *a volání* chráněného webového rozhraní API
- Povolit podporu resetování hesel

## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, vytvořte [tenanta Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>Webové rozhraní API Node.js

Následující kroky ukazují, jak může **webové rozhraní API** použít Azure AD B2C k ochraně sebe sama a vystavit vybrané obory klientské aplikaci.

MSAL.js pro uzel je aktuálně ve vývoji. Další informace najdete v tématu [plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) na GitHubu. V současnosti doporučujeme používat [Passport – Azure-AD](https://github.com/AzureAD/passport-azure-ad), knihovnu ověřování pro Node.js vyvinutou a podporovanou Microsoftem.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Pokud chcete své webové rozhraní API chránit pomocí Azure AD B2C, musíte ho nejdřív zaregistrovat. Podrobný postup najdete v tématu věnovaném [registraci aplikace](../../active-directory-b2c/add-web-api-application.md) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: stažení ukázkové aplikace

Stáhněte si ukázku jako soubor ZIP nebo ho naklonujte z GitHubu:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. Otevřete *config.js* v souboru v ukázce.

2. Nakonfigurujte ukázku pomocí přihlašovacích údajů aplikace, které jste získali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvem vašeho tenanta, ID klienta a názvem zásady.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Další informace najdete v této [ ukázceNode.js B2C webového rozhraní API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

Následující kroky ukazují, jak může **aplikace s jednou stránkou** používat Azure AD B2C k registraci, přihlášení a volání chráněného webového rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobný postup najdete v tématu věnovaném [registraci aplikace](../../active-directory-b2c/tutorial-register-applications.md) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: stažení ukázkové aplikace

Stáhněte si ukázku kódu [. Archiv ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte úložiště GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

Při konfiguraci aplikace je potřeba mít dva důležité skutečnosti:

- Konfigurace koncového bodu rozhraní API a vystavených oborů
- Konfigurace parametrů ověřování a oborů tokenů

1. Otevřete soubor *apiConfig.js* v ukázce.

2. Nakonfigurujte ukázku pomocí parametrů, které jste získali dříve během registrace webového rozhraní API. Změňte následující řádky kódu nahrazením hodnot adresou vašeho webového rozhraní API a vystavenými obory.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Otevřete soubor *authConfig.js* v ukázce.

1. Nakonfigurujte ukázku pomocí parametrů, které jste získali dříve při registraci jednostránkové aplikace. Změňte následující řádky kódu nahrazením hodnot hodnotami ID ClientId, autority a žádostí o tokeny.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Další informace najdete v tomto [ukázkovém B2C aplikaci pro JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Podpora resetování hesla

V této části rozšíříte jednostránkovou aplikaci tak, aby používala tok uživatele Azure AD B2C resetování hesla. I když MSAL.js v současné době nepodporují nativně více uživatelských toků nebo vlastních zásad, můžete knihovnu použít ke zpracování běžných případů použití jako resetování hesla.

V následujících krocích se předpokládá, že jste už provedli kroky uvedené v předchozí části s kódem [JavaScript Spa](#javascript-spa) .

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Krok 1: definování řetězce autority pro tok uživatele resetování hesla

1. Nejprve vytvořte objekt, kam uložíte identifikátor URI autority:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Dále inicializujte objekt MSAL se `signInSignUp` zásadami jako výchozí (viz předchozí fragment kódu). Když se uživatel pokusí přihlásit, zobrazí se vám následující obrazovka:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Přihlašovací obrazovka zobrazovaná Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Krok 2: zachycení a zpracování chyb ověřování v metodě přihlášení

Když uživatel vybere **zapomenuté heslo**, vaše aplikace vyvolá chybu, kterou byste měli zachytit ve svém kódu, a pak ji zpracovat tak, že prezentuje příslušný tok uživatele. V tomto případě je to `b2c_1_reset` tok resetování hesla.

1. Metodu přihlašování rozšíříte takto:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Předchozí fragment kódu ukazuje, jak zobrazit obrazovku pro resetování hesla po zachycení chyby s kódem `AADB2C90118` .

    Po resetování hesla se uživatel vrátí zpátky do aplikace, aby se znovu přihlásil.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Obrazovky toku resetování hesla zobrazené Azure AD B2C" border="false":::

    Další informace o kódech chyb a o zpracování výjimek naleznete v tématu [MSAL Error and Exception Codes](msal-error-handling-js.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o těchto Azure AD B2C konceptech:

- [Toky uživatelů](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Vlastní zásady](../../active-directory-b2c/custom-policy-get-started.md)
- [Přizpůsobení uživatelského prostředí](../../active-directory-b2c/configure-user-input.md)
