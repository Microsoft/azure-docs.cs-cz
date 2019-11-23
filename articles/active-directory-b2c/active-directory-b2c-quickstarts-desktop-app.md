---
title: Quickstart - Set up sign-in for a desktop app using Azure Active Directory B2C
description: In this Quickstart, run a sample WPF desktop application that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed92605c7fb74186ddde6ff193a1365146494594
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420221"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Rychlý start – Nastavení přihlášení pro desktopovou aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete desktopovou aplikaci WPF (Windows Presentation Foundation) k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from either Facebook, Google, or Microsoft.
- [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Spuštění aplikace v sadě Visual Studio

1. V sadě Visual Studio otevřete řešení **active-directory-b2c-wpf.sln** ze složky projektu ukázkové aplikace.
2. Stisknutím klávesy **F5** spusťte ladění aplikace.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Sign in** (Přihlásit se) spusťte pracovní postup **Registrace nebo přihlášení**.

    ![Screenshot of the sample WPF application](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    The sample supports several sign-up options. These options include using a social identity provider or creating a local account using an email address. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.


2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Sign In or Sign Up page showing identity providers](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

2. Dokončete proces přihlašování příslušného zprostředkovatele identity.

    Podrobnosti o vašem novém profilu účtu se předvyplní informacemi z vašeho účtu na sociální síti.

## <a name="edit-your-profile"></a>Úprava profilu

Azure AD B2C poskytuje funkci, která uživatelům umožňuje aktualizovat své profily. The sample web app uses an Azure AD B2C edit profile user flow for the workflow.

1. Na řádku nabídek aplikace klikněte na **Edit profile** (Upravit profil), abyste mohli upravit svůj vytvořený profil.

    ![Edit profile button highlighted in WPF sample app](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Zvolte zprostředkovatele identity přidruženého k účtu, který jste vytvořili. For example, if you used Facebook as the identity provider when you created your account, choose Facebook to modify the associated profile details.

3. Změňte **Display name** (Zobrazované jméno) nebo **City** (Město) a pak klikněte na **Continue** (Pokračovat).

    V textovém poli *Token info* (Informace o tokenu) se zobrazí nový přístupový token. Pokud chcete ověřit změny profilu, zkopírujte přístupový token a vložte ho do dekodéru tokenů na adrese https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Kliknutím na **Call API** (Zavolat rozhraní API) vytvořte žádost na chráněný prostředek.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Úspěšně jste pomocí svého uživatelského účtu Azure AD B2C provedli autorizované zavolání webového rozhraní API chráněného službou Azure AD B2C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

In this quickstart, you used a sample desktop application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
