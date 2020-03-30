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
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5d4319f47530a91bcceb9b2dba94c6aa8e4c388
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78183886"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý start: Nastavení přihlášení pro jednostránkovou aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje správu cloudových identit, aby vaše aplikace, firmy a zákazníci byli chráněni. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) s **ASP.NET a zatížením vývoje webu**
- [Node.js](https://nodejs.org/en/download/)
- Sociální účet od Facebooku, Googlu nebo Microsoftu
- Ukázka kódu z GitHubu: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Zde [si](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) můžete stáhnout zip archiv nebo klon ovat úložiště:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Spuštění aplikace

1. Spuštěním následujících příkazů na příkazovém řádku Node.js spusťte server:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js vrátí číslo portu, na kterém naslouchá na místním hostiteli.

    ```
    Listening on port 6420...
    ```

2. Přejděte na adresu URL aplikace. Například, `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Login** (Přihlášení) spusťte pracovní postup.

    ![Ukázková aplikace s jednou stránkou zobrazená v prohlížeči](./media/quickstart-single-page-app/sample-app-spa.png)

    Ukázka podporuje několik možností registrace, včetně použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. Pro tento rychlý start použijte účet poskytovatele sociální identity od Facebooku, Googlu nebo Microsoftu.

2. Azure AD B2C představuje přihlašovací stránku pro fiktivní společnost s názvem Fabrikam pro ukázkovou webovou aplikaci. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Přihlašovací nebo zaregistrovat stránku zobrazující tlačítka zprostředkovatele identity](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Ověřujete (přihlašujete) pomocí přihlašovacích údajů k účtu na sociální síti a opravňujete aplikaci ke čtení informací z vašeho účtu na sociální síti. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

3. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Klikněte na **Call Web API** (Zavolat webové rozhraní API), aby se ze zavolání webového rozhraní API vrátilo vaše zobrazované jméno jako objekt JSON.

![Ukázková aplikace v prohlížeči zobrazující odpověď webového rozhraní API](./media/quickstart-single-page-app/call-api-spa.png)

Ukázková jednostránková aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili ukázkovou jednostránkovou aplikaci k:

* Přihlášení pomocí vlastní přihlašovací stránky
* Přihlášení u poskytovatele sociální identity
* Vytvoření účtu Azure AD B2C
* Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
