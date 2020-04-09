---
title: 'Úvodní příručka: Nastavení přihlášení pro jednostránkovou aplikaci (SPA)'
titleSuffix: Azure AD B2C
description: V tomto rychlém startu spusťte ukázkovou jednostránkovou aplikaci, která používá Azure Active Directory B2C k přihlášení k účtu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875865"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý start: Nastavení přihlášení pro jednostránkovou aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje správu cloudových identit, aby vaše aplikace, firmy a zákazníci byli chráněni. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Sociální účet od Facebooku, Googlu nebo Microsoftu
- Ukázka kódu z GitHubu: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Zde [si](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) můžete stáhnout zip archiv nebo klon ovat úložiště:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. Spuštěním následujících příkazů na příkazovém řádku Node.js spusťte server:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    Server spuštěný serverem *server.js* zobrazí port, na který naslouchá:

    ```console
    Listening on port 6420...
    ```

1. Přejděte na adresu URL aplikace. Například, `http://localhost:6420`.

    ![Ukázková aplikace s jednou stránkou zobrazená v prohlížeči](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Chcete-li zahájit cestu uživatele, **vyberte Přihlásit** se.
1. Azure AD B2C představuje přihlašovací stránku pro fiktivní společnost s názvem Fabrikam pro ukázkovou webovou aplikaci. Chcete-li se zaregistrovat pomocí poskytovatele sociální identity, vyberte tlačítko poskytovatele identity, kterého chcete použít.

    ![Přihlašovací nebo zaregistrovat stránku zobrazující tlačítka zprostředkovatele identity](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Ověřujete (přihlašujete) pomocí přihlašovacích údajů k účtu na sociální síti a opravňujete aplikaci ke čtení informací z vašeho účtu na sociální síti. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

1. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Vyberte **rozhraní API pro volání,** chcete-li, aby se zobrazované jméno vrátilo z webového rozhraní API jako objekt JSON.

![Ukázková aplikace v prohlížeči zobrazující odpověď webového rozhraní API](./media/quickstart-single-page-app/call-api-spa.png)

Ukázková jednostránková aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili ukázkovou jednostránkovou aplikaci k:

- Přihlášení u poskytovatele sociální identity
- Vytvoření uživatelského účtu Azure AD B2C (vytvořené ho automaticky při přihlášení)
- Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
