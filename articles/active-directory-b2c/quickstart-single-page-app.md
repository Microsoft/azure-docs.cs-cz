---
title: 'Rychlý Start: nastavení přihlášení pro jednu stránku aplikace (SPA)'
titleSuffix: Azure AD B2C
description: V tomto rychlém startu spusťte ukázkovou jednostránkovou aplikaci, která používá Azure Active Directory B2C k poskytnutí přihlášení k účtu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db18c71b09cd9beb58799ad4fbcbc944061153fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87921754"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý start: Nastavení přihlášení pro jednostránkovou aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje cloudovou správu identit, která zajistí ochranu vašich aplikací, obchodních i zákazníků. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Účet sociální sítě z Facebooku, Google nebo Microsoftu
- Ukázka kódu z GitHubu: [Active-Directory-B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Můžete [stáhnout archiv zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo klonovat úložiště:

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

    Server spuštěný nástrojem *server.js* zobrazuje port, na kterém naslouchá:

    ```console
    Listening on port 6420...
    ```

1. Přejděte na adresu URL aplikace. Například, `http://localhost:6420`.

    ![Ukázková aplikace s jednoduchou stránkou zobrazená v prohlížeči](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Vyberte **Přihlásit** se a zahajte cestu uživatele.
1. Azure AD B2C zobrazí přihlašovací stránku pro fiktivní společnost nazvanou Fabrikam pro ukázkovou webovou aplikaci. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, vyberte tlačítko poskytovatele identity, kterého chcete použít.

    ![Stránka pro přihlášení nebo přihlášení zobrazující tlačítka poskytovatele identity](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Ověříte (přihlásíte) pomocí přihlašovacích údajů účtu sociální sítě a autorizujete aplikaci, aby četla informace z účtu sociální sítě. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

1. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Vyberte **volání rozhraní API** , aby se vaše zobrazované jméno vrátilo z webového rozhraní API jako objekt JSON.

![Ukázková aplikace v prohlížeči znázorňující odpověď webového rozhraní API](./media/quickstart-single-page-app/call-api-spa.png)

Ukázková jednostránková aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili ukázkovou jednostránkovou aplikaci pro:

- Přihlášení pomocí zprostředkovatele sociální identity
- Vytvořit uživatelský účet Azure AD B2C (automaticky vytvořený při přihlášení)
- Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
