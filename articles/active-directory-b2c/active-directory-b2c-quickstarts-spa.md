---
title: Rychlý Start – Nastavení přihlášení pro jednu stránku aplikace pomocí Azure Active Directory B2C
description: Spusťte ukázkovou jednostránkovou aplikaci využívající Azure Active Directory B2C k zajištění přihlášení k účtu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 18a2063c2ee2ae734e481cf663573ab5543e5edf
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065794"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rychlý start: Nastavení přihlášení pro jednostránkovou aplikaci pomocí Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje cloudovou správu identit, která zajistí ochranu vašich aplikací, obchodních i zákazníků. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete jednostránkovou aplikaci k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) s úlohou **vývoje ASP.NET a webu** .
- Instalovat [Node.js](https://nodejs.org/en/download/)
- Účet sociální sítě z Facebooku, Google nebo Microsoft.
- [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

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

    ![Ukázková aplikace s jednoduchou stránkou zobrazená v prohlížeči](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    Ukázka podporuje několik možností registrace, včetně použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. Pro tento rychlý Start použijte účet poskytovatele sociální identity z Facebooku, Google nebo Microsoftu.

2. Azure AD B2C zobrazí přihlašovací stránku pro fiktivní společnost nazvanou Fabrikam pro ukázkovou webovou aplikaci. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Stránka pro přihlášení nebo přihlášení zobrazující tlačítka poskytovatele identity](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Ověříte (přihlásíte) pomocí přihlašovacích údajů účtu sociální sítě a autorizujete aplikaci, aby četla informace z účtu sociální sítě. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

3. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Klikněte na **Call Web API** (Zavolat webové rozhraní API), aby se ze zavolání webového rozhraní API vrátilo vaše zobrazované jméno jako objekt JSON.

![Ukázková aplikace v prohlížeči znázorňující odpověď webového rozhraní API](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Ukázková jednostránková aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili ukázkovou jednostránkovou aplikaci pro:

* Přihlaste se pomocí vlastní přihlašovací stránky.
* Přihlášení pomocí zprostředkovatele sociální identity
* Vytvoření účtu Azure AD B2C
* Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
