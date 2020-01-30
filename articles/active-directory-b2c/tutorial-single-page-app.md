---
title: 'Kurz: povolení ověřování v jedné stránce aplikace'
titleSuffix: Azure AD B2C
description: V tomto kurzu se naučíte používat Azure Active Directory B2C k poskytnutí přihlašovacích údajů uživatele pro jednostránkové aplikaci založenou na JavaScriptu (SPA).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f66d8e229535346525f117d8ebbfb37b893fe022
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849996"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Kurz: povolení ověřování v aplikaci s jednou stránkou pomocí Azure Active Directory B2C (Azure AD B2C)

V tomto kurzu se dozvíte, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlašování a registraci uživatelů v aplikaci s jednou stránkou (SPA). Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v krocích v tomto kurzu, budete potřebovat následující Azure AD B2C prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace zaregistrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

V místním vývojovém prostředí budete navíc potřebovat následující:

* Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/) nebo [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download) nebo novější
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

V druhém kurzu, který jste dokončili v rámci požadavků, jste zaregistrovali webovou aplikaci v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, musíte do aplikace v Azure AD B2C přidat identifikátor URI přesměrování.

K aktualizaci aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové prostředí Unified **Registrace aplikací (Preview)** . [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte aplikaci *WebApp1* .
1. V části **Adresa URL odpovědi**přidejte `http://localhost:6420`.
1. Vyberte **Uložit**.
1. Na stránce vlastnosti si poznamenejte **ID aplikace**. ID aplikace použijete v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** , vyberte kartu **vlastněné aplikace** a pak vyberte aplikaci *WebApp1* .
1. Vyberte **ověřování**a pak vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **Web**vyberte odkaz **Přidat identifikátor URI** , zadejte `http://localhost:6420`a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, který stáhnete z GitHubu. Ukázka ukazuje, jak může jednostránková aplikace používat Azure AD B2C pro registraci a přihlašování uživatelů a volání chráněného webového rozhraní API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali ukázku, aktualizujte kód pomocí Azure AD B2C název tenanta a ID aplikace, které jste si poznamenali v předchozím kroku.

1. Otevřete soubor `index.html` v kořenovém adresáři ukázkového adresáře.
1. V definici `msalConfig` upravte hodnotu **ClientID** pomocí ID aplikace, kterou jste si poznamenali v předchozím kroku. Dále aktualizujte hodnotu identifikátoru URI **autority** pomocí vašeho názvu Azure AD B2C tenanta. Aktualizujte také identifikátor URI s názvem uživatelského toku pro registraci a přihlašování, který jste vytvořili v jednom z požadovaných součástí (například *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Název toku uživatele použitý v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, zadejte tento název do hodnoty `authority`.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete okno konzoly a přejděte do adresáře obsahujícího ukázku. Příklad:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Spusťte následující příkazy:

    ```
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu místně běžícího serveru Node. js:

    ```
    Listening on port 6420...
    ```

1. Chcete-li zobrazit aplikaci, v prohlížeči přejdete na `http://localhost:6420`.

Ukázka podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz se věnuje tomu, jak se uživatel zaregistruje pomocí e-mailové adresy.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

> [!WARNING]
> Po registraci nebo přihlášení se může zobrazit [Chyba nedostatečného oprávnění](#error-insufficient-permissions). Z důvodu aktuální implementace ukázky kódu je tato chyba očekávána. Tento problém bude vyřešen v budoucí verzi ukázky kódu, ve které bude toto upozornění odebráno.

1. Výběrem **přihlašovacího jména** zahajte *B2C_1_signupsignin1* uživatelský tok, který jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Registrovat nyní** .
1. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka prezentovaná uživatelským tokem přihlášení/přihlášení](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Vyberte **vytvořit** k vytvoření místního účtu v adresáři Azure AD B2C.

Když vyberete **vytvořit**, stránka registrace se zavře a znovu se zobrazí přihlašovací stránka.

Pro přihlášení k aplikaci teď můžete použít svou e-mailovou adresu a heslo.

### <a name="error-insufficient-permissions"></a>Chyba: nedostatečná oprávnění

Po přihlášení může aplikace vrátit nedostatečné oprávnění:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Tato chyba se zobrazí, protože webová aplikace se pokouší o přístup k webovému rozhraní API chráněnému adresářem demo, *fabrikamb2c*. Vzhledem k tomu, že váš přístupový token je platný jenom pro váš adresář služby Azure AD, volání rozhraní API je neautorizované.

Pokud chcete tuto chybu opravit, pokračujte k dalšímu kurzu v řadě (viz [Další kroky](#next-steps)) a vytvořte pro svůj adresář chráněné webové rozhraní API.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Teď přejděte k dalšímu kurzu v řadě, abyste udělili přístup k chráněnému webovému rozhraní API z hesla SPA:

> [!div class="nextstepaction"]
> [Kurz: poskytnutí přístupu k ASP.NET Core webovému rozhraní API z hesla SPA pomocí Azure AD B2C >](tutorial-single-page-app-webapi.md)
