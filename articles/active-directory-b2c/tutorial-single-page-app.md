---
title: 'Kurz: Povolení ověřování v jednostránkové aplikaci'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak pomocí služby Azure Active Directory B2C poskytnout přihlášení uživatele pro jednostránkovou aplikaci (SPA) založenou na jazyce JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875543"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Kurz: Povolení ověřování v jednostránkové aplikaci pomocí Azure AD B2C

Tento kurz ukazuje, jak používat Azure Active Directory B2C (Azure AD B2C) k registraci a přihlášení uživatelů v jednostránkové aplikaci (SPA).

V tomto kurzu první ve dvoudílné sérii:

> [!div class="checklist"]
> * Přidání adresy URL odpovědi do aplikace registrované v tenantovi Azure AD B2C
> * Stažení ukázky kódu z GitHubu
> * Úprava kódu ukázkové aplikace pro práci s tenantem
> * Zaregistrujte se pomocí toku uživatelů registrace/přihlášení

[Další kurz](tutorial-single-page-app-webapi.md) v řadě umožňuje web API část ukázky kódu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před pokračováním v krocích v tomto kurzu potřebujete následující prostředky Azure AD B2C na místě:

* [Klient Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace registrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

Kromě toho budete potřebovat následující v prostředí místního rozvoje:

* [Visual Studio Code](https://code.visualstudio.com/) nebo jiný editor kódu
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

Ve druhém kurzu, který jste dokončili jako součást předpokladů, jste zaregistrovali webovou aplikaci v Azure AD B2C. Chcete-li povolit komunikaci s ukázkou kódu v tomto kurzu, přidejte adresu URL odpovědi (označovanou také jako identifikátor URI přesměrování) k registraci aplikace.

K aktualizaci aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. V levém horním rohu portálu Azure vyberte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte aplikaci *webapp1.*
1. V části Adresa `http://localhost:6420`URL **odpovědi**přidejte .
1. Vyberte **Uložit**.
1. Na stránce vlastností zaznamenejte **ID aplikace**. ID aplikace se použije v pozdějším kroku při aktualizaci kódu ve webové aplikaci s jednou stránkou.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (náhled),** vyberte kartu **Vlastní aplikace** a pak vyberte aplikaci *webapp1.*
1. Vyberte **Ověřování**, pak vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V části **Web**vyberte odkaz `http://localhost:6420`Přidat identifikátor **URI,** zadejte a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok při aktualizaci kódu ve webové aplikaci s jednou stránkou.

* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, kterou stáhnete z GitHubu pro práci s klientem B2C. Ukázka ukazuje, jak jednostránková aplikace můžete použít Azure AD B2C pro registraci uživatele a přihlášení a volání chráněné webové rozhraní API (povolíte webové rozhraní API v dalším kurzu v řadě).

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali ukázku, aktualizujte kód s názvem klienta Azure AD B2C a ID aplikace, které jste zaznamenali v předchozím kroku.

1. Otevřete soubor *authConfig.js* ve složce *JavaScriptSPA.*
1. V `msalConfig` objektu aktualizujte:
    * `clientId`s hodnotou S **ID Aplikace (klienta),** které jste zaznamenali v předchozím kroku
    * `authority`Identifikátor URI s názvem klienta Azure AD B2C a názvem toku uživatelů registrace a přihlášení, který jste vytvořili jako součást předpokladů (například *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete okno konzoly a změňte na adresář obsahující ukázku. Příklad:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    npm start
    ```

    V okně konzoly se zobrazí číslo portu místně spuštěného serveru Node.js:

    ```console
    Listening on port 6420...
    ```
1. Přejděte `http://localhost:6420` k zobrazení webové aplikace spuštěné v místním počítači.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webový prohlížeč zobrazující jednostránkovou aplikaci spuštěnou místně":::

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

Tato ukázková aplikace podporuje registraci, přihlášení a resetování hesla. V tomto kurzu se zaregistrujete pomocí e-mailové adresy.

1. Vyberte **Přihlásit,** chcete-li zahájit *tok B2C_1_signupsignin1* uživatele, který jste zadali v předchozím kroku.
1. Azure AD B2C představuje přihlašovací stránku, která obsahuje odkaz pro registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Zaregistrovat nyní.**
1. Pracovní postup registrace představuje stránku pro shromažďování a ověřování identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Zaregistrovat stránku zobrazenou tokem uživatelů Azure AD B2C":::

1. Vyberte **Vytvořit,** chcete-li vytvořit místní účet v adresáři Azure AD B2C.

Když vyberete **Vytvořit**, aplikace zobrazí jméno přihlášeného uživatele.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webový prohlížeč zobrazující jednostránkovou aplikaci s přihlášeným uživatelem":::

Pokud chcete přihlásit, vyberte tlačítko **Odhlásit** se, pak vyberte **Přihlásit** se a přihlásit se pomocí e-mailové adresy a hesla, které jste zadali při registraci.

### <a name="what-about-calling-the-api"></a>A co volání API?

Pokud po přihlášení vyberete tlačítko **Rozhraní API pro volání,** zobrazí se místo výsledků volání rozhraní API stránka toku pro přihlášení nebo přihlášení uživatele. To se očekává, protože jste ještě nenakonfigurovali část rozhraní API aplikace pro komunikaci s aplikací webového rozhraní API zaregistrovanou *v* tenantovi Azure AD B2C.

V tomto okamžiku aplikace se stále pokouší komunikovat s rozhraním API registrovaným v ukázkovém tenantovi (fabrikamb2c.onmicrosoft.com) a protože nejste ověřeni s tímto tenantem, zobrazí se stránka registrace/přihlášení.

Přejděte k dalšímu kurzu v řadě a povolte chráněné rozhraní API (viz část [Další kroky).](#next-steps)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali jednostránkovou aplikaci pro práci s tokem uživatele v tenantovi Azure AD B2C, který poskytuje možnost registrace a přihlášení. Dokončili jste tyto kroky:

> [!div class="checklist"]
> * Přidána adresa URL odpovědi do aplikace registrované ve vašem tenantovi Azure AD B2C
> * Stažena ukázka kódu z GitHubu
> * Upraven kód ukázkové aplikace pro práci s tenantem
> * Přihlášení pomocí toku uživatelů registrace nebo přihlášení

Nyní přejít na další kurz v sérii udělit přístup k chráněné webové api z SPA:

> [!div class="nextstepaction"]
> [Kurz: Ochrana a udělení přístupu k webovému rozhraní API z jednostránkové aplikace >](tutorial-single-page-app-webapi.md)
