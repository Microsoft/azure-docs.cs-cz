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
ms.openlocfilehash: 6471d1b5a5ad2b8ba34080ae1220872fa0e2e232
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421052"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý Start: nastavení přihlášení pro jednostránkovou aplikaci pomocí Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje cloudovou správu identit, která zajistí ochranu vašich aplikací, obchodních i zákazníků. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Účet sociální sítě z Facebooku, Google nebo Microsoftu
- Ukázka kódu z GitHubu: [MS-identity-B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)

    Můžete [stáhnout archiv zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) nebo klonovat úložiště:

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. Spuštěním následujících příkazů na příkazovém řádku Node.js spusťte server:

    ```console
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
