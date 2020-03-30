---
title: 'Úvodní příručka: Nastavení přihlášení pro ASP.NET webovou aplikaci'
titleSuffix: Azure AD B2C
description: V tomto rychlém startu spusťte ukázkovou ASP.NET webovou aplikaci, která k přihlášení k účtu používá službu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 197155650bf7b8d113f96dbcfd6d9e2b58924b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187198"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Úvodní příručka: Nastavení přihlášení pro ASP.NET aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje správu cloudových identit, aby vaše aplikace, firmy a zákazníci byli chráněni. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete aplikaci ASP.NET k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) s **ASP.NET a zatížení mno žahou webu.**
- Účet na sociální chodu od Facebooku, Googlu nebo Microsoftu.
- [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Existují dva projekty jsou v řešení ukázky:

    - **TaskWebApp** – Webová aplikace pro vytvoření a úpravy seznamu úkolů. Webová aplikace používá **tok registrace nebo přihlášení** uživatele k registraci nebo přihlášení uživatelů.
    - **TaskService** – Webové rozhraní API, které podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Toto webové rozhraní API je chráněné službou Azure AD B2C a volané webovou aplikací.

## <a name="run-the-application-in-visual-studio"></a>Spuštění aplikace v sadě Visual Studio

1. V sadě Visual Studio otevřete řešení **B2C-WebAPI-DotNet.sln** ze složky projektu ukázkové aplikace.
2. Pro účely tohoto rychlého startu spustíte oba projekty **TaskWebApp** a **TaskService** najednou. V Průzkumníku řešení klikněte pravým tlačítkem na řešení **B2C-WebAPI-DotNet** a vyberte **Nastavit projekty po spuštění**.
3. Vyberte **Více projektů po spuštění** a u obou projektů změňte **akci** na **Spuštění**.
4. Klikněte na tlačítko **OK**.
5. Stisknutím klávesy **F5** spusťte ladění obou aplikací. Obě aplikace se otevřou na samostatné kartě prohlížeče:

    - `https://localhost:44316/` – Webová aplikace ASP.NET. V tomto rychlém startu pracujete přímo s touto aplikací.
    - `https://localhost:44332/` – Webové rozhraní API volané webovou aplikací ASP.NET.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Sign up / Sign in** (Registrace / Přihlášení) ve webové aplikaci ASP.NET spusťte pracovní postup.

    ![Ukázka ASP.NET webové aplikace v prohlížeči se zvýrazněným odkazem na registraci/přihlášení](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    Ukázka podporuje několik možností registrace, včetně použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. Pro tento rychlý start použijte účet poskytovatele sociální identity od Facebooku, Googlu nebo Microsoftu.

2. Azure AD B2C představuje přihlašovací stránku pro fiktivní společnost s názvem Fabrikam pro ukázkovou webovou aplikaci. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Přihlašovací nebo zaregistrovat stránku zobrazující tlačítka zprostředkovatele identity](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    Ověřujete (přihlašujete) pomocí přihlašovacích údajů k účtu na sociální síti a opravňujete aplikaci ke čtení informací z vašeho účtu na sociální síti. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

3. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="edit-your-profile"></a>Úprava profilu

Azure Active Directory B2C poskytuje funkci, která uživatelům umožňuje aktualizovat své profily. Ukázková webová aplikace používá tok uživatele profilu úprav Azure AD B2C pro pracovní postup.

1. Na řádku nabídek aplikace klikněte na název vašeho profilu a vyberte **Edit profile** (Upravit profil), abyste mohli upravit svůj vytvořený profil.

    ![Ukázková webová aplikace v prohlížeči se zvýrazněným odkazem Upravit profil](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Změňte **Display name** (Zobrazované jméno) nebo **City** (Město) a pak kliknutím na **Continue** (Pokračovat) svůj profil aktualizujte.

    Změny se zobrazí v pravé horní části domovské stránky webové aplikace.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

1. Klikněte na **To-Do List** (Seznam úkolů), abyste mohli zadat a upravit položky vašeho seznamu úkolů.

2. Zadejte text do textového pole **New Item** (Nová položka). Klikněte na **Add** (Přidat). Zavolá se webové rozhraní API chráněné službou Azure AD B2C, které přidá položku seznamu úkolů.

    ![Ukázka webové aplikace v prohlížeči s položkou Přidat seznam úkolů](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    Webová aplikace ASP.NET do požadavku na chráněný prostředek webového rozhraní API, aby provedl operace s položkami seznamu úkolů uživatele, zahrne přístupový token Azure AD.

Úspěšně jste pomocí svého uživatelského účtu Azure AD B2C provedli autorizované zavolání webového rozhraní API chráněného službou Azure AD B2C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili ukázkovou aplikaci ASP.NET k:

* Přihlášení pomocí vlastní přihlašovací stránky
* Přihlášení u poskytovatele sociální identity
* Vytvoření účtu Azure AD B2C
* Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
