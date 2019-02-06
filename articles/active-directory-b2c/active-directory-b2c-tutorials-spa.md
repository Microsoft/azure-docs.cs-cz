---
title: Kurz – povolení ověřování v jednostránkové aplikaci – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů v jednostránkové aplikaci (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 813c7131ff0a56e843e728cd78fff969b1d90fcc
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756321"
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

* [Vytvořit toky uživatelů](tutorial-create-user-flows.md) umožňující činnosti koncových uživatelů ve vaší aplikaci. 
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadou funkcí **Vývoj pro ASP.NET a web**.
* Nainstalujte [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) nebo novější
* Instalovat [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

V tomto kurzu jste dokončili jako součást požadavků jste přidali webové aplikace v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, budete muset přidat identifikátor URI přesměrování pro aplikaci v Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte *webapp1* aplikace.
5. V části **adresy URL odpovědi**, přidejte `http://localhost:6420`.
6. Vyberte **Uložit**.
7. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.
8. Vyberte **klíče**vyberte **vygenerovat klíč**a vyberte **Uložit**. Záznam klíče, které budete používat při konfiguraci webové aplikace.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

V tomto kurzu nakonfigurujete vzorku, který si můžete stáhnout z Githubu. Ukázka předvádí, jak může jednostránková aplikace pomocí Azure AD B2C registrovat a přihlášení uživatele a volat chráněné webové rozhraní API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Chcete-li změnit nastavení:

1. Otevřít `index.html` soubor v ukázce.
2. Nakonfigurujte ukázku s ID aplikace a klíč, který jste si poznamenali dříve. Změňte následující řádky kódu tak, že nahradíte hodnoty s názvy adresáře a rozhraní API:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Název toku uživatele použité v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiné uživatelské jméno toku, použít název toku uživatele v `authority` hodnotu.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Spusťte následující příkazy:

    ```
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu, na kterém je aplikace hostovaná.
    
    ```
    Listening on port 6420...
    ```

4. Použít prohlížeč a přejděte na adresu `http://localhost:6420` zobrazíte aplikaci.

Ukázka podporuje registraci a přihlašování, úpravy profilu a resetování hesla. Tento kurz ukazuje, jak se uživatel přihlásí pomocí e-mailovou adresu.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **přihlášení** se zaregistrujte jako uživatel aplikace. Tady se používá **B2C_1_signupsignin1** tok uživatele, které jste definovali v předchozím kroku.
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 
3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v adresáři Azure AD B2C.

Uživatel teď můžete použít e-mailovou adresu se přihlásit a používat JEDNOSTRÁNKOVOU aplikaci.

> [!NOTE]
> Po přihlášení aplikace zobrazí chybu Nedostatečná oprávnění. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového adresáře. Vzhledem k tomu, že je váš přístupový token platný jenom pro váš adresář Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svůj adresář vytvoříte chráněné webové rozhraní API.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
