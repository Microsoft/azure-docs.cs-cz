---
title: 'Kurz: Ochrana webového rozhraní API Node.js pomocí Azure AD B2C a udělit přístup k jednostránkové aplikaci (SPA)'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak pomocí služby Active Directory B2C chránit webové rozhraní API Node.js a volat z jednostránkové aplikace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875663"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Kurz: Ochrana a udělení přístupu k webovému rozhraní NODE.js z jednostránkové aplikace pomocí Azure AD B2C

Tento kurz ukazuje, jak volat Azure Active Directory B2C (Azure AD B2C) chráněné rozhraní NODE.js webové rozhraní API z jednostránkové aplikace.

V tomto kurzu druhý ve dvoudílné sérii:

> [!div class="checklist"]
> * Vytvoření registrace webové aplikace rozhraní API v tenantovi Azure AD B2C
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Úprava ukázky kódu webového rozhraní API pro práci s tenantem

V [prvním kurzu](tutorial-single-page-app.md) v této sérii jste stáhli ukázku kódu a upravili ji tak, aby přihlašovala uživatele s tokem uživatelů v tenantovi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončení kroků a předpokladů v [kurzu: Povolení ověřování v jednostránkové aplikaci pomocí Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) nebo jiný editor kódu
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu definujete oprávnění ke čtení i zápisu pro webové rozhraní API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zaznamenejte hodnotu `demo.read` v části **Obory** pro obor, který se má použít v pozdějším kroku při konfiguraci jednostránkové aplikace. Hodnota celého oboru `https://contosob2c.onmicrosoft.com/api/demo.read`je podobná hodnotě .

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z jiné aplikace, musíte této aplikaci udělit oprávnění k webovému rozhraní API.

V požadovaném kurzu jste vytvořili webovou aplikaci s názvem *webapp1*. V tomto kurzu nakonfigurujete tuto aplikaci tak, aby volala webové rozhraní API, které jste vytvořili v předchozí části *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Jednostránkové webové aplikaci byla nyní udělena oprávnění k chráněnému webovému rozhraní API pro zadané obory. Uživatel se ověřuje pomocí Azure AD B2C a použije jednostránkovou aplikaci. Jednostránková aplikace používá tok udělení autorizace pro přístup k chráněnému webovému rozhraní API s přístupovým tokenem vráceným Azure AD B2C.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Teď, když je webové rozhraní API registrované a definovali jste obory, nakonfigurujte kód webového rozhraní API tak, aby fungoval s klientem Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API Node.js, které stáhnete z GitHubu.

[Stáhněte \*si archiv ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkový projekt webového rozhraní API z GitHubu. Můžete také přejít přímo na [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) projektu na GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor *config.js* v editoru kódu.
1. Upravte hodnoty proměnných tak, aby odrážely hodnoty registrace aplikace, kterou jste vytvořili dříve. Také aktualizujte `policyName` s tok em uživatelů, který jste vytvořili jako součást předpokladů. Například *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>Povolení CORS

Chcete-li povolit jednostránkové aplikaci volat rozhraní Node.js web API, musíte povolit [CORS](https://expressjs.com/en/resources/middleware/cors.html) ve webovém rozhraní API. V produkční aplikaci byste měli být opatrní, která doména je podání požadavku, ale pro účely tohoto kurzu povolit požadavky z libovolné domény.

Chcete-li povolit CORS, použijte následující middleware. V ukázce kódu webového rozhraní API Node.js v tomto kurzu již byl přidán do souboru *index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránková aplikace (SPA) z [předchozího kurzu](tutorial-single-page-app.md) v řadě používá Azure AD B2C pro registraci a přihlášení uživatele a ve výchozím nastavení volá rozhraní Node.js webové rozhraní API chráněné ukázkovým tenantem *fabrikamb2c.*

V této části aktualizujete jednostránkovou webovou aplikaci tak, aby volala webové rozhraní API Node.js chráněné *vaším* tenantem Azure AD B2C (a které spustíte v místním počítači).

Změna nastavení ve spa:

1. V projektu [active-directory-b2c-javascript-msal-singlepageapp,][github-js-spa] který jste stáhli nebo naklonovali v předchozím kurzu, otevřete soubor *apiConfig.js* ve složce *JavaScriptSPA.*
1. Nakonfigurujte ukázku pomocí identifikátoru URI pro dříve vytvořený obor *demo.read* a adresu URL webového rozhraní API.
    1. V `apiConfig` definici nahraďte hodnotu `b2cScopes` úplným identifikátorem URI pro obor *demo.read* (hodnota **Obor,** kterou jste zaznamenali dříve).
    1. Změňte doménu `webApi` v hodnotě na identifikátor URI přesměrování, který jste přidali při registraci webové aplikace rozhraní API v předchozím kroku.

    Vzhledem k tomu, `/hello` že rozhraní API je přístupné v koncovém bodě, ponechte */hello* v identifikátoru URI.

    Definice `apiConfig` by měla vypadat podobně jako následující blok kódu, ale se jménem `<your-tenant-name>`klienta B2C v místě :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Spuštění spa a webového rozhraní API

Nyní jste připraveni otestovat přístup jednostránkové aplikace k rozhraní API. Spusťte webové rozhraní Node.js i ukázkovou jednostránkovou aplikaci JavaScript v místním počítači. Potom se přihlaste k jednostránkové aplikaci a vyberte tlačítko **rozhraní API pro volání** a iniciujte požadavek na chráněné rozhraní API.

I když obě aplikace běží místně, když budete postupovat podle tohoto kurzu, jste je nakonfigurovali tak, aby používaly Azure AD B2C pro zabezpečené registrace nebo přihlášení a udělit přístup k chráněnému webovému rozhraní API.

### <a name="run-the-nodejs-web-api"></a>Spuštění webového rozhraní NODE.js

1. Otevřete okno konzoly a změňte adresář obsahující ukázku webového rozhraní API Node.js. Příklad:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    node index.js
    ```

    V okně konzoly se zobrazí číslo portu, kde je aplikace hostována.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Spuštění jednostránkové aplikace

1. Otevřete jiné okno konzoly a přecházejte do adresáře obsahujícího ukázku javascriptového spa. Příklad:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    npm start
    ```

    V okně konzoly se zobrazí číslo portu, kde je aplikace hostována.

    ```console
    Listening on port 6420...
    ```

1. Chcete-li aplikaci zobrazit, přejděte `http://localhost:6420` v prohlížeči.

    ![Ukázková aplikace s jednou stránkou zobrazená v prohlížeči](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [předchozím kurzu](tutorial-single-page-app.md). Po úspěšném přihlášení, `User 'Your Username' logged-in` měli byste vidět zprávu.
1. Vyberte tlačítko **Rozhraní API pro volání.** Spa získá udělení autorizace od Azure AD B2C, pak přistupuje k chráněné webové rozhraní API pro zobrazení názvu přihlášeného uživatele:

    ![Jednostránková aplikace v prohlížeči zobrazující výsledek uživatelského jména JSON vrácený rozhraním API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření registrace webové aplikace rozhraní API v tenantovi Azure AD B2C
> * Nakonfigurované obory pro webové rozhraní API
> * Udělená oprávnění k webovému rozhraní API
> * Upravil ukázku kódu webového rozhraní API pro práci s tenantem.

Teď, když jste viděli SPA požádat o prostředek z chráněného webového rozhraní API, získat hlubší představu o tom, jak tyto typy aplikací vzájemně spolupracují a s Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikací, které lze použít ve službě Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
