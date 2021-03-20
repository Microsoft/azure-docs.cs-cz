---
title: 'Rychlý Start: nastavení přihlášení pro desktopovou aplikaci'
titleSuffix: Azure AD B2C
description: V tomto rychlém startu spusťte ukázkovou desktopovou aplikaci WPF, která používá Azure Active Directory B2C k poskytnutí přihlášení k účtu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78187284"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Rychlý start – Nastavení přihlášení pro desktopovou aplikaci pomocí služby Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) poskytuje cloudovou správu identit, která zajistí ochranu vašich aplikací, obchodních i zákazníků. Azure AD B2C umožňuje aplikacím provádět ověřování účtů na sociálních sítích a podnikových účtů s využitím protokolů s otevřenými standardy. V tomto rychlém startu použijete desktopovou aplikaci WPF (Windows Presentation Foundation) k přihlášení pomocí zprostředkovatele sociální identity a zavolání webového rozhraní API chráněného službou Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) s úlohou **vývoje ASP.NET a webu** .
- Účet sociální sítě z Facebooku, Google nebo Microsoft.
- [Stáhněte soubor zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) nebo naklonujte úložiště [Azure-Samples/Active-Directory-B2C-dotnet-Desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) z GitHubu.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Spuštění aplikace v sadě Visual Studio

1. V sadě Visual Studio otevřete řešení **active-directory-b2c-wpf.sln** ze složky projektu ukázkové aplikace.
2. Stisknutím klávesy **F5** spusťte ladění aplikace.

## <a name="sign-in-using-your-account"></a>Přihlášení pomocí vašeho účtu

1. Kliknutím na **Sign in** (Přihlásit se) spusťte pracovní postup **Registrace nebo přihlášení**.

    ![Snímek obrazovky ukázkové aplikace WPF](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Ukázka podporuje několik možností registrace. Mezi tyto možnosti patří použití zprostředkovatele sociální identity nebo vytvoření místního účtu pomocí e-mailové adresy. Pro tento rychlý Start použijte účet poskytovatele sociální identity z Facebooku, Google nebo Microsoftu.


2. Azure AD B2C zobrazí přihlašovací stránku pro fiktivní společnost nazvanou Fabrikam pro ukázkovou webovou aplikaci. Pokud se chcete přihlásit pomocí zprostředkovatele sociální identity, klikněte na tlačítko zprostředkovatele sociální identity, kterého chcete použít.

    ![Stránka pro přihlášení nebo přihlášení zobrazující zprostředkovatele identity](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Ověříte (přihlásíte) pomocí přihlašovacích údajů účtu sociální sítě a autorizujete aplikaci, aby četla informace z účtu sociální sítě. Díky udělení přístupu může aplikace z účtu na sociální síti načíst informace o profilu, jako je vaše jméno a město.

2. Dokončete proces přihlašování příslušného zprostředkovatele identity.

    Podrobnosti o vašem novém profilu účtu se předvyplní informacemi z vašeho účtu na sociální síti.

## <a name="edit-your-profile"></a>Úprava profilu

Azure AD B2C poskytuje funkci, která uživatelům umožňuje aktualizovat své profily. Ukázková webová aplikace používá pro pracovní postup profil uživatele Azure AD B2Cho úpravy profilu.

1. Na řádku nabídek aplikace klikněte na **Edit profile** (Upravit profil), abyste mohli upravit svůj vytvořený profil.

    ![Tlačítko Upravit profil zvýrazněné v ukázkové aplikaci WPF](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Zvolte zprostředkovatele identity přidruženého k účtu, který jste vytvořili. Pokud jste například při vytváření účtu použili Facebook jako poskytovatele identity, klikněte na Facebook a upravte přidružené podrobnosti o profilu.

3. Změňte **Display name** (Zobrazované jméno) nebo **City** (Město) a pak klikněte na **Continue** (Pokračovat).

    V textovém poli *Token info* (Informace o tokenu) se zobrazí nový přístupový token. Pokud chcete ověřit změny profilu, zkopírujte přístupový token a vložte ho do dekodéru tokenů na adrese https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Přístup k chráněnému prostředku rozhraní API

Kliknutím na **Call API** (Zavolat rozhraní API) vytvořte žádost na chráněný prostředek.

![Volání rozhraní API](./media/quickstart-native-app-desktop/call-api-wpf.png)

Aplikace do požadavku na chráněný prostředek webového rozhraní API zahrne přístupový token Azure AD. Webové rozhraní API odešle zpět zobrazované jméno obsažené v přístupovém tokenu.

Úspěšně jste pomocí svého uživatelského účtu Azure AD B2C provedli autorizované zavolání webového rozhraní API chráněného službou Azure AD B2C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů nebo rychlých startů pro Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili ukázkovou desktopovou aplikaci k těmto akcím:

* Přihlaste se pomocí vlastní přihlašovací stránky.
* Přihlášení pomocí zprostředkovatele sociální identity
* Vytvoření účtu Azure AD B2C
* Volání webového rozhraní API chráněného Azure AD B2C

Začněte s vytvářením vlastního tenanta Azure AD B2C.

> [!div class="nextstepaction"]
> [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)
