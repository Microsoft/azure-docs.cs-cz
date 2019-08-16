---
title: Kurz – povolení ověřování v jedné stránce aplikace – Azure Active Directory B2C
description: Naučte se používat Azure Active Directory B2C k poskytnutí přihlašovacích údajů uživatele pro aplikaci s jednou stránkou (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8683169e0f8464cdcf52600968f67f3622e61445
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509589"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování v jednostránkové aplikaci pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) B2C přihlašovat a registrovat uživatele v jednostránkové aplikaci (SPA). Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v krocích v tomto kurzu, budete potřebovat následující Azure AD B2C prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace](tutorial-register-applications.md) zaregistrovaná ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

V místním vývojovém prostředí budete navíc potřebovat následující:

* Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/) nebo [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download) nebo novější
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

V druhém kurzu, který jste dokončili v rámci požadavků, jste zaregistrovali webovou aplikaci v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, musíte do aplikace v Azure AD B2C přidat identifikátor URI přesměrování.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete **adresář a** filtr předplatného a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte aplikaci *WebApp1* .
1. V části **Adresa URL odpovědi**přidejte `http://localhost:6420`.
1. Vyberte **Uložit**.
1. Na stránce vlastnosti si poznamenejte **ID aplikace**. ID aplikace použijete v pozdějším kroku, když aktualizujete kód ve webové aplikaci s jednou stránkou.

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, který stáhnete z GitHubu. Ukázka ukazuje, jak může jednostránková aplikace používat Azure AD B2C pro registraci a přihlašování uživatelů a volání chráněného webového rozhraní API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali ukázku, aktualizujte kód pomocí Azure AD B2C název tenanta a ID aplikace, které jste si poznamenali v předchozím kroku.

1. `index.html` Otevřete soubor v kořenovém adresáři ukázkového adresáře.
1. V definici upravte hodnotu ClientID pomocí ID aplikace, kterou jste si poznamenali v předchozím kroku. `msalConfig` Dále aktualizujte hodnotu identifikátoru URI autority pomocí vašeho názvu Azure AD B2C tenanta. Aktualizujte také identifikátor URI s názvem uživatelského toku pro registraci a přihlašování, který jste vytvořili v některém z požadavků (například *B2C_1_signupsignin1*).

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

    Název toku uživatele použitý v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, zadejte tento název do `authority` hodnoty.

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

1. Chcete-li zobrazit aplikaci, otevřete vprohlížeči.`http://localhost:6420`

Ukázka podporuje registraci, přihlašování, úpravy profilu a resetování hesla. Tento kurz se věnuje tomu, jak se uživatel zaregistruje pomocí e-mailové adresy.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Výběrem **přihlašovacího jména** zahajte uživatelský tok *B2C_1_signupsignin1* , který jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Registrovat nyní** .
1. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka prezentovaná uživatelským tokem přihlášení/přihlášení](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Vyberte **vytvořit** k vytvoření místního účtu v adresáři Azure AD B2C.

Když vyberete **vytvořit**, stránka registrace se zavře a znovu se zobrazí přihlašovací stránka.

Pro přihlášení k aplikaci teď můžete použít svou e-mailovou adresu a heslo.

### <a name="error-insufficient-permissions"></a>Chyba: nedostatečná oprávnění

Po přihlášení se v aplikaci zobrazí chyba nedostatečné oprávnění – **očekává**se:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Tato chyba se zobrazí, protože webová aplikace se pokouší o přístup k webovému rozhraní API chráněnému adresářem demo, *fabrikamb2c*. Vzhledem k tomu, že váš přístupový token je platný jenom pro váš adresář služby Azure AD, volání rozhraní API je proto neautorizované.

Pokud chcete tuto chybu opravit, pokračujte k dalšímu kurzu v řadě (viz [Další kroky](#next-steps)) a vytvořte pro svůj adresář chráněné webové rozhraní API.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Teď přejděte k dalšímu kurzu v řadě, abyste udělili přístup k chráněnému webovému rozhraní API z hesla SPA:

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k ASP.NET Core webovému rozhraní API z hesla SPA pomocí Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
