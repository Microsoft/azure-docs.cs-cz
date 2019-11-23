---
title: Quickstart - Set up sign in for an ASP.NET application using Azure Active Directory B2C
description: In this Quickstart, run a sample ASP.NET web app that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 114e82147b03fb500197a2129b7960101f85a455
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420192"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Quickstart: Set up sign in for an ASP.NET application using Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete aplikaci ASP.NET k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from Facebook, Google, or Microsoft.
- [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    There are two projects are in the sample solution:

    - **TaskWebApp** – Webová aplikace pro vytvoření a úpravy seznamu úkolů. The web application uses the **sign-up or sign-in** user flow to sign up or sign in users.
    - **TaskService** – Webové rozhraní API, které podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Toto webové rozhraní API je chráněné službou Azure AD B2C a volané webovou aplikací.

## <a name="run-the-application-in-visual-studio"></a>Spuštění aplikace v sadě Visual Studio

1. V sadě Visual Studio otevřete řešení **B2C-WebAPI-DotNet.sln** ze složky projektu ukázkové aplikace.
2. Pro účely tohoto rychlého startu spustíte oba projekty **TaskWebApp** a **TaskService** najednou. V Průzkumníku řešení klikněte pravým tlačítkem na řešení **B2C-WebAPI-DotNet** a vyberte **Nastavit projekty po spuštění**.
3. Vyberte **Více projektů po spuštění** a u obou projektů změňte **akci** na **Spuštění**.
4. Klikněte na **OK**.
5. Stisknutím klávesy **F5** spusťte ladění obou aplikací. Obě aplikace se otevřou na samostatné kartě prohlížeče:

    - `https://localhost:44316/` – Webová aplikace ASP.NET. V tomto rychlém startu pracujete přímo s touto aplikací.
    - `https://localhost:44332/` – Webové rozhraní API volané webovou aplikací ASP.NET.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Sign up / Sign in** (Registrace / Přihlášení) ve webové aplikaci ASP.NET spusťte pracovní postup.

    ![Sample ASP.NET web app in browser with sign up/sign link highlighted](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    Ukázka podporuje několik možností registrace, včetně použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.

2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Sign In or Sign Up page showing identity provider buttons](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

3. Dokončete proces přihlašování příslušného zprostředkovatele identity.

## <a name="edit-your-profile"></a>Úprava profilu

Azure Active Directory B2C poskytuje funkci, která uživatelům umožňuje aktualizovat své profily. The sample web app uses an Azure AD B2C edit profile user flow for the workflow.

1. Na řádku nabídek aplikace klikněte na název vašeho profilu a vyberte **Edit profile** (Upravit profil), abyste mohli upravit svůj vytvořený profil.

    ![Sample web app in browser with Edit profile link highlighted](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Změňte **Display name** (Zobrazované jméno) nebo **City** (Město) a pak kliknutím na **Continue** (Pokračovat) svůj profil aktualizujte.

    Změny se zobrazí v pravé horní části domovské stránky webové aplikace.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

1. Klikněte na **To-Do List** (Seznam úkolů), abyste mohli zadat a upravit položky vašeho seznamu úkolů.

2. Zadejte text do textového pole **New Item** (Nová položka). Klikněte na **Add** (Přidat). Zavolá se webové rozhraní API chráněné službou Azure AD B2C, které přidá položku seznamu úkolů.

    ![Sample web app in browser with Add a to-do list item](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    Webová aplikace ASP.NET do požadavku na chráněný prostředek webového rozhraní API, aby provedl operace s položkami seznamu úkolů uživatele, zahrne přístupový token Azure AD.

Úspěšně jste pomocí svého uživatelského účtu Azure AD B2C provedli autorizované zavolání webového rozhraní API chráněného službou Azure AD B2C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

In this quickstart, you used a sample ASP.NET application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
