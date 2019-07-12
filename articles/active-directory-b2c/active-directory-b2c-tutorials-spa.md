---
title: Kurz – povolení ověřování v jednostránkové aplikaci – Azure Active Directory B2C
description: Další informace o použití Azure Active Directory B2C k zajištění přihlašování uživatelů v jednostránkové aplikaci (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 496cf801a44638af61306b43791abce9466e2cb2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835686"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování v jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak používat Azure Active Directory (Azure AD) B2C k přihlášení registraci a přihlašování uživatelů v jednostránkové aplikaci (SPA). Azure AD B2C umožňuje vaší aplikace k ověřování účtů na sociálních sítích, podnikových účtů a účtů služby Azure Active Directory pomocí protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat s kroky v tomto kurzu potřebujete následující prostředky Azure AD B2C na místě:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikaci registrovanou](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů pro vytvoření](tutorial-create-user-flows.md) ve vašem tenantovi

Kromě toho budete potřebovat následující ve vašem místním vývojovém prostředí:

* Editor, například kód [Visual Studio Code](https://code.visualstudio.com/) nebo [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [Sada .NET core SDK 2.0.0](https://www.microsoft.com/net/core) nebo novější
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

V druhém kurz dokončili jako součást požadavků jste zaregistrovali webové aplikace v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, budete muset přidat identifikátor URI přesměrování pro aplikaci v Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikací**a pak vyberte *webapp1* aplikace.
1. V části **adresy URL odpovědi**, přidejte `http://localhost:6420`.
1. Vyberte **Uložit**.
1. Na stránce Vlastnosti záznamu **ID aplikace**. ID aplikace v pozdějším kroku použít aktualizace kódu jednostránkové webové aplikace.

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete vzorový kód, který můžete stáhnout z webu GitHub. Vzorek ukazuje, jak může jednostránková aplikace pomocí Azure AD B2C pro registraci uživatelů a přihlášení a volat chráněné webové rozhraní API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali vzorku, aktualizujte kód s název tenanta Azure AD B2C a ID aplikace, které jste si poznamenali v dřívějším kroku.

1. Otevřít `index.html` souboru v kořenovém adresáři vzorku.
1. V `msalConfig` definice, upravte **clientId** hodnotu s ID aplikace, které jste si poznamenali v dřívějším kroku. Dále, aktualizujte **autority** hodnotu identifikátoru URI názvem vašeho tenanta Azure AD B2C. Také aktualizujte název toku přihlášení-registrace/přihlášení uživatelů vytvořené v některém z požadovaných součástí identifikátoru URI (například *B2C_1_signupsignin1*).

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

    Název toku uživatele použité v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiné uživatelské jméno toku, zadejte tento název `authority` hodnotu.

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

    V okně konzoly se zobrazí číslo portu serveru místně spuštěné Node.js:

    ```
    Listening on port 6420...
    ```

1. Přejděte na `http://localhost:6420` ve svém prohlížeči aplikaci zobrazit.

Ukázka podporuje registraci a přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje, jak se uživatel přihlásí pomocí e-mailovou adresu.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **přihlášení** se zaregistrujte jako uživatel aplikace. Tady se používá **B2C_1_signupsignin1** tok uživatele, které jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se).
1. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Registrační stránku předložený tok přihlašování – v registrace/přihlášení uživatele](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v adresáři Azure AD B2C.

Po kliknutí na **vytvořit**, na stránku registrace nezavře a znovu zobrazí přihlašovací stránku.

Nyní můžete e-mailovou adresu a heslo pro přihlášení k aplikaci.

### <a name="error-insufficient-permissions"></a>Chyba: Nedostatečná oprávnění

Po přihlášení, aplikace zobrazí chybu nedostatečná oprávnění – to je **očekává**:

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

Tato chyba se zobrazí, protože se pokouší o přístup k prostředku z adresáře ukázku, ale váš přístupový token je platný pouze pro váš adresář Azure AD. Volání rozhraní API je proto neoprávněné.

Pokračovat k dalšímu kurzu v této sérii (viz [další kroky](#next-steps)) k vytvoření chráněného webového rozhraní API pro svůj adresář.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

Teď přejdeme k dalšímu kurzu v této sérii udělit přístup k chráněné webové rozhraní API z tato jednostránková aplikace:

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
